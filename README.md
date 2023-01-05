# AccManagementSvc-Design

## AccManagementSvc Endpoints

## Account Summary
A user hits this endpoint in order to view the details of their account. They can view details like the services that are available and services that theywant to subscribe to.
There will be jwt token containing userid in cookie
#### Specification:
Method: `GET`

Path: `/account`

Request Body: `not required.`

Success to follow response as specified:

Response Header: HTTP 200

Response Body(json):
```json
{
  "status": 200,
  "message": "SUCCESS",
  "data": {
    "account_number": "<full account number>",
    "income": <income calculated based on all incoming transactions> as float,
    "spends": <spends calculated based on all outgoing transactions> as float,
    "active_services": ["<list of all services that user has subscribed to>",],
    "available_services": ["<list of all services that user has not subscribed to but are available for subscription>",]
  }
}
```

## Create Account
This endpoint is used to create a new account in a Relational DB with `account_number` as the `primary key` and as a `int`,`user_id`as `string` and it should be `unique`, `income` and `spends` as `decimal`,`created_at` and `updated_at` as `timestamp` `active_services` and `inactive_services` as `json` and once its done we will notify the user mgmt svc through msg queue. This endpoint will get hit from usermanagement service through msg queue.

DbSchema:
```
+-------------------+---------------+------+-----+-------------------+-----------------------------------------------+
| Field             | Type          | Null | Key | Default           | Extra                                         |
+-------------------+---------------+------+-----+-------------------+-----------------------------------------------+
| user_id           | varchar(255)  | NO   | UNI | NULL              |                                               |
| account_num       | int           | NO   | PRI | NULL              | auto_increment                                |
| income            | decimal(18,2) | YES  |     | 0.00              |                                               |
| spends            | decimal(18,2) | YES  |     | 0.00              |                                               |
| created_at        | timestamp     | NO   |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED                             |
| updated_at        | timestamp     | NO   |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED on update CURRENT_TIMESTAMP |
| active_services   | json          | NO   |     | json_object()     | DEFAULT_GENERATED                             |
| inactive_services | json          | NO   |     | json_object()     | DEFAULT_GENERATED                             |
+-------------------+---------------+------+-----+-------------------+-----------------------------------------------+
```

#### Specification:
Method: `POST`

Path: `/account`

Request Body: 
```json
{
  "user_id": "<user_id for the record to activate>"
}
```

Success to follow response as specified:

Response Header: HTTP 200

Response Body(json):
```json
{
  "status": 201,
  "message": "SUCCESS",
  "data": nil
}
```

## Latest Transaction
This endpoint receives the details of latest transaction and it updates the income and spends column in db according to type of transaction
#### Specification:
Method: `PUT`

Path: `/account/update/transaction`

Request Body: 
```json
{
  "account_number":"acc_no."
  "amount": total amount of the transaction as float,
  "type":"debit or credit"
}
```

Success to follow response as specified:

Response Header: HTTP 200

Response Body(json):
```json
{
  "status": 202,
  "message": "SUCCESS",
  "data": nil
}
```

## Update services
This endpoint updates the active service and available service column acc to query
#### Specification:
Method: `PUT`

Path: `/account/update/transaction`

Request Body: 
```json
{
  "account_number":"acc_no."
  "name of service": name of service that needs to be removed or added,
  "type":"add or remove"
}
```

Success to follow response as specified:

Response Header: HTTP 200

Response Body(json):
```json
{
  "status": 202,
  "message": "SUCCESS",
  "data": nil
}
```

## AccManagementSvc Middlewares

1. ExtractUser: extracts the user_id from the cookie passed in the request and forwards it in the context for downstream processing.
2. ScreenRequest: allows requests only from the message queue to be passed downstream. The middleware checks the “`user-agent`” & request `URL` to identify requests originating from the message queue.
   *The URL(s) of the message queue(s) is passed as a configuration to the service to allow requests only from URLs in the list*.
3. Caching middleware
