# Partner QP Integration
 

## Introduction
Partner is expected to provide a service URL for QP operations, the service url is expected to provide the following capabilities: authenticate user, return user details (id, balance, username), transact (create/enter contest, cancel, win, rollback).

## Request Overview 
The following are true for all requests made to the partner service.
* Request is in standard JSON format
* Requests uses the standard HTTP POST method.
* Request are signed based on the payload contained in the request, which the Partner is expected to verify with the secure key provided.
* Every request to the partner wallet service is expected return the following response structure.

Every request contains the following fields.

## Request
| Field | Type | Mandatory | Description |
| ----- | ----- | --------- | ----------- |
| x-quickplay-signature | String | Yes | QP public key |
| token | String | Yes | The token for the request |
 
Every request must return a successful response with http code 200 according to the structure described below :

## Success Response
 
| Field | Type | Mandatory | Description |
| ----- | ---- | --------- | -------------|
| data | JSON Object | Yes | Successful response envelope |
| id | String | Yes | User ID | 
| balance | Number | Yes | User balance |
| username | String | Yes | User name |

example below

```
{
  "data": {
    "id": "555",
    "balance": 100,
    "username": "Testuser"
  }
}
```

Requests ending with errors must return an error response with the code 400 according to the structure described below :

## Error Response
| Field | Type | Mandatory | Description |
| ----- | ---- | -------- | -------------|
| error | JSON Object | Yes | Failed request response envelope |
| code | Number | Yes | An error code |
| message | String | Yes | Error description|

example below

```
{
  "error": {
    "code": "2",
    "message": "Invalid user provided"
  }
}
```

### Error codes

| Code | Description |
| ----- | ---------- |
| 1 | Invalid partner name provided |
| 2 | Invalid token provided |
| 3 | Invalid user provided |
| 4 | Insufficient user funds |
| 5 | Invalid message signature |
| 6 | User login token has been expired |

## Request Security

In order to ascertain the security of incoming request, the partner service is expected to verify the genuinity and authenticity of every request by verifying the value of the request header 
X-Quickplay-Signature which is passed along with every request.
The verification should be done using the OpenSSL verification.
The sample below shows the signature creation.

### PHP Sample
```
protected function signRequest($data) {
    if(!$this->privateKeyPath || !file_exists($this->privateKeyPath)) {
        return '';
    }

    // encode request data to json string
    $data = yii\helpers\Json::encode($data);
    $pkeyid = openssl_pkey_get_private(file_get_contents($this->privateKeyPath));
    $r = openssl_sign($data, $signature, $pkeyid);

    if($r == true) {
        return base64_encode($signature);
    }

    return '';
}
```

### DEV public key:

```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC9WOdzo1hIL5FDlDjR6tmeioZ0
DdvJY6s/PrMTTHQY4IN+Aw58KdNtNEz0V3KnPWyeHTjOv/M7xudyKTB53IyIMmKn
dYX3MsQSXMROZ7Kssw8yx5DNOhDj+ANc74kBoh3rvWZJOtvx7n8QQB/+AfHArYtf
wBPViAOvGQ8AQuw9IwIDAQAB
-----END PUBLIC KEY-----
```

## Authentication request (auth)
### `POST /auth`

Paramerter data[token] - user token for authorization

Sample Request
```
curl --request POST \
  --url 'http://partner_endpoint_url/auth' \
  --header 'token: qweqwe123' \
  --header 'x-quickplay-signature: YXNkYXNkYXNkYXNxMHUxMDIzNGpraDMyNGk5eTEzOXV5NDFqazIzaDEyOTgzeTEyODkz' \
  --data '{"data":{"token":"qweqwe123"}}'
```

Sample Response
```
{
  "data": {
    "id": "111",
    "balance": 100,
    "username": "Superman1"
  }
}
```

## Get user details
### `POST /user-details`

Paramerter data[user_id] - requested user

Sample Request
```
curl --request POST \
  --url 'http://partner_endpoint_url/user-details' \
  --header 'x-quickplay-signature: YXNkYXNkYXNkYXNxMHUxMDIzNGpraDMyNGk5eTEzOXV5NDFqazIzaDEyOTgzeTEyODkz'
  --data '{"data":{"user_id":555}}'
```

Sample Response
```
{
  "data": {
    "id": "111",
    "balance": 100,
    "username": "Superman1"
  }
}
```

## Transaction for win, cancel, rollback
### `POST /transaction`

Paramerter in data array:
* partner
* token (token is ignored for win / cancel contest - if not preserved )
* user
* type - 0 - enter contest; 1 - win contest ; 2 - cancel contest; 3 - rollback; 
* amount
* contest_id
* transaction_id - unique number
* reference (transaction_id) - only for 3 - rollback


Sample Request
```
curl --request POST \
  --url 'http://partner_endpoint_url/user-details' \
  --header 'x-quickplay-signature: YXNkYXNkYXNkYXNxMHUxMDIzNGpraDMyNGk5eTEzOXV5NDFqazIzaDEyOTgzeTEyODkz'
  --data '{"data":
	{
		"user_id":555, 
		"type": 0, 
		"amount": 100,
		"contest_id": 523,
		"transaction_id": "awq8az1q2x",
		"reference": "awq8az1q2x",
	}}'
```

Sample Response
```
{
    "data": {
        "id": "555",
        "balance": 100,
        "username": "Test user"
    }
}
```

