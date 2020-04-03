# API guide: Transaction list

## Purpose

User reads all the payments information.  

- `GET /profile/payment_transaction?type=1`.

## Request parameters

- `from`: end of the time period for "updated", Unix epoch time (seconds), default = now() + 1 minute
- `to`: end of the time period for "updated", Unix epoch time (seconds), default = Unix epoch start
- `asset` (see the table below)
- `type` (see the table below)
- `offset`: offset for pagination, default = uint(0)
- `limit`: limit for pagination, default = uint(1000)

## Response format

The results object contains:
- list of payments sorted by "updated" timestamp in descending order
- pagination block

### List item (payment)

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

### Payment attributes

- `payment_id`: unique payment ID
- `asset_from` & `asset_to`: the asset (= currency) for debit & credit
- `from_user` & `to_user`: blocks for sender and recipient
  - `profile_id`: counterparty profile ID (equals to response in `GET /contact?find`), can be used for a new payment request (e.g. [send](send.md#4-send-money))
  - `first_name`: name of the counterparty
  - `last_name`: surname of the counterparty
- `amount`: credit amount (decimal), corresponds to `asset_to`
- `type`: payment type (see the table below)
- `state`: payment status (see the table below)
- `created`: payment creation timestamp, Unix time (seconds)
- `updated`: payment last update timestamp, Unix time (seconds)
- `meta_data.notes`: extra information provided by the counterparty

### Pagination block

    "pagination": {
        "limit": 1000,
        "offset": 0,
        "total": 18
    }

## Transaction Types & States
    0x0  | Receive        0x0 | Created
    0x1  | Send           0x1 | Pending
    0x2  | Swap           0x2 | Rejected
    0x10 | Deposit        0x4 | Failed
    0x20 | Withdraw       0x8 | Done
