
# HTTPBin API Documentation: GET `/get`

## 1. Overview

HTTPBin is a web service that provides endpoints for testing HTTP requests and responses.

The `GET /get` endpoint returns information about the incoming HTTP request in JSON format. This includes query parameters, request headers, the request's origin IP address, and the requested URL.

This endpoint is useful for understanding how HTTP GET requests work and how servers process incoming request information.

## 2. `GET /get`

### 2.1 Endpoint Details

| Property | Details |
|---|---|
| Endpoint | `https://httpbin.org/get` |
| HTTP Method | `GET` |
| Response Format | JSON |
| Authentication | Not required |
| Expected Success Status | `200 OK` |

### 2.2 Description

The `GET` method is used to retrieve information from a server.

When a client sends a GET request to this endpoint, HTTPBin returns details about the request it received.

The endpoint does not require specific query parameters. However, optional query parameters can be added to the URL and will be included in the response.

### 2.3 Query Parameters

Query parameters are values passed to an endpoint through the URL. They allow a client to send additional information as part of a request.

### Example

```url
https://httpbin.org/get?name=Niveditha&role=writer
```

The URL contains two query parameters:

| Parameter | Example Value | Description |
|---|---|---|
| `name` | `Niveditha` | Example name parameter |
| `role` | `writer` | Example role parameter |

**Understanding the URL syntax:**

- `?` indicates the beginning of the query parameters.
- `name=Niveditha` represents the first parameter and its value.
- `&` separates multiple query parameters.
- `role=writer` represents the second parameter and its value.

*Note: These are example parameters supplied by the client. They are not mandatory, predefined parameters of the HTTPBin endpoint.*

### 2.4 Request Headers

HTTP headers are key-value pairs that provide additional information about an HTTP request.

For example:

| Header | Description |
|---|---|
| `Accept` | Specifies the response media types the client can accept. |
| `Host` | Identifies the server being contacted. |
| `User-Agent` | Identifies the client software making the request. |

The headers may vary depending on the client. A browser and a command-line tool such as cURL may send different headers.

### 2.5 Example Request

The following command sends a GET request with two query parameters:

```bash
curl -i "https://httpbin.org/get?name=Niveditha&role=writer"
```

**Command explanation:**

- `curl` is a command-line tool used to send HTTP requests.
- `-i` includes the HTTP response headers in the output.
- The URL specifies the endpoint and its query parameters.

### 2.6 Response Structure

The endpoint returns a JSON object containing information about the received request.

The response generally contains the following fields:

| Field | Description |
|---|---|
| `args` | Contains the query parameters sent in the request. |
| `headers` | Contains the HTTP headers received by the server. |
| `origin` | Contains the source IP address observed by the server. |
| `url` | Contains the complete URL requested by the client. |

### 2.7 Example Response

The following is an illustrative example. Actual response values, especially headers and IP address, may vary.

```json

{
  "args": {
    "name": "Niveditha",
    "role": "writer"
  },
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/8.7.1"
  },
  "method": "GET",
  "origin": "CLIENT_IP",
  "url": "https://httpbin.org/get?name=Niveditha&role=writer"
}
```

**Understanding the response:**

1. `args` contains the two query parameters sent through the URL.
2. `headers` displays information received through the request headers.
3. `origin` identifies the source IP address observed by HTTPBin.
4. `url` displays the complete URL used for the request.

### 2.8 Response Status Codes

A successful request to this endpoint normally returns `200 OK`.

The following are common HTTP status codes that are useful to understand when testing APIs:

| Status Code | Meaning |
|---|---|
| `200 OK` | Request completed successfully. |
| `201 Created` | A resource was successfully created. |
| `400 Bad Request` | The server could not process the request due to a client-side error. |
| `401 Unauthorized` | Authentication is required or has failed. |
| `403 Forbidden` | The server refuses to authorize the request. |
| `404 Not Found` | The requested resource was not found. |
| `500 Internal Server Error` | The server encountered an unexpected error. |

*Note: The table above lists general HTTP status codes. It does not mean that every status code is returned by the `GET /get` endpoint.*

### 2.9 Testing Observations

During testing, the following behaviour should be observed:

- The endpoint accepts GET requests.
- Query parameters supplied in the URL are returned under the `args` field.
- Request headers are displayed under the `headers` field.
- The response is returned in JSON format.
- A successful request returns the HTTP status code `200 OK`.

### 2.10 Conclusion

The HTTPBin `GET /get` endpoint demonstrates how an HTTP GET request is processed and how information from the request is returned in a JSON response.

## 3. Testing an Unsuccessful Response: `404`

To understand how the API handles unsuccessful responses, the following endpoint was tested:

**Request:**

    curl -i "https://httpbin.org/status/404"

**Response:**

    HTTP/2 404

**Observations:**

- The server returned HTTP status code 404 (Not Found).
- The response body was empty, as indicated by `content-length: 0`.
- The request reached the server, but the endpoint deliberately returns an HTTP 404 (Not Found) status code to demonstrate how an unsuccessful HTTP response behaves.
- Unlike `/get`, this endpoint did not return a JSON object.

### Conclusion

The `/status/404` endpoint demonstrates how an HTTP server can return an unsuccessful HTTP status code without including a response body.


## 4. POST `/post`

### 4.1 Endpoint Details

| Property | Details |
|---|---|
| Endpoint | `https://httpbin.org/post` |
| HTTP Method | `POST` |
| Example Request Format | JSON |
| Response Format | JSON |
| Authentication | Not required |
| Expected Success Status | `200 OK` |

### 4.2 Description

The POST method is used to send data to a server through the request body.

The HTTPBin `POST /post` endpoint receives the data sent by the client and returns information about the incoming HTTP request.

It can be used to understand how data is submitted to an API and how the server processes request bodies.

### 4.3 Request Headers

HTTP headers provide additional information about the request.

For this example, the following header is used:

| Header | Value | Description |
|---|---|---|
| `Content-Type` | `application/json` | Indicates that the request body contains JSON data. |

The `Content-Type` header helps the server identify the format of the data being sent.

### 4.4 Request Body

A request body contains the data sent by the client to the server.

For this example, the following JSON data is sent:

```json
{
  "name": "Niveditha",
  "role": "Technical Writer"
}
```

The request body contains two key-value pairs:

| Key | Value |
|---|---|
| `name` | `Niveditha` |
| `role` | `Technical Writer` |

Unlike GET query parameters, this information is sent in the request body rather than being included in the URL.

### 4.5 Example Request

The following cURL command sends a POST request containing JSON data:

```bash
curl -i -X POST "https://httpbin.org/post" \
-H "Content-Type: application/json" \
-d '{"name":"Niveditha","role":"Technical Writer"}'
```

**Command explanation:**

- `curl` is a command-line tool used to send HTTP requests.
- `-i` displays the response headers along with the response body.
- `-X POST` specifies the HTTP method.
- `-H` adds a request header.
- `Content-Type: application/json` specifies the format of the request body.
- `-d` supplies the data to be sent in the request body.

### 4.6 Response Structure

The endpoint returns a JSON object containing information about the received request.

The important response fields include:

| Field | Description |
|---|---|
| `args` | Contains query parameters passed through the URL. |
| `data` | Contains the raw request body as received by the server. |
| `files` | Contains information about files sent in the request, if any. |
| `form` | Contains form data submitted through the request, if any. |
| `headers` | Contains the HTTP headers received by the server. |
| `json` | Contains the parsed JSON data received in the request body. |
| `origin` | Contains the source IP address observed by the server. |
| `url` | Contains the complete URL requested by the client. |

### 4.7 Example Response

The following is an illustrative example of the response:

```json
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Content-Length": "46",
    "Content-Type": "application/json",
    "Host": "httpbin.org",
    "User-Agent": "curl/8.7.1"
  },
  "method": "POST",
  "origin": "CLIENT_IP",
  "url": "https://httpbin.org/post",
  "data": "{\"name\":\"Niveditha\",\"role\":\"Technical Writer\"}",
  "files": {},
  "form": {},
  "json": {
    "name": "Niveditha",
    "role": "Technical Writer"
  }
}
```

**Understanding the response:**

1. `args` is empty because no query parameters were included in the URL.
2. `data` contains the raw request body.
3. `json` contains the parsed JSON data sent by the client.
4. `headers` displays the request headers received by the server.
5. `origin` identifies the source IP address observed by HTTPBin.
6. `url` displays the endpoint used for the request.

### 4.8 Response Status

A successful POST request to this endpoint normally returns:

| Status Code | Meaning |
|---|---|
| `200 OK` | The server successfully received and processed the request. |

### 4.9 Testing Observations

During testing, the following behaviour should be observed:

- The endpoint accepts POST requests.
- Data can be submitted through the request body.
- The `Content-Type` header specifies the format of the submitted data.
- The raw request body is returned under the `data` field.
- The parsed JSON object is returned under the `json` field.
- A successful request normally returns HTTP status code `200 OK`.

### 4.10 Conclusion

The HTTPBin `POST /post` endpoint demonstrates how data can be submitted to a server using an HTTP POST request.

Testing this endpoint provides an understanding of request bodies, JSON formatting, HTTP headers, and how servers receive and process submitted data.

