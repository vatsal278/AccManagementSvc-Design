# AccManagementSvc-Design

## AccManagementSvc Endpoints
## Summary
A user hits this endpoint in order to view the details of their account. They can view details like the services that are available and services that theywant to subscribe to.
#### Specification:
Method: `GET`

Path: `/summary`

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
    "income": "<income calculated based on all incoming transactions>",
    "spends": "<spends calculated based on all outgoing transactions>",
    "active_services": "<list of all services that user has subscribed to>",
    "available_services": "<list of all services that user has not subscribed to but are available for subscription>"
  }
}
```
