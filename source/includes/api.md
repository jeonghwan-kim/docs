# API Overview

## API Summary

The Stamplay API exposes the following resources:

Manage and access object data.
Manage and access user data, create user sessions.
Manage and access user roles, assign roles.
Execute Code Blocks.
Manage customers and payment information, charge a customer, add subscriptions.
Send an email.


| Resource | Endpoint |	
|---------|-------------------------:|
| Objects | /api/cobject/v1/{id} |
| Users | /api/user/v1/users |
| Roles | /api/user/v1/roles |
| Code Blocks | /api/v1/run/:codeblock |
| Stripe | /api/stripe/v1/ |
| Email | /api/email/v1/send |

## Pagination

```json
	{
		"data": [],
		"pagination": {
			"page": 1,
			"per_page": 20,
			"total_pages": 4,
			"total_elements": 71
		}
	}
```

Responses from the **User** and **Object** resource will also return a `pagination` object.

Pagination can be managed by using the `page` and `per_page` query parameters.

Pagination details are included using the Link header introduced by RFC 5988.
The Link header returns a set of ready-made links so the API consumer does not have to construct the links themselves.

## Filtering

```shell
	curl -X "GET" "https://APP-ID.stamplayapp.com/api/cobject/v1/picture?status=published"
```

```javascript
	Stamplay.Object("picture")
		.get({ status : "published" })
		.then(function(res) {
			// success
		}, function(err) {
			// error
		})
```

```nodejs
	Stamplay.Object('picture')
		.get({ status : "published" }, function(err, res) {
		  // response
		})
```

To filter a response based on a value of a field, set the field and value as a parameter in the request. To filter by more than one field, add each filterable field to the request as a paramter.

## Sorting

```shell
	curl -X "GET" "https://APP-ID.stamplayapp.com/api/cobject/v1/picture?sort=-dt_create"
```

```javascript
	Stamplay.Object("picture")
		.get({ sort : "-dt_create" })
		.then(function(res) {
			// success
		}, function(err) {
			// error
		})
```

```nodejs
	Stamplay.Object('picture')
		.get({ sort : "-dt_create" }, function(err, res) {
		  // response
		})
```

To sort the response, set a `sort` parameter as a comma delimited list to sort by in order of importance.

If a field name is appened to an `-` character then that field will be sorted in descending order based on the type, otherwise by default the field will be sort in ascending order.


## Selecting

```shell
	curl -X "GET" "https://APP-ID.stamplayapp.com/api/cobject/v1/picture?select=-dt_create,owner,status"
```

```javascript
	Stamplay.Object("picture")
		.get({ select : "dt_create,owner,status" })
		.then(function(res) {
			// success
		}, function(err) {
			// error
		})
```

```nodejs
	Stamplay.Object('picture')
		.get({ sort : "dt_create,owner,status" }, function(err, res) {
		  // response
		})
```

Selecting is the response projection for each object returned. This allows for a lighter data transfer between requests, and simplifies response objects.

To select a field to return, set the `select` request parameter to the value of the field name to return. If you want to return more than one field, set the `select` parameter as a comma delimited set of properties to return.

The `_id` field is always returned.

## Advanced Queries

```shell
	curl -X "GET" "https://APP-ID.stamplayapp.com/api/cobject/v1/picture?where=%7B%20rating'%20:%20%7B%20$gt%20:%205%20%7D%20%7D"

```

```javascript
	Stamplay.Object("picture")
		.get({ where : JSON.stringify({ rating : { $gt : 5 } }) })
		.then(function(res) {
			// success
		}, function(err) {
			// error
		})
```

```nodejs
	Stamplay.Object('picture')
		.get({ where : JSON.stringify({ rating : { $gt : 5 } }) }, function(err, res) {
		  // response
		})
```

For more advanced methods of queries, Stamplay supports an array of MongoDB's conditional operators.

By setting a JSON query string to the `where` parameter you can use the operators to perform advanced queries.

| Operator   | Summary |
|:------------|:--------------------------------------------------------------------------------------------------------|
| $gt        |Matches values that are greater than the value specified in the query. |
| $gte       | Matches values that are greater than or equal to the value specified in the query. |
| $in        | Matches any of the values that exist in an array specified in the query. |
| $lt        | Matches values that are less than the value specified in the query. |
| $lte       | Matches values that are less than or equal to the value specified in the query. |
| $ne        | Matches all values that are not equal to the value specified in the query. |
| $nin       | Matches values that do not exist in an array specified to the query. |
| $or        | Joins query clauses with a logical OR returns all documents that match the conditions of either clause. |
| $and       | Joins query clauses with a logical AND returns all documents that match the conditions of both clauses. |
| $not       | Inverts the effect of a query expression and returns documents that do not match the query expression. |
| $nor       | Joins query clauses with a logical NOR returns all documents that fail to match both clauses. |
| $exists    |  Matches documents that have the specified field. |
| $type      | Selects documents if a field is of the specified type. |
| $all       | Matches arrays that contain all elements specified in the query. |
| $elemMatch | Selects documents if element in the array field matches all the specified $elemMatch condition. |
| $size      | Selects documents if the array field is a specified size |
| $regex     | Selects documents where values match a specified regular expression. |
          
## Authentication

```shell-always
	
	"Authorization" : "Basic c3RhbXBsYXlrYjo5MTRhMmUzMaMwZWRlMDdmZThhNjYzMzkxMWQyYWQyOTNkNjJkNTQ3OGFiYmViNjEyNzhhYjFjYzE2MDhiZaRi"
```

```shell-always
	"x-stamplay-jwt" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0eXBlIjoidXNlciIsImFwcERvbWFpbiI6InN0YW1wbGF5a2Iuc3RhbXBsYXlscHAuY29tIiwiYXBwSWQiOiJzdGFtcGxheWtiIiwidXNlciI6IjU2ZjAyZDY0NjFkNWI0MTA1ZDFhMjY4NCIsImlhdCI6MTQ1ODU4MTQwNiwiZXhwIjoxNDU5MTg2Mjc4fQ.UDFUe0Zb2ZHx3HXdiUXWnHSyKghVI_harpkkyC3BU8A"
```

Depending on the origin enviornment of a request to Stamplay, the method of authenticating the request differs.

Client side requests can be authenticated by setting the `x-stamplay-jwt` header as the JSON Web Token issued when a user authenticates with an email and password, or social provider.

Server side request can be authenticated by setting the `Authorization` header as `Basic AppId:ApiKey`. The `AppId:ApiKey` are to be set as a base64 encoding.

## CORS Policy

Stamplay uses a selective cross-origin resource sharing (CORS) policy, meaning that you may only make requests to the Stamplay from an origin that has been previously declared.

This is made to govern which origins are authorized and which are not. By default only the base URL ending with `.stamplayapp.com` is the one allowed to send requests.

To enabled other CORS enabled domains, visit the **Hosting** section with the Stamplay editor.
## Allowed HTTP Requests
| Method   |    Summary                          |
|----------|-------------------------------------|
| GET      | Get a resource or list of resources |
| POST     | Create a resource                   |
| PUT	   | pdate a resource                    |
| PATCH    | Update a resource partially         |
| DELETE   | Delete a resource                   |

## Error Codes
| Status Code | Status Text           | Summary
|-------------|-----------------------|--------------------------------|
|    200      | OK                    | The request was successful. |
|    201      | Created               | The request was successful and a resource was created. |
|    204      | No Content            | The request was successful but there is no representation to return (i.e. the response is empty). |
|    400      | Bad Request           | The request could not be understood or was missing required parameters. |
|    401	  | Unauthorized          | Authentication failed or the user request the resource doesn’t have the required permissions for reqested operation. |
|    403      | Forbidden             | Access denied, Authentication will not help. |
|    404      | Not Found             | Resource not found. |
|    405      | Method Not Allowed    | Requested method is not supported for resource. |
|    500      | Internal Server Error | Something went wrong during your request |