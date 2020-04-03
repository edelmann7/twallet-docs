# API guide: Send

## Purpose

The user wants to transfer money from his own account to another user (recipient).

## Precondition:

- user and recipient are registered in T-Wallet
- recipient's registration phone number is known to the user
- user is authenticated (the token is not expired)

See also: ["API usage"](common.md)

## Basic scenario

### 1 Account number

User gets his account number.

- `GET /profile/`.

> sender_account_number = `Response.account_id`

### 2 Available assets

User collects the list of available assets and balances for his account.

- `GET /profile/account/<sender_account_number>`.

- The list of available assets: `Response.meta_data.assets[]`
- Balance of each asset: `Response.balances.<asset_key>.balance`

### 3 Find contact

The user searches a new recipient by phone number. The \<phone_number> supports digits only. The "+" symbol and "00" are not expected at the string beginning.

- `GET /contact?find=phone:<phone_number>`.

> recipient_profile_id = `Response.profile_id`

#### Error `403 forbidden`

JWT is invalid.

> Response.Body = "_find request requires user authentication_".

### 4 Send money

The user sends money.

- `POST /profile/send-to-profile`.

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

1. `HTTP200: status OK`
 - Response.Body = `{code: 200, msg: []}`: the transaction is being processed
 - Response.Body = `{"code":601,"msg":["op_underfunded"]}`: insufficient balance for the requested asset.
 
2. `HTTP400: record not found`
  - \<sender_account_number>  is not correct for the sender
  - the requested asset is not available for the sender

### 5 Transaction status

User checks the status.

- `GET /profile/payment_transaction?type=1`.

See more details: [Transaction list](payment_transaction.md)

---
## Alternative scenarios

### 3.1 Save contact

The user stores the found contact with `profile_id`, `first_name` and `last_name`. You may use `first_name` and `last_name` from Response of `GET /contact/find`.

- `POST /profile/contact`

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
