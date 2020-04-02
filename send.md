# API guide: Send

## Purpose

The user wants to transfer money from his own account to another user (recipient).

## Precondition:

- user and recipient are registered in T-Wallet
- recipient's registration phone number is known to the user
- user is authenticated (JWT token is issued and not expired)

## API usage

Base URL `https://{host}/api/v1/wallet/{appID}/`

JWT has to be sent in the header "X-Authorization" of each request.

Test values:
- host = stage-wallet-api.rock-west.net
- appID = dabac956-ba14-49e5-bc57-5a312a1f477c

## Basic scenario

### 1 Account number

User gets his account number with method `GET /profile/`.

> sender_account_number = `Response.account_id`

### 2 Available assets

User collects the list of available assets and balances for his account
with the method `GET /profile/account/<sender_account_number>`.

- The list of available assets: `Response.meta_data.assets[]`
- Balance of each asset: `Response.balances.{asset_key}.balance`

### 3 Find contact

The user searches a new recipient by phone number with the method `GET /contact?find=phone:{phone_number}`.

The phone_number supports digits only. The "+" symbol and "00" are not expected (at the string beginning).

> recipient_profile_id = `Response.profile_id`

##### Error `403 forbidden`

JWT is invalid.

> Response.Body = "_find request requires user authentication_".

### 4 Send money

The user sends money with the method `POST /profile/send-to-profile`.

Request body:

    {
     "from_account_id": <sender_account_number>,
     "to_profile_id": <recipient_profile_id>,
     "asset_key": <asset_key>,
     "amount": <decimal value not greater than asset balance>,
     "meta_data": {
         "notes": <optional comment for the transfer>
         }
    }

###### Responses

1. `HTTP400: record not found`
  - a \<sender_account_number>  is not correct for the sender
  - the requested asset is not available for the sender

2. `HTTP200: status OK`
 - Response.Body = `{code: 200, msg: []}`: the transaction is being processed
 - Response.Body = `{"code":601,"msg":["op_underfunded"]}`: insufficient balance for the requested asset.

### 5 Transaction status

User checks the status with the method `GET /profile/payment_transaction?type=1`. The response records are sorted from newest to oldest.

##### Response record format:

    {
         "payment_id": "3d0179ff-5fda-4cdc-bba4-81a2d4066868",
         "asset_from": "XLM",
         "asset_to": "XLM",
         "from_user": {
             "profile_id": "abc7be61-3c85-4e27-8e06-4ff7f733d4de",
             "first_name": "sender name",
             "last_name": "sender surname"
         },
         "to_user": {
             "profile_id": "abc7be61-3c85-4e27-8e06-4ff7f733d4de",
             "first_name": "recipient name",
             "last_name": "recipient surname"
         },
         "amount": 0.01,
         "type": 1,
         "state": 8,
         "created": 1585863295,
         "updated": 1585863302,
         "meta_data": {
             "notes": "test"
         }
     }

##### Transaction Types & States
    0x0  | Receive        0x0 | Created
    0x1  | Send           0x1 | Pending
    0x2  | Swap           0x2 | Rejected
    0x10 | Deposit        0x4 | Failed
    0x20 | Withdraw       0x8 | Done

---
## Alternative scenarios

### 3.1 Save contact

The user stores the found contact with `profile_id`, `first_name` and `last_name`.

- `POST /profile/contact`. 

You may use `first_name` and `last_name` from Response of `GET /contact/find`.

### 3.2 Choose contact

User finds the list of previously stored contacts.

- `GET /profile/contact`.

> recipient_profile_id = `Response.data.[<profile_id>]`

### 3.3 Delete contact

User deletes a previously stored contact.

- `DEL /profile/contact/<recipient_profile_id>`

---
## Common Errors

#### `498 status code 498`
Response.Body = "Token has expired"

JWT is invalid.

#### `401 Unauthorized`

X-Authorization header is blank or missing.
