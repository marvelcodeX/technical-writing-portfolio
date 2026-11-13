
# HTTPBin API Documentation: GET `/get`

## 1. Overview

HTTPBin is a web service that provides endpoints for testing HTTP requests and responses.

The `GET /get` endpoint returns information about the incoming HTTP request in JSON format. This includes query parameters, request headers, the request's origin IP address, and the requested URL.

This endpoint is useful for understanding how HTTP GET requests work and how servers process incoming request information.

## 2. Endpoint Details

| Property | Details |
|---|---|
| Endpoint | `https://httpbin.org/get` |
| HTTP Method | `GET` |
| Response Format | JSON |
| Authentication | Not required |
| Expected Success Status | `200 OK` |

## 3. Description

The `GET` method is used to retrieve information from a server.

When a client sends a GET request to this endpoint, HTTPBin returns details about the request it received.

The endpoint does not require specific query parameters. However, optional query parameters can be added to the URL and will be included in the response.

## 4. Query Parameters

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

## 5. Request Headers

HTTP headers are key-value pairs that provide additional information about an HTTP request.

For example:

| Header | Description |
|---|---|
| `Accept` | Specifies the response media types the client can accept. |
| `Host` | Identifies the server being contacted. |
| `User-Agent` | Identifies the client software making the request. |

The headers may vary depending on the client. A browser and a command-line tool such as cURL may send different headers.

## 6. Example Request

The following command sends a GET request with two query parameters:

```bash
curl -i "https://httpbin.org/get?name=Niveditha&role=writer"
```

**Command explanation:**

- `curl` is a command-line tool used to send HTTP requests.
- `-i` includes the HTTP response headers in the output.
- The URL specifies the endpoint and its query parameters.

## 7. Response Structure

The endpoint returns a JSON object containing information about the received request.

The response generally contains the following fields:

| Field | Description |
|---|---|
| `args` | Contains the query parameters sent in the request. |
| `headers` | Contains the HTTP headers received by the server. |
| `origin` | Contains the source IP address observed by the server. |
| `url` | Contains the complete URL requested by the client. |

## 8. Example Response

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
  "origin": "CLIENT_IP",
  "url": "https://httpbin.org/get?name=Niveditha&role=writer"
}
```

**Understanding the response:**

1. `args` contains the two query parameters sent through the URL.
2. `headers` displays information received through the request headers.
3. `origin` identifies the source IP address observed by HTTPBin.
4. `url` displays the complete URL used for the request.

## 9. Response Status Codes

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

## 10. Testing Observations

During testing, the following behaviour should be observed:

- The endpoint accepts GET requests.
- Query parameters supplied in the URL are returned under the `args` field.
- Request headers are displayed under the `headers` field.
- The response is returned in JSON format.
- A successful request returns the HTTP status code `200 OK`.

## 11. Conclusion

The HTTPBin `GET /get` endpoint demonstrates how an HTTP GET request is processed and how information from the request is returned in a JSON response.

Testing this endpoint provides a basic understanding of HTTP methods, query parameters, request headers, JSON responses, and HTTP status codes.
