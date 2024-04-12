---
sidebar_label: "API Specification"
---

# Build Server API Documentation

This document provides details about the Build Server API, which allows users to interact with compiler configurations and manage compilation jobs.

## Table of Contents

1. [Endpoints](#endpoints)
    - [GET /compiler](#get-compiler)
    - [POST /compiler/job](#post-compilerjob)
    - [GET /compiler/job/\{uid\}](#get-compilerjobuid)
2. [Data Structures](#data-structures)
3. [OpenAPI Specification](#openapi-specification)

## Overview

The Build Server API provides endpoints to retrieve compiler configurations and manage compilation jobs. It is built on the OpenAPI 3.1.0 specification.

## Endpoints

### GET /compiler

Retrieve compiler configuration.

- **Summary**: GET compiler configuration
- **Operation ID**: get-compiler
- **Description**: Returns a JSON object containing the queue size and available compiler versions.
- **Responses**:
    - `200 OK`: Successful response. Returns a JSON object with compiler configuration details.
    - `404 Not Found`: If the resource is not found.
- **Example Request**:
```http
  GET /compiler HTTP/1.1
  Host: localhost:3000
```
- **Example Response**:
```json
{
    "availableCompilerVersions": [
        "1.77.0",
        "1.76.0",
        "1.75.0",
        "1.74.1"
    ],
    "queueSize": 0
}
```

-----


### POST /compiler/job
Create a new compilation job.

- **Summary**: Create new compilation job
- **Operation ID**: post-compiler-job
- **Description**: Submits a new compilation job with the specified compiler version and source code.
- **Responses**:
    - `200 OK`: Successful request. 
    - `400 Bad Request`: If the request is malformed. 
    - `429 Too Many Requests`: If there are too many requests. 
    - `500 Internal Server Error`: If there is an internal server error.
- **Request Body**:
```json
{
  "version": "1.76.0",
  "warningsOff": false,
  "buildType": "release",
  "optimization": "0",
  "artifacts": "all",
  "source": [
    {
      "name": "Cargo.toml",
      "path": "/",
      "content": "...base64"
    },
    {
      "name": "lib.rs",
      "path": "/",
      "content": "...base64"
    }
  ]
}
```
- **Example response**:
```json
{
    "jobId": "877bcf4a-6013-41c4-81b8-90343dff0fc4",
    "version": "1.76.0",
    "startedAt": "2024-04-12T08:40:37.597500968",
    "status": "New"
}
```

----


###  GET /compiler/job/\{uid\}
Retrieve job status by UUID.

- **Summary**: Get job status
- **Operation ID**: get-compiler-job-uid
- **Description**: Returns the status of a compilation job identified by its UUID.
- **Parameters**:
  - **uid (path)**: UUID of the job (required)
- **Responses**:
  - **200 OK**: Successful response. Returns a JSON object with job details.
  - **404 Not Found**: If the job with the specified UUID is not found.
- **Example Request**:
```http
    GET /compiler/job/123e4567-e89b-12d3-a456-426614174000 HTTP/1.1
    Host: localhost:3000
```
- **Example Response**:
```json
{
    "jobId": "877bcf4a-6013-41c4-81b8-90343dff0fc4",
    "version": "1.76.0",
    "startedAt": "2024-04-12T08:40:37.597500968",
    "status": "Started",
    "result": null
}
```

### **Data Structures**:

Can be found in build-server implementation [here](https://github.com/Smart-Squid-IDE/build-server): 

**Create Job (request)**:
```rust
pub struct CompilerJobCreateDTO {
    pub version: String,
    pub source: Vec<JobFile>,
    #[serde(rename = "warningsOff")]
    pub warnings_off: bool,
    #[serde(rename = "buildType")]
    pub build_type: BuildType,
    pub optimization: OptimisationLevel,
    pub artifacts: BuildArtifactsType,
}
```

```rust
pub struct JobFile {
    pub name: String,
    pub path: String,
    pub content: String,//Base64 encoded
}
```

```rust
pub enum BuildType {
    #[serde(rename = "debug")]
    Debug,
    #[serde(rename = "release")]
    Release,
}
```

```rust
pub enum OptimisationLevel {
    #[serde(rename = "0")]
    L0,
    #[serde(rename = "1")]
    L1,
    #[serde(rename = "2")]
    L2,
    #[serde(rename = "3")]
    L3,
    #[serde(rename = "4")]
    L4,
    #[serde(rename = "S")]
    LS,
    #[serde(rename = "Z")]
    LZ,
}
```

```rust
pub enum BuildArtifactsType {
    #[serde(rename = "all")]
    All,
    #[serde(rename = "none")]
    None,
}
```

**Read Compile job by UID (response)**:

```rust
pub struct CompilerJobStatusDTO {
    #[serde(rename = "jobId")]
    pub job_id: Uuid,
    pub version: String,
    #[serde(rename = "startedAt")]
    pub started_at: NaiveDateTime,
    pub status: CompilerJobStatus,
    pub result: Option<Vec<JobFile>>,
}
```

### **OpenAPI Specification**:

```yaml
openapi: 3.1.0
x-stoplight:
  id: delgrqeysw84p
info:
  title: Build Server
  version: '1.0'
servers:
  - url: 'http://localhost:3000'
paths:
  /compiler:
    get:
      summary: GET compiler configuration
      tags: []
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  queueSize:
                    type: integer
                    x-stoplight:
                      id: 30i2ujxtisu8i
                    example: 2
                    default: 0
                    minimum: 0
                    maximum: 100
                  availableCompilerVersions:
                    type: array
                    x-stoplight:
                      id: ch4lpqfc9jnc6
                    items:
                      x-stoplight:
                        id: k98xj5bg0dqh3
                      type: string
                      minLength: 0
                      maxLength: 10
                      example: 1.76.1
                      pattern: '^\d\.\d{2}\.\d$'
                required:
                  - queueSize
                  - availableCompilerVersions
              examples:
                Example 1:
                  value:
                    availableCompilerVersions:
                      - 1.70.0
                      - 1.71.0
                      - 1.72.0
                      - 1.73.0
                    queueSize: 3
      operationId: get-compiler
      description: Response contains list of supported compiler (rust) versions
  /compiler/job:
    post:
      summary: Create new compilation job
      operationId: post-compiler-job
      responses:
        '200':
          description: OK
        '400':
          description: Bad Request
        '429':
          description: Too Many Requests
        '500':
          description: Internal Server Error
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                version:
                  type: string
                  x-stoplight:
                    id: 3olp75zc6q31o
                source:
                  type: string
                  x-stoplight:
                    id: 6qmldalh7mk61
                  description: Project sources encoded as ??? (TBD)
              required:
                - version
                - source
    parameters: []
  '/compiler/job/{uid}':
    parameters:
      - schema:
          type: string
        name: uid
        in: path
        required: true
        description: uuid4 of the job
    get:
      summary: Get job status
      tags: []
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  job_id:
                    type: string
                    x-stoplight:
                      id: 1vxc8lc4q9jxz
                    format: uuid
                  started_at:
                    type: string
                    x-stoplight:
                      id: 4pd8vt72qm5ik
                    format: date-time
                  status:
                    type: string
                    x-stoplight:
                      id: qlqh1r5s4u1bq
                    enum:
                      - NEW
                      - STARTED
                      - FINISHED
                      - FAILED
                      - COMPILATION_FAILED
        '404':
          description: Not Found
      operationId: get-compiler-job-uid
components:
  schemas: {}
```
