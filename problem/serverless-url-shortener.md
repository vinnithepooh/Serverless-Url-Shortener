Building a serverless URL shortener application

PROBLEM STATEMENT

Build a serverless URL shortener application that creates short URLs, automatically expires them after a set duration, and tracks usage analytics using an event-driven architecture. Your solution must be implemented using Python for AWS Lambda functions and should use AWS Free Tier services. 

REQUIREMENTS

1.  REST API Endpoints :
        - POST /shorten
            ~ Accepts a JSON payload (e.g., { "url": https://example.com, "expiry_minutes": 1440 }), where the client specifies how  many minutes the URL should remain valid.
            ~ Authentication Requirement :Only authorized users can generate short URLs   
            ~ Generates a unique short identifier using your custom, authentication-based algorithm.
            ~ Stores the mapping between the short ID, the original URL, and its expiry timestamp in DynamoDB.
            ~ Returns the shortened URL (e.g., https://example.com/abc123).
        - GET /{short_id}
            ~ Retrieves the original URL from DynamoDB based on the provided short ID.
            ~ Checks the stored expiry timestamp; if the URL has expired, it returns an appropriate error (e.g., HTTP 410 Gone).
            ~ If valid, performs an HTTP redirect to the original URL.
            ~ Asynchronously triggers an event (via SQS) to log the click for analytics.
2.  Data Storage:
        - Use DynamoDB to persist URL mappings.
        - Each record must include the following attributes:
            ~ Original URL
            ~ Short code
            ~ Creation timestamp
            ~ Expiry timestamp (for auto-expiry)
        - Optionally, you may use a secondary table or an additional schema to track click analytics.
3.  Event-Driven Analytics:
        - Every time a short URL is accessed (GET endpoint), push a message to an SQS queue containing click metadata (e.g., timestamp, short ID, user-agent, IP address).
        - A separate Lambda function, triggered by SQS, should process these messages to update usage analytics (such as incrementing click counters or recording timestamps).
        - Note: Do not use DynamoDB Streams for event handling.
4.  Automatic URL Expiry:
        - Implement a mechanism that automatically marks URLs as expired based on their expiry timestamp. Checking expiry during each GET request.
        - Expired URLs should no longer redirect and must return an HTTP 410 Gone or similar error.
5.  Infrastructure as Code (IaC):
        - AWS CloudFormation to define and deploy your infrastructure.
        - Your CloudFormation template must include definitions for:
            ~ API Gateway endpoints
            ~ Lambda functions
            ~ DynamoDB tables
            ~ SQS queues and necessary IAM roles/policies
6.  Documentation and Design:
        - Provide a design document (or detailed in-code comments) that explains:
            ~ Your overall architecture (a diagram is a plus).
            ~ How the event-driven analytics with SQS is implemented.
            ~ The auto-expiry mechanism for short URLs.
            ~ Your custom, authentication-based short code generation algorithm (include a handwritten or whiteboard-style diagram ~ ~ with pseudocode or key logic, attached as a scanned image or high-quality photo).
            ~ Your error handling, testing strategy, and any trade-offs considered.