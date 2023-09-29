# RESTful API

An API is a user interface for developers. Put the effort in to ensure it's not just functional but pleasant to use.

## Terminologies

The following are the most important terms related to REST APIs.
- Resource is an object or representation of something, which has some associated data with it, and there can be a set of methods to operate on it. E.g., Animals, schools, and employees are resources, and `delete, add, update` are the operations to be performed on these resources.

- Collections are set of resources, e.g., Companies is the collection of Company resource.

- URL (Uniform Resource Locator) is a path through which a resource can be located, and some actions can be performed on it.

## Foundations

- We use Swagger/OpenAPI for REST API documentation.

- Use SSL everywhere, no exceptions.

- Pretty print by default & ensure gzip is supported.

- Field name casing: make sure the casing convention is consistent across the application. If the request body or response type is JSON, please follow [camelCase](https://en.wikipedia.org/wiki/Camel_case) to maintain the consistency. (At the moment we wrote this document, our backend is written in Go, .Net, and our frontend is written in ReactJS, so we consider camelCase as the standard for field name casing)

- Use Nouns in URI: REST API should be designed for resources. For example, instead of `/createUser` use `/users`.

- We prefer to use plurals so we don't having to deal with odd pluralization (person/people, goose/geese) makes the life of the API consumer better and is easier for the API provider to implement , but there is no hard rule that one can't use the singular for the resource name.

- Include a `Request-Id` header in each API response, populated with a UUID value. By logging these values on the client, server and any backing services, it provides a mechanism to trace, diagnose and debug requests.

## API Versioning

Always version your API. Version via the URL, not via headers. Versioning APIs always helps to ensure backward compatibility of service while adding new features or updating existing functionality for new clients. The backend will support the old version for a specific amount of time.

- URL: Embed the version in the URL such as `POST /v2/users`.
- Header
    - Custom header: Adding a custom X-API-VERSION header key by the client can be used by a service to route a request to the correct endpoint.
    - Accept header: Using the accept header to specify your version.

## Stateless Authentication & Authorization

REST APIs should be stateless. Every request should be self-sufficient and must be fulfilled without knowledge of the prior request. This means that request authentication should not depend on cookies or sessions. Instead, each request should come with some sort of authentication credentials.

Previously, developers stored user information in server-side sessions, which is not a scalable approach. Use token-based authentication, transported over OAuth2 where delegation is needed.

- For service-to-service communication, try to have the encrypted API-key passed in the header.
- For user authorization, JWT with OAuth2 provides a way to go.

## Request

- All POST, PUT, PATCH requests are JSON encoded and must have content type of application/json, or the API will return a 415 Unsupported Media Type status code.

- Let the HTTP verb define action. Don't misuse safe methods. Use HTTP methods according to the action which needs to be performed.

- Depict resource [hierarchy](https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9) through URI. If a resource contains sub-resources, make sure to depict this in the API to make it more explicit.

```
  GET /tickets/12/messages - Retrieves list of messages for ticket #12
  GET /tickets/12/messages/5 - Retrieves message #5 for ticket #12
  POST /tickets/12/messages - Creates a new message in ticket #12
  PUT /tickets/12/messages/5 - Updates message #5 for ticket #12
  PATCH /tickets/12/messages/5 - Partially updates message #5 for ticket #12
  DELETE /tickets/12/messages/5 - Deletes message #5 for ticket #12
```
- If an action that don't fit into the world of CRUD operations we can use one of the following approaches:

    - Restructure the action to appear like a field of a resource. This works if the action doesn't take parameters.
    For example an `activate` action could be mapped to a boolean `activated` field and updated via a PATCH to the resource.
    - Treat it like a sub-resource with RESTful principles.
    For example, GitHub's API lets you star a gist with `PUT /gists/:id/star` and unstar with `DELETE /gists/:id/star`.
    - Sometimes you really have no way to map the action to a sensible RESTful structure.
    For example, a multi-resource search doesn't really make sense to be applied to a specific resource's endpoint.
    In this case, `/search` would make the most sense even though it isn't a resource.

- GET requests should never change data on the server!

## Response

- All response bodies are JSON encoded.

- Don't use an envelope by default, but make it possible when needed

- Unset fields will be represented as a `null` instead of not being present. If the field is an array, it will be represented as an empty array - ie `[]`.

- Timestamps are in [UTC](http://en.wikipedia.org/wiki/Coordinated_Universal_Time) and formatted as [ISO8601](http://en.wikipedia.org/wiki/ISO_8601).

  Single data entry response

  ``` json
  {
    "field1": "value",
    "field2": true,
    "field3": []
  }
  ```

  A collection of resources is represented as a JSON array of objects:

  ``` json
  [
    {
      "field1": "value",
      "field2": true,
      "field3": []
    },
    {
      "field1": "another value",
      "field2": false,
      "field3": []
    }
  ]
  ```

### Return something useful from POST, PATCH & PUT requests

`POST, PUT, or PATCH `methods, used to create a resource or update fields in a resource, should always return updated resource representation as a response with appropriate status code as described in further points.

`POST`, if successful in adding a new resource, should return HTTP status code 201 along with the URI of the newly created resource in the `Location` header (as per HTTP specification)

### HTTP status codes

HTTP defines a bunch of meaningful status codes that can be returned from your API. These can be leveraged to help the API consumers route their responses accordingly. I've curated a shortlist of the ones that you definitely should be using:

- `200 OK` - Response to a successful GET, PUT, PATCH or DELETE. It can also be used for a POST that doesn't result in creation.

- `201 Created` - Response to a POST that results in a creation. Should be combined with a Location header pointing to the location of the new resource

- `204 No Content` - Response to a successful request that won't be returning a body (like a DELETE request)

- `304 Not Modified` - Used when HTTP caching headers are in play

- `400 Bad Request` - The request is malformed, such as if the body does not parse

- `401 Unauthorized` - When no or invalid authentication details are provided. Also useful to trigger an auth popup if the API is used from a browser

- `403 Forbidden` - When authentication succeeded but the authenticated user doesn't have access to the resource

- `404 Not Found` - When a non-existent resource is requested

- `405 Method Not Allowed` - When an HTTP method is being requested that isn't allowed for the authenticated user

- `415 Unsupported Media Type` - If the incorrect content type was provided as part of the request

- `422 Unprocessable Entity` - Used for validation errors

- `429 Too Many Requests` - When a request is rejected due to rate limiting

- `500, 501, 502, 503, etc ` - An internal server error occured

### Error

All 400 series errors (400, 401, 403, etc) will be returned with a JSON object in the body and a `application/json` content type.

500 series error codes (500, 501, 502, etc) do not return JSON bodies.

A JSON error body should provide a few things for the developer - a useful error message, a unique error code (that can be looked up for more details in the docs) and possibly a detailed description.

```json
{
  "error": {
    "code": 123,
    "message": "Something bad happened",
    "description": "More details"
  }
}
```

In case of validation errors on a `POST/PUT/PATCH` request, a `422 Unprocessable Entry` status code will be returned. The JSON response body will include an array of error messages.

```json
{
  "code" : 1024,
  "message" : "Validation Failed",
  "errors" : [
    {
      "code" : 5432,
      "field" : "first_name",
      "message" : "First name cannot have fancy characters"
    },
    {
       "code" : 5622,
       "field" : "password",
       "message" : "Password cannot be blank"
    }
  ]
}
```

## Rate limiting

To prevent abuse, it is standard practice to add some rate-limiting to an API. At a minimum, include the following headers:

- `X-Rate-Limit-Limit` - The number of allowed requests in the current period
- `X-Rate-Limit-Remaining` - The number of remaining requests in the current period
- `X-Rate-Limit-Reset` - The number of seconds left in the current period

Some APIs use a UNIX timestamp (seconds since epoch) for X-Rate-Limit-Reset. Don't do this! Use [RFC 1123 date formats](https://www.ietf.org/rfc/rfc1123.txt) instead.

## Caching

HTTP provides a built-in caching framework. All you have to do is include some additional outbound response headers and do a little validation when you receive some inbound request headers.

There are 2 approaches: [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) and [Last-Modified](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.29)

- ETag: When generating a response, include an HTTP header ETag containing a hash or checksum of the representation. This value should change whenever the output representation changes. If an inbound HTTP request includes a If-None-Match header with a matching ETag value, the API should return a 304 Not Modified status code instead of the resource's output representation.

- Last-Modified: This works like ETag, except that it uses timestamps. The response header Last-Modified contains a timestamp in RFC 1123 format, which is validated against If-Modified-Since. Note that the HTTP spec has had 3 different acceptable date formats, and the server should be prepared to accept any one of them.

## Field Filtering

The API consumer doesn't always need the full representation of a resource. The ability to select and chose returned fields goes a long way in letting the API consumer minimize network traffic and speed up their own usage of the API.

All responses from the API can limit fields to only the fields you need. Just pass in a `fields` query parameter with a comma separated list of fields you need.
For example: `GET /api/v1/users?fields=id,first_name`

```json
[
  {
    "id": "543abc",
    "first_name:": "John"
  },
  {
    "id": "543add",
    "first_name:": "Bob"
  }
]
```

Filter by time range:

```
GET /companies?time=fromTime-toTime
```

## Embedding

There are many cases where an API consumer needs to load data related to (or referenced from) the resource being requested.
For example, when loading a single company, it's customer information is often needed as well. Instead of forcing the API consumer to make multiple requests, we can allow them to specify what related data they need in the same request.

In this case, embed would be a separated list of fields to be embedded. Dot-notation could be used to refer to sub-fields.

```
GET /companies/12?embed=customer&fields=id,customer.id,customer.name
```

```json
  {
    "id": "12",
    "customer": {
      "id": "543abc",
      "name": "John"
    }
  }
```

## Pagination

Requests for collections can return between 0 and N results, controlled using the `per_page` and `page` query parameters. All end points are limited to 10 results by default.

```
GET /api/v1/tickets?per_page=15&page=2
```

## Sorting

Some endpoints offer result sorting, triggered using the `sort` query parameter. The value of `sort` is a comma separated list of fields to sort by.

- You can specify descending sort by prepending `-` to a field.

- Not all fields can be sorted on. The endpoint documentation will list supported sort options.

- The default sort for all endpoints should be descending order of creation.

```
GET /companies?sort=-created_at
```

Multiple sort fields can be specified:

```
GET /companies?sort=-created_at,name
```

## Searching

```
GET /companies?search=Mckinsey
```

## Batch Delete

There is no clean `Restful way` to delete a collection of id without any limitation:
  - DELETE method with list of ID in query params is limit by its length (2048 characters)
  - DELETE method doesn't require a BODY, so most of Gateway services ignore the body when direct the request to servers.

We use a custom `POST` methods to achieve a batch delete functionality:
```
POST /companies/batch_delete

BODY: {
    ids: ["id_1", "id_2"]
}
```