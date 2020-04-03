`GET /profile/payment_transaction?type=1`.

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
