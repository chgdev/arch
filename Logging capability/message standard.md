# Logging Message Standard 1.0.0

This document describes the standard format for log messages at CHG.

All log messages are in [JSON](https://www.json.org/json-en.html) format and conform to the [Elastic Common Schema version 1.4.0](https://www.elastic.co/guide/en/ecs/1.4/index.html). As the ECS is a permissive schema, messages may include additional properties beyond those established in the schema and still be considered conformant.

The ECS 1.4.0 standard requires only the following fields appear in all events:

- `@timestamp`
- `ecs.version`

All other fields are optional in the ECS, but if present, must conform to the specifications set forth in that standard.

CHG's logging message standard extends ECS 1.4.0 in the following ways:

- The following fields are required for all events:
  - `env`: Extension property declaring the environment: one of `production`, `staging`, or `development`
  - `message`
  - `host.ip`
  - `log.level`
  - `service.id`
  - `service.version`
  - `tracing.trace.id`
- All fields of type `Date` are formatted according to [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) (`YYYY-MM-DDTHH:mm:ss.sssZ`). (This is the format output by the JavaScript `Date.toISOString()` function.)
- The `container.id` and `container.image.name` fields, while not required, are strongly recommended.
- The `error` field is required if the message documents an error encountered by the application. In this scenario:
  - The `error.message` and `error.type` fields are required.
  - It is strongly recommended to include a stack trace if possible, recorded in the `error.stack_trace` field.
  - If a stack trace is available, it is recommended that the `error.stack_hash` field be included. This is an extension to the ECS standard which stores a hex-encoded hash of the stack trace. If the stack includes entries which originate from dynamically-generated code which may vary over time without code changes (for example, a Java class generated at runtime which has a randomized name), those entries in the stack trace should be excluded from the hash computation. No particular hash algorithm is required.

## Example Message
```json
{
  "@timestamp": "2020-02-07T19:56:41.265Z",
  "container": {
    "id": "e8c620a91",
    "image": {
      "name": "foo-service-1.0.0"
    }
  },
  "ecs": {
    "version": "1.4.0"
  },
  "env": "production",
  "error": {
    "message": "Expected \"foo\", received \"bar\"",
    "type": "Error",
    "stack_trace": "at <anonymous>:47",
    "stack_hash": "dce10d59"
  },
  "host": {
    "ip": "192.168.137.31"
  },
  "log": {
    "level": "error"
  },
  "message": "Expected \"foo\", received \"bar\"",
  "service": {
    "id": "chg-foo-service",
    "version": "1.0.0"
  },
  "tracing": {
    "trace": {
      "id": "8b8e1cf2-6d74-4c09-99e1-54551944ab45"
    }
  }
}
```

## Implementations
- The [`@chg/ecs-validate` package](https://registry.common.aws.chgit.com/-/web/detail/@chg/ecs-validate) provides validation of messages against the ECS 1.4.0 standard for Node applications. At this writing, it does not currently provide validation of CHG's extension to the ECS.
