CHG Enterprise Architecture Principles

[![GitHub version](https://badge.fury.io/gh/chgdev%2Farch.svg)](http://badge.fury.io/gh/chgdev%2Farch)

# API Conventions

## API Types
### Interfaces
#### RESTful API

REST should be the default standard for APIs.

#### GraphQL API

GraphQL is a query language that provides a powerful interface for querying data through an API. This dictates that an API client must support GraphQL. See graphql.org.

### Layers
#### Experience API

An Experience API is an API built for one or more specific experience(s) (UX). For example, an API built to support a specific application UI. An Experience API is usually custom tailored to a UX and aggregates dependent data in a way most useful to the UX in question.

#### Domain API

Shared API's for Domain data.

#### Application API

Lowest layer of API which works directly with specific applications or data stores.


## General Standards

### Microservices

A microservice is an API that is "just big enough". That description is decidedly ambiguous. A microservice boundary is usually a logical boundary dictated by packaging of deployable code. It can contain multiple endpoints, but should function in one, and only one, logical business domain.

### Storage

A microservice should have one (or more) private datastore(s) to which it is the sole read/write point.

### Stateless

APIs should be stateless.

### Idempotent

In almost all cases, APIs should be idempotent.

### Language Agnostic

APIs should be language agnostic. The outlined syntaxes and standards in this document should help assure that the underlying programming languages should be irrelevant to the interface.

Nowhere in the URL should there be any indication of underlying programming language.

### Infrastructure Agnostic

The deployed infrastructure for an API is also irrelevant.

Nowhere in the URL should there be any indication of underlying infrastructure or architecture.

### Non-breaking Changes

Whenever possible, make additive non-breaking changes to APIs. This will help avoid the pain of maintaining multiple deployed versions. See [versioning](#versioning).

## <a name="versioning"></a> Versioning

While not required, providing versioning is an excellent way to provide non-breaking forward-looking changes to an API with existing consumers.

### Version Numbering

When tagging a build and/or incrementing the version via `package.json` or `pom.xml`, use [Semantic Versioning](http://semver.org). The published API version should be only the "major" version portion and should change infrequently, only incrementing when introducing major/breaking changes. For example:

|SemVer Build Version|API Version|
|--------------------|-----------|
| 1.0.3 | v1 |
| 1.1.0 | v1 |
| 2.0.0 | v2 |

etc.

### Deployed Versions

Be cautious when building APIs. Breaking changes will necessitate new versions and you will have to deploy (and maintain) multiple versions OR all dependent clients will have to conform to the changes at an organized time (likely impossible). As soon as you deploy multiple versions, you may find it costly to keep multiple versions available long-term.

### Number of Versions Supported

It is **recommended** to support no more than two active major versions at one time, due to complexity of maintenance.

### Syntax

Specify versions in the URL as part of the folder structure. The version of each API should be specified at the root, after the API name, but before each endpoint in the API.

Version all endpoints together within an API, rather than worrying about version routes for each endpoint--this would get rather messy in a large API with many endpoints.

Examples:

    GET /identity-service/v1/users/34123564087
            ^ API name   ^ version   ^ endpoint

### Recommendation

It's not necessary to statically version all APIs. You may consider omitting a version specifier from your URL structure if there are no plans for additional API consumers. Experience API's are most likely to not require versioning as the requirement is based primarily on the number of consumers and how likely you are to make breaking changes.

If your API is planned to be consumed by multiple clients, start with versioning from the beginning, starting with 'v1'. Build this into your initial API endpoints.

#### Code and Deployment

To simplify deployments, once a breaking change will be introduced, we recommend creating a full copy of the old version (e.g. 'v1') within the (same) code base. Future deployments will create a deployable package that implements each active/supported version.

##### Pros

- Simplified deployment/pipeline infrastructure
- Simple to make changes to old versions (bug/security fixes)
- Encourages team to support fewer versions
- Encourages team to really consider when to make breaking changes/major version increments

##### Cons

- Maintaining two versions requires code duplication
- Bug fixes may need to be done twice
- Endpoint routing must be considered up-front for version consideration

##### Other approaches

- Forking the repo to create old version fork
    - Deployment/pipeline/infrastructure is much more expensive
    - Infrastructure may not support this approach with the recommended URL structure at all
- Tagging versions (Git tag)
    - This works better for Applications than it does for API's. API's are more likely to have multiple live versions at one time and need complex deployment/infrastructure.


### Example

Experience API (versionining not necessary):

    GET /identity-service/users/16818463

Common service with multiple planned consumers:

    GET /identity-service/v1/users/16818463     // v1
    GET /identity-service/v2/users/16818463     // v2

## URL Structure

General format:

    [environment].[domain]/[apiname]/[version]/objects/[id][?query]

### Case Sensitivity

Domain names are case-insensitive by definition. All other URL constructs are case-sensitive by default (this is recommended). This includes paths and named query-string parameters. Whether query-string values are case-sensitive is up to the application (but case-sensitive is **always** recommended).

### Domains

Keep the domain name simple and logical. Domains are typically provided by the company/infrastructure.

### API Name

The name of the API collection/deployment. This should likely NOT be pluralized and should likely have a '-service' suffix.

### Version

Optional. As discussed in [Versioning](#versioning).

#### Environments

Prefix your primary domain with an environment indicator. 

Example:

    somedomain.com/apiname       // prod
    dev.somedomain.com/apiname   // dev
    stage.somedomain.com/apiname //stage

### Path

All path components should be all lower-case. Words should be separated with dashes.

Endpoint names should be plural. This is to avoid ambiguity.

Example:

    GET /travel-service/v1/airport-codes/  // list of airport codes in v1 of the travel-service
    GET /identity-service/users          // list of users
    GET /identity-service/users/8187373  // get user 8187373
    POST /identity-service/users         // create new user(s)
    GET /identity-service/users/8187373/preferences // get list of preferences for user 8187373

### Version

See [Versioning](#versioning) for guidelines.

Examples:

    GET /identity-service/v2/users/818913/settings

### Query String

Query string parameter names should be `camelCase`.

#### Filtering (optional)
#### Pagination (optional)

## Hypermedia 

Hypermedia is a useful tool to add to webservices. We currently recommend HATEOAS style, if implementing hypermedia. Hypermedia is especially useful on common services where many consumers are expected, especially external consumers.

### HATEOAS

#### Examples

## Payloads

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
