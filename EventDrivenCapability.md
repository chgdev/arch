# **Event Schema**
## Status: Proposal

This is a tentative standard for event payloads and metadata within the CHG common Kafka cluster.

This definition was arrived at via a combined meeting of Solution Architects and Site Reliability Engineers. This is not the final word, as it still needs review from a broader audience of engineers. Please comment here with ideas, critiques, etc. if you have any. Pull requests are welcome!

**Casing:** <a href="https://en.wikipedia.org/wiki/Camel_case">lower camelCase - aka dromedary case</a> (lowecase first letter, capital first letter of each subsequent word or acronym - Examples: jdeId, myAwsId).

**Format:** <a href="http://json.org">JSON - Javascript Object Notation</a>

**Definition:**
```json
{
    "correlation": {
        "id": "REQUIRED, UUID v4, generated or repeated by producer",
        "name": "Named parameter for correlation tracking"
    },
    "publisher":{
        "name": "REQUIRED, Name of the publishing app or service",
        "version": "REQUIRED, Version number of the publishing app or service",
        "instanceId": "The container ID of the publishing app or service",
        "schemaVersion":"REQUIRED, The publisher's schema version number"
    },
    "timestamp": "REQUIRED, Time of the change YYYY-MM-DDTHH:MM:SSZ (UTC)",
    "channel": "REQUIRED, Name of event channel, ie: job_updated, job_inserted, etc.",
    "replayId": "Replay Id of this event",
    "tags":[],
    "payload":{
        "data": {
            "id": "unique identifier of the record",
            "record": {}
        }
    },
    "delta":{
        "fieldName": {
        "before": "value of field before the change",
        "after": "value of field after the change"
        }
    },
    "operation":{
        "type": "delete"
    }
}
```
