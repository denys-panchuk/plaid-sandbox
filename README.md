# plaid-sandbox
## https://plaid.com/docs/api

Credential (client_id, secret) you can find in Plaid cabinet.
![Cabinet screen](/img/cabinet_screen.png)


### 1. Create plaid user:
```
curl --location --request POST 'https://sandbox.plaid.com/user/create' \
--header 'Content-Type: application/json' \
--data '{
    "client_id": "",
    "secret": "",
    "client_user_id": "1"
}'
```
<sub>client_user_id - is external user id, at now it`s not important</sub>

In response you need `user_token` for next steps.
```
{
    "request_id": "rWr8CS7uFuK8prm",
    "user_id": "af148e68ed358df675b9ab4305cdf6e299dc9426436ad84abf0767440c258870",
    "user_token": "user-sandbox-f37c8752-548b-4eaf-9229-8474e37870fb"
}
```

### 2. Create link-token so that create authorize link for client:
```
curl --location --request POST 'https://sandbox.plaid.com//link/token/create' \
--header 'Content-Type: application/json' \
--data '{
    "client_id": "",
    "secret": "",
    "user_token": "user-sandbox-f37c8752-548b-4eaf-9229-8474e37870fb",
    "user": {
        "client_user_id": "c0e2c4ee-b763-4af5-cfe9-46a46bce883d"
    },
    "client_name": "Personal Finance App",
    "products": [
        "income_verification",
        "transactions",
        "assets",
        "transfer"
    ],
    "country_codes": [
        "US"
    ],
    "language": "en",
    "income_verification": {
        "income_source_types": [
            "bank"
        ],
        "bank_income": {
            "days_requested": 365
        }
    }
}'

```
In response need `link_token`
```
{
    "expiration": "2023-12-08T14:27:27Z",
    "link_token": "link-sandbox-b7442c4a-7e6b-4aa6-a25c-1b790dee05f1",
    "request_id": "UoKMAn7xTWSBMIQ"
}
```

### 3. To connect your bank account necessary compleate auth flow.
- Edit index.html. Isert `link_token` into row 45 (set `token` property).
- Open index.html in browser. And press <b>Link accoun</b> button. 
- On successuf authorization account will apear allert message with `public_token` - store, it will be need for transaction feature.

### 4. Get bank income. 
```
curl --location --request POST 'https://sandbox.plaid.com/credit/bank_income/get' \
--header 'Content-Type: application/json' \
--data '{
    "client_id": "",
    "secret": "",
    "user_token": ""
}'
```
<sub>`user_token` - from step 1.</sub>

### 5. Get transactions
 1. Get access token. From response store `access_token`.
 ```
 curl --location --request POST 'https://sandbox.plaid.com/item/public_token/exchange' \
--header 'Content-Type: application/json' \
--data '{
    "client_id": "",
    "secret": "",
    "public_token": ""
}
 ```
 <sub>`public_token` - from step 3.</sub>

 2. Get transactions
 ```
 curl --location --request POST 'https://sandbox.plaid.com/transactions/sync' \
--header 'Content-Type: application/json' \
--data '{
    "client_id": "",
    "secret": "",
    "access_token": ""
}'
 ```
