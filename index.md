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
| Field | Type |Mandatory | Description |
| ------ | ----- | ----- | ------- |
| x-quickplay-signature | String | Yes | QP public key |
| token | String | Yes | The token for the request |
 
Every request must return a successful response with http code 200 according to the structure described below :

## Success Response
 
| Field | Type | Mandatory | Description |
| ----- | -----| -------- | -------------|
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
| ----- | -----| -------- | -------------|
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
