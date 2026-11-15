
# HTTPBin API Documentation

## Overview

This project documents selected HTTPBin API endpoints to demonstrate fundamental concepts of HTTP communication, API requests, and responses.

HTTPBin is a web service that provides endpoints for testing HTTP requests and understanding how servers process incoming requests.

The documentation was created through practical testing using cURL and the HTTPBin service.

## Objectives

- Understand HTTP methods such as GET and POST.
- Learn how query parameters are passed through URLs.
- Understand request headers and request bodies.
- Explore JSON request and response formats.
- Interpret HTTP response status codes.
- Test successful and unsuccessful HTTP responses.

## Endpoints Documented

| Endpoint | Method | Purpose |
|---|---|---|
| `/get` | GET | Returns information about the incoming request, including query parameters and headers. |
| `/post` | POST | Receives submitted data and returns information about the request. |
| `/status/404` | GET | Demonstrates an HTTP 404 Not Found response. |

## Tools and Technologies

- **HTTPBin:** Used as the API testing service.
- **cURL:** Used to send HTTP requests from the command line.
- **JSON:** Used to understand structured request and response data.
- **Markdown:** Used to write and organise the documentation.

## Project Structure

```text
api-documentation/
│
├── README.md
│
└── api-reference.md
```

## How to Test the Endpoints

Make sure you have an internet connection and cURL installed.

### 1. Test GET

```bash
curl -i "https://httpbin.org/get?name=Niveditha&role=writer"
```

### 2. Test POST

```bash
curl -i -X POST "https://httpbin.org/post" \
-H "Content-Type: application/json" \
-d '{"name":"Niveditha","role":"Technical Writer"}'
```

### 3. Test HTTP 404

```bash
curl -i "https://httpbin.org/status/404"
```

The `-i` option displays the HTTP response headers along with the response body.

## Key Learnings

Through this exercise, I explored:

- The difference between GET and POST requests.
- The use of query parameters and request bodies.
- The purpose of HTTP request headers.
- The difference between raw request data and parsed JSON.
- The structure of HTTP responses.
- The interpretation of HTTP status codes.

## Detailed Documentation

For endpoint descriptions, example requests, response structures, and testing observations, refer to [API Reference](api-reference.md).

## Reference

- [HTTPBin Official Website](https://httpbin.org/)
- [HTTPBin Documentation](https://httpbin.org/#/)
