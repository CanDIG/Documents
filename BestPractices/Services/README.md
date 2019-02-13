CanDIG RESTful Services Style Guide
===================================

The CanDIG project is building a [*12-factor
application*](https://12factor.net/) for genomic data, and a federation
layer on top of it. As part of this project, we will be standing up a
number of services - where possible, using existing standards, but where
necessary, developing our own.

To try to reduce the amount of decision making needed for each service,
and to try to ensure a degree of uniformity across services, the
following style guide documents the current CanDIG thinking on the
design, implementation, and maintenance of these services for new
development.

API:
----

1.  APIs are designed in OpenAPI (Currently OpenAPI 2, aka Swagger),
and implemented afterwards. Tools like
[Redoc](https://github.com/Rebilly/ReDoc) for documentation,
[Dredd](https://github.com/apiaryio/dredd) for testing, and go-swagger
(go) or Connexion (flask) for server implementation and swagger-codegen
for client implementation can all make use of the OpenAPI specification.

2.  APIs are versioned: *e.g*. `namespace/v1/entityname`. API versions
are increasing integers, and correspond to the major version
number of the tool.  Namespace can be handled by the API gateway.

3.  Endpoints should be plural nouns, not verbs, and represent the (API)
entities (which may be different than those in the data model
underlying the server). The HTTP methods are the verbs. *E.g*.

| Resource  |  `GET` (read)        |   `POST` (create)  |   `PUT` (update)     |    `DELETE`         |
|-----------|----------------------|--------------------|----------------------|---------------------|
| `/cars`     | Returns list of cars | Create a new car   | Bulk update of cars  |  Delete all cars    |
| `/cars/711` | Returns specific car | Not allowed (405)  | Updates specific car | Deletes specific car|

4.  Use sub-resources for relations - If a resource is related to
another resource use subresources. E.g:
```
GET /cars/711/drivers/  # Returns a list of drivers for car 711
GET /cars/711/drivers/4 # Returns driver 4 for car 711
```

5.  There are a number of components in a REST API, and it isn’t always
obvious about what goes where: this breakdown of what goes where
from the [*Adidas style guide*](https://adidas-group.gitbooks.io/api-guidelines/content/rest/protocol/separate-concerns.html)
is useful:

    a.  A *resource identifier*–URI must be used to indicate identity
        only (*e.g.*, we don’t use /cars/711.json to specify format)
        
    b.  *HTTP request method* must be used to communicate the action
        semantics (intent and safety)
        
    c.  *HTTP response status* code must be used to communicate the
        information about the result of the attempt to understand and
        satisfy the request
        
    d.  *HTTP message body* must be used to transfer the message content
    
    e.  *HTTP message headers* must be used to transfer the metadata
         about the message and its content - for instance, Content-Type
         defines the request format, and Accept defines a list of
         acceptable response formats
         
    f.  *URI query parameter* should not be used to transfer metadata

6.  For each endpoint create operations first in the OpenAPI
specification, then update operations, then get operations; move
from more fundamental entities to those that use them; and include
examples for all properties and data objects. The former will help
with automated testing, and the later will help with both testing
and make the documentation more understandable.

7.  Wherever referenced, related entities should have a links field that
includes the URI (endpoint + references) to the entity - this is
sometimes called Hypermedia as the Engine of Application State or
HATEOAS, and provides consistent navigation through (and more
importantly, *across*) APIs

8.  If we implement returning a collection in a sorted order, we
implement it with a sort query parameter like so:
```
GET /cars?sort=-manufacturer,+model
```

9.  If we implement projection operator (returning only some columns),
we implement it with a fields query parameter like so:
```
GET /cars?fields=manufacturer,model,id,color
```

10.  Paging should be handled by the client, not by maintaining state at
the server. (Streaming is another option). Paging is enabled by
allowing the client to request limits to the amount of data sent:
```
GET /cars?offset=10&limit=5
```

11.  Simple filtering is also implemented, where it is implemented, as a
query parameter **TODO**: more complicated filtering (and vs or), aggregations -
see also [Microsoft’s approach](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md#97-filtering)
```
GET /cars?color=red # Returns a list of red cars
GET /cars?seats&lt;=2 # List of cars with a maximum of 2 seats
```

12.  Use HTTP status codes: the ones that make most sense follow.

| **Status Code** | **200 Success** | **201 Created** | **202 Accepted** | **204 No Content** | **400 Bad Request** | **404 Not Found** | **422 Unprocessable Entity** | **500 Internal Server Error** |
|-----------------|-----------------|-----------------|------------------|--------------------|---------------------|-------------------|------------------------------|-------------------------------|
| GET             | X               |                 |                  |                    | X                   | X                 | X?                           | X                             |
| POST            | X               | X               | X?               |                    | X                   | X?                | X?                           | X                             |
| PUT             | X               |                 | X?               | X                  | X                   | X                 | X?                           | X                             |
| DELETE          | X               |                 |                  | X                  | X                   | X                 | X?                           | X                             |

12. Verb usage is as follows:

    a.  GET: The purpose of the GET method is to retrieve a resource. On
        success, a status code 200 and a response with the content of
        the resource is expected. In cases where resource collections
        are empty (0 items in /v1/namespace/resources), 200 is the
        appropriate status (resource will contain an empty items
        array). If a resource item is 'soft deleted' in the underlying
        data, 200 is not appropriate (404 is correct) unless the
        'DELETED' status is intended to be exposed.
        
    b.  POST: The primary purpose of POST is to create a resource. If
        the resource did not exist and was created as part of the
        execution, then a status code 201 SHOULD be returned. It is
        expected that on a successful execution, a reference to the
        resource created (in the form of a link or resource
        identifier) is returned in the response body.
        
    c.  PUT: This method SHOULD return status code 204 as there is no
        need to return any content in most cases as the request is to
        update a resource and it was successfully updated. The
        information from the request should not be echoed back. Put
        MUST be idempotent.
        
    d.  DELETE: This method SHOULD return status code 204 as there is no
        need to return any content in most cases as the request is to
        delete a resource and it was successfully deleted. As the
        DELETE method MUST be idempotent as well, it SHOULD still
        return 204, even if the resource was already deleted

13. Error handling: Errors should return with an error payload in the
    body. These should include a message for the end-user (when
    appropriate), internal error code (corresponding to some specific
    internally determined ID) and links where client developers can
    find more information (these could be links to a github README).
    For example: (See also [*Microsoft’s more detailed approach*](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md#710-response-formats))

```
"Error": {
    "internalMessage" : "Verbose, plain language description of the problem.  Provide developers suggestions about how to solve their problems here",
    "userMessage" : "This is a message that can be passed along to end-users, if needed.",
    "code" : "444444",
    "moreInfo" : "http://api.example.gov/v1/documentation/errors/444444.html"
}
```

Service Implementation:
-----------------------

1.  Because we’re a small team and can’t support a large number of
    different frameworks, we’re going to focus our efforts on one of
    two toolsets. Note that the toolsets (in particular go-swagger)
    means we only support OpenAPI 2.0 for now:
    a.  Go + [*Go-swagger*](https://github.com/go-swagger/go-swagger) +
        [*Pop*](https://github.com/gobuffalo/pop) (for ORM when
        necessary)
    b.  Python3 + [*Connexion*](https://github.com/zalando/connexion) +
        [*SQLAlchemy*](https://www.sqlalchemy.org) (for ORM when
        necessary)

2.  Note the distinction between the API data model (in swagger) and the
    underlying data model (in an ORM); in general the two data models
    can be quite different, even if many new services will often start
    with the API closely modelling the implementation. Even so, many
    crucial pieces of the underlying data model (foreign keys,
    consistency requirements, etc) can’t be modeled in OpenAPI and
    need to be defined separately.

3.  Make use of the validation functionality at both the API level and
    the ORM level.

4.  Much of the logic of any web service involves transformations
    between the API data objects and the internal data model of the
    services. These transformations should be kept in one
    subdirectory, and a peer of the data models of the API and ORM.

5.  We’re moving towards a standard license (LGPL?), with copyright
    owned by the institution which “owns” the service; this is a
    little different than what is currently stated in the
    inter-institutional agreement.  The service owner is responsible
    for release management.

6.  Services are on github under the CanDIG organization, publicly
    accessible, with a Dockerfile that builds the repo into a docker
    container and documentation to install both as a user and as a
    developer.

6.  All services should be unit tested with Travis-CI; try to use
    [*Dredd*](https://github.com/apiaryio/dredd), with Python hooks
    where necessary.  A Travis test badge is visible.

7.  We will also have integration tests but those are not implemented
    yet.  When implemented, new releases of individual must pass integration 
    tests to the satisfaction of the release manager of the integrated
    automated deployment.

8.  We’ll move towards some standard configuration option structures for
    consistency but we’re not there yet.

Logging:
--------

1.  Use the standard logging interfaces in
    [*go*](https://golang.org/pkg/log/) or
    [*Python*](https://docs.python.org/3.6/library/logging.html); we
    will eventually be moving to uniform logging with logstash + ELK,
    and using the existing standard logging interfaces will make that
    migration easier.

2.  Logs should be in structured format, and contain the following 
    fields:
    -   `timestamp` - string in iso8601 format
    -   `level` - info/warning/error etc, logger probably already gives you that
    -   `message` - human readable string
    -   `tag` - (optional) any short tag for this "kind" of log message, to make it easy to search for
    -   `service` - the service that's emitting the log message
    -   `version` - version of the service (which might be different than the API version - e.g., v1.3.2 of a service would still implement the v1 api)
    -   `host` - hostname of the service
    -   `pid` - (optional) - PID of the server
    -   `ip` - origin of the request being served
    -    Plus anything else that makes sense in the particular context being logged
    
    Either key-value pairs (`timestamp = "2018-08-21T17:26:56+00:00" level="INFO"`)
    or JSON (`{ "timestamp": "2018-08-21T17:26:56+00:00", "level": "INFO" ... }`) 
    are fine, they're both easy to parse.

3.  Log each API call. You will read online that that’s a bad idea, but
    we’re not Facebook or Twitter; our scale allows us to do that for
    the time being, so let’s take advantage of that for ease of
    tracing. Include OIDC token used with the call.

4.  In addition, log all error conditions, whether client- or
    server-side. Client-side errors (something the client or client
    dev can do something about) should be relayed in the error
    message; internal things the client can’t do anything about should
    simply be logged, with a 500 status + error code returned.

5.  Log all write operations or calls to other services, again for
    traceability.

Security:
---------

-   Handle OIDC token unless explicitly disabled
-   Eventually, hooks to authz service

Data
----

### Resource Identifiers: 

-   standard(ish) format/approach TBD

### Immutability:

-   In general, we want our data objects to be immutable for provenance; Updates increase the version number

References:
-----------

Much of the API style guide was taken from those found elsewhere and
simplified for our use case:

[https://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/](https://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/)

[https://github.com/paypal/api-standards/blob/master/api-style-guide.md](https://github.com/paypal/api-standards/blob/master/api-style-guide.md#http-methods)

[https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)

[https://apiguide.readthedocs.io/en/latest/index.html](https://apiguide.readthedocs.io/en/latest/index.html)
