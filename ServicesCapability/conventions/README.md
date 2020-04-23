CHG Enterprise Architecture Principles

[![GitHub version](https://badge.fury.io/gh/chgdev%2Farch.svg)](http://badge.fury.io/gh/chgdev%2Farch)

# API Conventions

## API Types
### Interfaces
#### RESTful API
REST should be the default standard for APIs.

#### GraphQL API
GraphQL is a query language that provides a powerful interface for querying data through an API. This dictates that an API support GraphQL. See graphql.org.

### Layers
#### Experience API
An Experience API is an API built for one or more specific experience(s). For example, an API built to support a specific application UI.

#### Domain API
Composite API's for Domain data.

#### Application API
Lowest layer of API which works directly with specific applications or data stores.


## General Standards
### Stateless
APIs should be stateless.

### Idempotent
In almost all cases, APIs should be idempotent.

### Language Agnostic
APIs should be language agnostic. The outlined syntaxes and standards in this document should help assure that the underlying programming languages should be irrelevant to the interface.

Nowhere in the URL should there be any indication of underlying programming language.

### Environment Agnostic
The deployed environment for an API is also irrelevant.

Nowhere in the URL should there be any indication of underlying environments or architectures.

### Non-breaking Changes
Whenever possible, make additive non-breaking changes to APIs. This will help avoid the pain of maintaining multiple deployed versions. See [versioning](#versioning).


## <a name="versioning"></a> Versioning
When tagging a build, use [Semantic Versioning](http://semver.org).

### Deployed Versions
While not required, providing versioning is an excellent way to provide non-breaking forward-looking changes to an API with existing consumers.

Be cautious when building APIs. Breaking changes will necessitate new versions and you will have to deploy (and maintain) multiple versions OR all dependent clients will have to conform to the changes. As soon as you deploy multiple versions, you may find it costly to keep multiple versions available long-term.

#### Syntax
Specify versions in the URL as part of the folder structure. The version of each endpoint should be specified just after the endpoint name.

Examples:

    GET /users/v1/34123564087

#### Recommendation
It's not necessary to statically version all APIs. You may consider omitting a version specifier from your URL structure until absolutely necessary. Experience API's are most likely to not require versioning as the requirement is based primarily on the number of consumers and how likely you are to make breaking changes.

A version number can be at the endpoint level or the "container" level, depending on where code branches can create differing versions (if each endpoint is its own codebase, versioning goes here, otherwise, it's up a level).

Originally:

    GET /users/16818463

Once v2 is necessary and must be deployed alongside v1:

    GET /users/16818463     // v1
    GET /users/v2/16818463  // v2


## URL Structure

General format:

    api.[environment].domain/[container]/objects/[id][?query]

### Case Sensitivity
Domain names are case-insensitive by definition. All other URL constructs are case-sensitive by default (this is recommended). This includes paths and named query-string parameters. Whether query-string values are case-sensitive is up to the application.

### Domains
Keep the domain name simple and logical.

#### Environments
Prefix your primary domain with an environment indicator. The keyword "api" should go before *that* prefix.

Example:

    api.somedomain.com       // prod
    api.dev.somedomain.com   // dev
    api.stage.somedomain.com //stage

### Path
All path components should be all lower-case. Words should be separated with dashes.


Endpoint names should be plural. This is to avoid ambiguity.

Example:

    GET /travel/airport-codes/  // list of airport codes
    GET /users          // list of users
    GET /users/8187373  // get user 8187373
    POST /users         // create new user(s)
    GET /users/8187373/preferences // get list of preferences for user 8187373

**NOTE:** 'travel' is a [container](#container), not a service, therefore it need not be plural.

#### <a name="container"></a> Container (optional)
There may be a logical container for a collection of APIs. This may even be multiple levels.

This could be a categorization, logical structure, or a named collection like "tools" or "helpers".

This level is NOT required.

### Version
See [Versioning](#versioning) for guidelines.

Examples:

    GET /users/v2/818913/settings

### Query String
Query string parameter names should be `camelCase`.

#### Filtering (optional)
#### Pagination (optional)

## Hypermedia 

Hypermedia is a useful tool to add to webservices. We currently recommend HATEOAS style, if implementing hypermedia. Hypermedia is especially useful on common services where many consumers are expected, especially external consumers.

### HATEOAS

#### Examples

## Payload

### Metadata

Some suggested Metadata:

1. `correlationId`
1. `timestamp`
1. Probably others...

### Data

#### Format

1. JSON (Content-Type `application/JSON`) is highly recommended for most services.
1. Top-level element should always be an object to help prevent [JSON Hijacking](https://haacked.com/archive/2009/06/25/json-hijacking.aspx/).
1. Include meta-data at the top level
1. Data should be included in a `data` or `records` container

### Conventions

1. Field names should always be in "camelCase" format. 
  1. Fields should always start with a lower-case letter
1. Strict "camelCase" should apply to all acronyms and abbreviations to make their boundaries clear: e.g. `userId`, `userSsnVerified`

## Responses

### HTTP Status Codes

RESTful services should return logical, standard HTTP status codes.

See [IANA for official status codes](http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml).

Common codes:

|Code and Text|Reason / use|
|-------------|------------|
|**2XX**|**Success codes**|
|200|Request was acceptable and succeeded|
|201|Request to create data was successful|
|202|Asynchronous request was accepted - processing pending|
|**300**|**(REDIRECTION)**|
|301|Resource has moved to a permanent location|
|302|Resource has temporarily moved (or location is not permanent)|
|304|Resource was found, but not modified (If-Modified-Since)|
|**4XX**|**(CLIENT ERROR)**|
|400|When the request is malformatted or not understood|
|401|When public access is not allowed but no auth provided When authentication is required ||but|failed|
|404|When request URI path is not found or search does not match|
|406|Request accept parameter(s) cannot be fulfilled|
|409|Conflict|
|**5XX**|**(SERVER ERROR)**|
|500|Server experienced an unhandled error/exception|
|501|Requested a method that is not implemented|
|502|Bad gateway or unhandled downstream error|
|503|Service unavailable, down or in error state|
|504|Gateway timeout or possibly timeout occurred downstream|


## Security
APIs should always be served with TLS where possible.

### Methods
#### JWT
#### OAuth2
##### Flows
