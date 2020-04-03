# Common information

## API usage

Base URL `https://<host>/api/v1/wallet/<appID>/`

JWT has to be sent in the header "X-Authorization" of each request.

Test values:
- host = stage-wallet-api.rock-west.net
- appID = dabac956-ba14-49e5-bc57-5a312a1f477c

## JWT contents 

    {
      "exp": 1585947190,
      "guid": "abc7be61-3c85-4e27-8e06-4ff7f733d4de",
      "system_session_token": "ebbee0675601bfbab8eb2d9448b71a31d652e3cc3b8a694474f48bbdcc93175"
    }
    
- exp - expiry in Unix epoch timestamp (seconds) 
- guid - profile ID 
- system_session_token - ID of authentication session
