---
title: "Bulletproof API"
layout: post
date: 2025-02-24 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- api design
category: blog
author: devdiary
description: Nội dung như title
---

Tổng hợp các phương pháp bảo vệ và thiết kế API hiệu quả 

# Signature

To prevent data in API interfaces from being tampered with, it is often necessary to implement signatures for API interfaces.

The API requester concatenates the request parameters, timestamp, and secret key into a string, then generates a signature (sign) using an MD5 or other hash algorithm.

This sign is then included in the request parameters or headers and sent to the API.

On the API gateway service side, the gateway retrieves the sign value, then uses the same request parameters, timestamp, and secret key to generate another sign using the same MD5 algorithm. It then compares the two sign values.

If the two sign values match, the request is considered valid, and the API gateway service forwards the request to the appropriate business system.
If the two sign values do not match, the API gateway service returns a signature error.
Why include a timestamp in the signature?
To enhance security and prevent the same request from being repeatedly used, a timestamp is included in the signature. This also reduces the chances of the secret key being cracked. Each request must have a reasonable expiration time, such as 15 minutes.

Thus, a request remains valid for 15 minutes. If it exceeds 15 minutes, the API gateway service returns an error indicating that the request has expired.

Currently, there are two ways to generate the secret key used in signatures:

Fixed Private Key (privateKey): Both parties agree on a fixed value as the secret key.
AK/SK Key Pair: The API provider assigns an AK/SK pair. The SK is used as the secret key in the signature, while the AK is sent as an accessKey in the request header. The API provider retrieves the SK using the AK and generates a new sign for validation.

# Encryption
In some cases, API interfaces transmit highly sensitive data, such as user login passwords, bank card numbers, and transfer amounts. Exposing such parameters in plaintext over the public internet is extremely risky.

To mitigate this risk, encryption must be implemented.

For example, in a user registration interface, after a user enters their username and password, the password needs to be encrypted.

A common approach is to use AES symmetric encryption:

On the front end, the user’s password is encrypted using a public key.
The registration API then decrypts the password using a secret key, performs necessary business validations, and re-encrypts it using a different encryption method before storing it in the database.

# IP Whitelisting
To further enhance API security, IP whitelisting can be implemented. Even if the signature or encryption mechanisms are compromised, attackers would still need to request the API from an approved IP address.

The solution is to restrict API requests based on IP addresses, allowing only requests from whitelisted IPs.

If an API request originates from a whitelisted IP, it is processed normally.
If the request is from a non-whitelisted IP, access is denied immediately.
IP whitelisting can be enforced at the API gateway level.

However, internal application servers within the company may also be compromised, allowing attackers to send API requests from within the network.

To counter this, a web firewall such as ModSecurity should be deployed for additional protection.

# Rate Limiting
When a third-party platform calls your API, its request frequency may be uncontrollable.

If a third party suddenly sends a massive volume of requests concurrently, it could overwhelm your API service and cause downtime.

Thus, rate limiting must be implemented.

There are three common rate-limiting strategies:

Limit requests per IP
Example: A single IP can make at most 10,000 requests per minute.

Limit requests per API endpoint
Example: A single IP can make at most 2,000 requests per minute to a specific API endpoint.

Limit requests per user (AK/SK)
Example: A single AK/SK user can make at most 10,000 API requests per minute.

In real-world applications, rate limiting can be implemented using Nginx, Redis, or API gateways.

# Parameter Validation
API interfaces must enforce parameter validation, such as:

Checking whether required fields are empty.
Verifying field types.
Validating field lengths.
Ensuring enumerated values are correct.
This helps filter out invalid requests early in the process, preventing unnecessary processing.

For example, if a request attempts to insert data where a field exceeds the maximum allowed length, the database would throw an error. However, such validation should be handled before the database operation to save system resources.

Examples of validation pitfalls:

Incorrect amount values: If a field is supposed to store positive numbers but negative values are accepted, this could cause unexpected losses.
Invalid status values: If the system doesn’t validate the status field and an unknown value is received, it could corrupt the database.
Common validation frameworks
In Java, the most commonly used validation framework is Hibernate Validator, which includes annotations such as:

@Null
@NotEmpty
@Size
@Max
@Min
These annotations make data validation straightforward.

For date fields and enum fields, custom annotations may be needed for validation.

Trường hợp một param dạng string lưu thông tin email của user nhập vào nếu không được validate trước khi lưu vào database. Email hoàn toàn có thể truyền vào dạng một mã js, khi server response về cho client, đoạn js này có thể được kích hoạt để thực hiện một chức năng nào đó. User có thể bị tấn công.

# Unified Response Format
I have encountered APIs where different responses have inconsistent JSON formats, for example:

Normal response:

{
  "code": 0,
  "message": null,
  "data": [{ "id": 123, "name": "abc" }]
}
Signature error response:

{
  "code": 1001,
  "message": "Signature error",
  "data": null
}
Permission denied response:

{
  "rt": 10,
  "errorMgt": "No permission",
  "result": null
}
Why is this a problem?
When an API returns responses in different formats, it creates unnecessary confusion for integrators.

This issue often arises when:

The API gateway has one response format.
The business system has a different response format.
If an API gateway error occurs, one format is returned.
If a business system error occurs, another format is returned.

The solution: Standardizing response structures
The API gateway should enforce a unified response format.

If a business system throws a RuntimeException containing an error message, the API gateway should catch this exception and return it in a standardized format.

# Unified Exception Handling
API interfaces must implement consistent exception handling.

Have you ever encountered a situation where an API request fails due to a database issue (e.g., a missing table or an SQL syntax error), and the response directly returns raw SQL errors?

Some poorly designed APIs might expose exception stack traces, database details, error codes, and even line numbers in the response.

This is a serious security risk.

Malicious actors could exploit this information to conduct SQL injection attacks or directly exfiltrate the database, leading to system breaches.

Solution: Mask sensitive error details
Instead of exposing raw error messages, all API exceptions should be converted into a standard error response, such as:

{
  "code": 500,
  "message": "Internal Server Error",
  "data": null
}
The code field is 500 (standard HTTP error code for server errors).
The message field is a generic error message without exposing internal system details.
Internal logging for debugging
To debug issues, internal logs should still record:

Full exception stack traces
Database error details
The exact error line number
This ensures that internal teams have the necessary information to diagnose issues without exposing sensitive data to external users.

Gateway-level exception interception
Exception handling can be enforced at the API gateway level, ensuring all error responses follow a consistent, sanitized format.

# Request Logging
Request logs are crucial when diagnosing issues with API calls, especially for third-party platforms.

To ensure traceability, log the following API request details:

Request URL
Request parameters
Request headers
HTTP method
Response data
Response time
Using traceId for request tracing
A traceId should be included in the logs, allowing all related logs for a specific request to be linked together. This helps filter out irrelevant logs when troubleshooting.

Making logs accessible to third parties
In some cases, third-party platforms may also need access to request logs.

To facilitate this:

Store logs in a database such as MongoDB or Elasticsearch.
Develop a UI dashboard that allows third-party users to search and view logs.
This enables self-service debugging, reducing the need for external users to contact support for minor issues.

# Idempotency Design
A third-party platform may send duplicate API requests within a very short time, often due to:

Bugs in their system, causing repeated calls.
Retry mechanisms when an API response is delayed or fails.
If an API does not handle idempotency, duplicate requests could lead to duplicate records, causing data inconsistencies.

Solution: Ensure duplicate requests do not create duplicate records
If the same API request is received multiple times within a short period:

The first request processes normally and inserts data.
Subsequent identical requests do not insert new data but still return a success response.
Implementation approaches
Unique constraints in the database

Use a unique index on request parameters to prevent duplicate entries.
Using Redis for deduplication

Store a requestId in Redis along with request parameters.
If the same requestId is received again, reject the request.

# Limiting Record Count in Batch APIs
For batch processing APIs, it's critical to limit the number of records per request.

Why?

Allowing too many records per request can cause API timeouts and instability.
Large payloads increase server load and reduce overall API performance.
Recommended limits
A single API request should allow a maximum of 500 records.
If more than 500 records are sent, the API should return an error.
This limit should be configurable and agreed upon with third-party users before going live.

Pagination for large queries
If an API needs to return a large dataset, implement pagination instead of returning all data in one request.

# Load Testing
Before launching an API, load testing is essential to understand its QPS (queries per second) limits.

Even if rate limiting is in place, you need to verify whether the API can actually handle the expected load.

Example scenario
The API rate limit is set to 50 requests per second.
However, actual server capacity may only handle 30 requests per second.
This means even with rate limiting, the API could still crash.
Load testing tools
JMeter
Apache Bench (ab)
By running stress tests, you can determine how many server nodes are needed to maintain stable performance.

# Asynchronous Processing
Most APIs are synchronous, meaning requests are processed immediately, and responses are returned in real-time.

However, some complex operations, particularly batch processing, can take too long to execute.

Solution: Convert long-running tasks into asynchronous processing
Send an MQ (message queue) message

The API immediately returns success after enqueuing the task.
A separate message consumer processes the task asynchronously.
Let third parties check the processing result

Callback approach: Notify the third-party API when processing is complete (common in payment APIs).
Polling approach: The third party repeatedly calls a status-check API to track progress.

# Data Masking (Data Redaction)
Some API responses contain sensitive user data, such as:

Phone numbers
Bank card numbers
If this information is exposed without masking, it increases the risk of data leaks.

Solution: Apply data masking
For example, mask bank card numbers:

Original: 5196123456781234
Masked: 5196****1234
Even if the data is leaked, it remains partially protected, reducing security risks.

# Comprehensive API Documentation
Well-documented APIs reduce integration effort and minimize miscommunication.

API documentation should include:
API URL
HTTP method (e.g., GET, POST)
Request parameters & field descriptions
Response format & field descriptions
Error codes & messages
Encryption & signature examples
Example requests
Additional requirements (e.g., IP whitelisting)
Standardizing naming conventions
To maintain consistency:

Use camelCase for field names.
Standardize field types & lengths (e.g., id as Long, status as int).
Define a uniform time format (e.g., yyyy-MM-dd HH:mm:ss).
Documentation should also specify AK/SK key usage and API domain names.

# Request Methods
APIs support various request methods, including:

GET
POST
PUT
DELETE
Choosing the right method
GET: Suitable for read-only requests (when no parameters need to be sent).
POST: Recommended when parameters are required (less prone to issues).
Why use POST over GET?

POST allows easier parameter expansion
In Feign API calls, adding new parameters doesn’t require modifying existing code.
GET has a URL length limit
Maximum 5000 characters, while POST has no limit.

# Request Headers
Certain parameters, such as authentication tokens or traceId, should be sent via request headers instead of query parameters.

For example:

Instead of adding traceId as a URL parameter in every API request,
Clients should send it in the request header.
The server can then extract traceId using an interceptor.

# Batch Processing
When designing an API, whether it's for querying, adding, updating, or deleting data, batch processing should always be considered.

Why is batch processing important?
Many scenarios require querying multiple records at once.
For example, retrieving order details:

If an API only supports fetching one order at a time, a separate API call is needed for each order.
If an API supports batch retrieval, a single request can retrieve multiple orders, improving efficiency.
Similarly, when adding data:

If an API only supports adding one record per request, and a batch job needs to insert 1,000 records, this would require 1,000 separate API calls.
Instead, a batch insert API allows submitting all 1,000 records in one request, reducing overhead.
Design recommendation:
Where feasible, APIs should support batch operations instead of handling single records only.
This makes APIs more generic and scalable for different business needs.

# Single Responsibility Principle
Some API designs are overly complex, supporting numerous conditions in a single request.
As a result:

The service layer (Service class) contains excessive if...else statements.
The response model includes all possible fields, leading to an overwhelming number of properties.
The problem with overly complex APIs:
Difficult to maintain: After a year, even the original developer may struggle to recall which fields are required for specific scenarios.
High risk of breaking changes: Modifying logic for Scenario A may inadvertently impact Scenario B.
Solution: Follow the Single Responsibility Principle
Instead of a one-size-fits-all API, split APIs into smaller, scenario-specific endpoints.

**Example:**
For an order placement API, there are two platforms (Web & Mobile) and two ordering methods (Standard & Fast).

Instead of designing one API to handle everything, separate them:

Web platform APIs
/web/v1/order/create
/web/v1/order/fastCreate
Mobile platform APIs
/mobile/v1/order/create
/mobile/v1/order/fastCreate
This results in four distinct APIs, each dedicated to a specific use case.
Benefits:

Business logic remains clear.
APIs are easier to maintain.
There's less risk of unintended side effects when making changes.
Conclusion
By following these best practices, API interfaces become more secure, scalable, and maintainable. These principles help:

Prevent security vulnerabilities (e.g., unauthorized access, SQL injection).
Optimize performance (e.g., rate limiting, batch processing).
Improve developer experience (e.g., standardized responses, clear API documentation).
Whether designing APIs for internal systems or third-party integrations, adopting these best practices ensures a more robust and future-proof architecture.

# Reference
https://leapcell.io/blog/bulletproof-api-design

