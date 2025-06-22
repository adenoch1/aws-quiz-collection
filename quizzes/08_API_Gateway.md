## AWS API Gateway Interview Questions & Detailed Answers

Below is a comprehensive set of 100+ questions and detailed answers designed to evaluate a candidate's deep understanding of AWS API Gateway.

---

### Section 1: Fundamentals

1. **What is AWS API Gateway?**
   **Answer:** AWS API Gateway is a fully managed service that simplifies the creation, publishing, maintenance, monitoring, and securing of REST, HTTP, and WebSocket APIs at any scale. It provides features like throttling, caching, authorization, and monitoring.

2. **What API types does API Gateway support?**
   **Answer:** API Gateway supports three API types: REST APIs, HTTP APIs (a newer, lower-cost option), and WebSocket APIs (for real-time two-way communication).

3. **Explain the difference between REST API and HTTP API in API Gateway.**
   **Answer:** HTTP APIs are optimized for lower latency and cost, support OIDC and JWT authorizers, and provide native CORS handling. REST APIs offer more features such as API keys, usage plans, AWS WAF integration, and transformations but at higher cost and latency.

4. **What is an API Gateway stage?**
   **Answer:** A stage is a logical reference to a lifecycle state of your API deployment (e.g., dev, test, prod). Each stage has its own endpoint and configuration settings like throttling and logging.

5. **How do you secure an API in API Gateway?**
   **Answer:** You can secure APIs using AWS IAM roles/policies, Lambda or Amazon Cognito authorizers, API keys, usage plans, resource policies, and AWS WAF.

6. **What is an API resource and method?**
   **Answer:** A resource is a URI path (e.g., `/users`), and a method is an HTTP verb on that resource (GET, POST, PUT, DELETE). Each method is configured with integration settings.

---

### Section 2: Integrations & Transformations

7. **Describe the types of integrations supported by API Gateway.**
   **Answer:** Integrations include AWS service integrations (Lambda, DynamoDB, SQS, SNS), HTTP proxy integrations, Mock integrations, VPC Link integrations (for privately hosted services), and AWS Passthrough integrations.

8. **What is a mapping template?**
   **Answer:** Mapping templates, written in Velocity Template Language (VTL), transform request and response payloads between client and backend formats.

9. **How do you pass binary data through API Gateway?**
   **Answer:** Enable binary support at the API level, specify binary media types, and Base64-encode payloads in mapping templates.

10. **Explain VPC Link in API Gateway.**
    **Answer:** VPC Link allows API Gateway to access HTTP/HTTPS resources inside a VPC (e.g., private ALBs, NLBs) securely without exposing them publicly.

11. **How do you implement request validation?**
    **Answer:** Define request models (JSON Schema) for query parameters, headers, and bodies. Enable request validation on methods to reject invalid requests before reaching the backend.

12. **What are integration timeouts, and how can you configure them?**
    **Answer:** Integration timeouts define how long API Gateway waits for a backend response. For REST APIs, the maximum is 29 seconds; for HTTP APIs, it defaults to 30 seconds but can be modified. You configure via integration settings.

---

### Section 3: Security & Authorization

13. **Explain how IAM authorization works in API Gateway.**
    **Answer:** When IAM authorization is enabled, clients sign requests with AWS Signature Version 4. API Gateway validates the signature against IAM policies to authorize access.

14. **What is a Lambda authorizer?**
    **Answer:** A Lambda authorizer is a Lambda function that API Gateway invokes to authorize each request. It returns IAM policy statements allowing or denying access and optional context data.

15. **How do you configure a Cognito user pool authorizer?**
    **Answer:** Create a Cognito user pool in AWS Cognito, then in API Gateway define a Cognito authorizer linking to the pool. API Gateway validates JWT tokens issued by the pool.

16. **What are resource policies in API Gateway?**
    **Answer:** Resource policies are JSON IAM policies attached to the API, restricting which principals or source IPs/VPC endpoints can invoke it.

17. **Describe usage plans and API keys.**
    **Answer:** Usage plans define rate and burst limits for API key holders. API keys identify clients; mapping keys to usage plans enforces throttling and quotas.

18. **How do you integrate AWS WAF with API Gateway?**
    **Answer:** Attach a WAF Web ACL to an API Gateway stage to protect against common web exploits and bots.

---

### Section 4: Performance & Scalability

19. **What is request throttling?**
    **Answer:** Throttling limits the number of requests per second (RPS) and burst capacity to protect backend systems. Configured at API, stage, or method level.

20. **Explain caching in API Gateway.**
    **Answer:** API Gateway supports response caching at the stage level. You define cache TTL and cache key parameters (headers, query params) to improve performance and reduce backend load.

21. **What is the maximum payload size API Gateway can handle?**
    **Answer:** REST APIs support up to 10 MB payload; HTTP APIs up to 6 MB. Larger payloads require alternative strategies (e.g., direct S3 upload).

22. **How does API Gateway handle high availability?**
    **Answer:** API Gateway is a regional service deployed across multiple AZs for fault tolerance. You can enable edge-optimized endpoints using CloudFront.

23. **What are edge-optimized APIs?**
    **Answer:** Edge-optimized APIs deploy to a CloudFront distribution, reducing latency for global clients by caching at edge locations.

24. **How do you implement canary deployments in API Gateway?**
    **Answer:** Use deployment stages with traffic shifting. Define a canary percentage in stage settings to route a portion of traffic to a new deployment for testing.

---

### Section 5: Monitoring & Troubleshooting

25. **How do you enable logging in API Gateway?**
    **Answer:** Enable CloudWatch Logs at the stage or method level. Specify a log group and configure log format for execution logs and access logs.

26. **What are access logs vs execution logs?**
    **Answer:** Access logs record request-level data (request time, client IP, latency). Execution logs provide detailed info about request processing, integration, mapping templates, and errors.

27. **How do you trace API Gateway using X-Ray?**
    **Answer:** Enable X-Ray tracing in the stage settings. API Gateway adds tracing headers and sends segments to X-Ray, allowing end-to-end tracing with integrated services.

28. **What metrics does API Gateway publish to CloudWatch?**
    **Answer:** Metrics include Count, 4XXError, 5XXError, Latency, IntegrationLatency, CacheHitCount, CacheMissCount, and more.

29. **How can you troubleshoot 502 errors in API Gateway?**
    **Answer:** A 502 error indicates a bad gateway. Common causes include integration misconfiguration, mapping template errors, or backend timeout. Check execution logs and integration responses.

30. **What is a stage variable?**
    **Answer:** Stage variables are key-value pairs accessible in mapping templates and integration endpoints, allowing parameterization across stages.

---

### Section 6: Advanced Features & Best Practices

31. **Explain the concept of private APIs.**
    **Answer:** Private APIs are accessible only from within a VPC using interface VPC endpoints (AWS PrivateLink). They are not exposed publicly.

32. **How do you set up API Gateway as a private endpoint?**
    **Answer:** Create a VPC endpoint of type Interface with service name for API Gateway, then configure the API as private and set required resource policies.

33. **Describe how you would secure a private API.**
    **Answer:** Use resource policies to restrict VPC endpoint IDs or CIDR ranges, and enforce IAM or Cognito authorizers as needed.

34. **What is the purpose of deployment IDs?**
    **Answer:** Deployment IDs uniquely identify a snapshot of the API configuration at the time of deployment. They are immutable.

35. **How does API Gateway handle multi-value query string parameters?**
    **Answer:** Enable multi-value query string support in the API settings. Integration receives parameters as arrays.

36. **What is CORS and how do you enable it?**
    **Answer:** Cross-Origin Resource Sharing allows browser clients from different origins to access APIs. Enable CORS by configuring OPTIONS method, setting Access-Control-Allow-Origin, headers, and methods.

37. **Explain usage of mutual TLS (mTLS) with API Gateway.**
    **Answer:** For private and regional REST APIs, API Gateway can enforce client certificate validation. Upload truststore in ACM; clients present certificates to authenticate.

38. **How do you handle API versioning in API Gateway?**
    **Answer:** Use stage variables to parameterize Lambda ARNs or integration URIs, or create separate stages/resources for each version (e.g., `/v1`, `/v2`).

39. **What are the limits of API Gateway?**
    **Answer:** Defaults: 10,000 RPS across all APIs per region for REST; 100,000 RPS for HTTP APIs. API count, model count, resource count, stages per API, etc. can be raised via support.

40. **How to manage large numbers of APIs in an organization?**
    **Answer:** Use Terraform/CloudFormation for IaC, AWS Service Catalog, API Gateway REST API import/export, and CI/CD pipelines. Create standardized templates.

---

### Section 7: WebSocket APIs

41. **What is API Gateway WebSocket API?**
    **Answer:** A managed service that enables real-time, two-way communication between clients and servers using WebSocket protocol, supporting persistent connections and message routing.

42. **How do you define routes in a WebSocket API?**
    **Answer:** Routes map message `action` values to integration targets, such as Lambda functions. Common routes include `$connect`, `$disconnect`, and custom routes.

43. **Explain the `$connect`, `$disconnect`, and `$default` routes.**
    **Answer:** `$connect` triggers when a client connects; `$disconnect` when a client disconnects; `$default` handles messages without a matching route key.

44. **How do you send messages from the backend to clients?**
    **Answer:** Use the `@connections` API—specifically the `POST /@connections/{connectionId}` endpoint—to push data to connected clients.

45. **How can you manage connection state in WebSocket APIs?**
    **Answer:** Store connection IDs and client context in a backend datastore (e.g., DynamoDB) during `$connect` and remove them on `$disconnect`.

46. **What are common use cases for WebSocket APIs?**
    **Answer:** Chat applications, live dashboards, multiplayer gaming, collaborative editing, and IoT telemetry.

47. **How do you throttle WebSocket API messages?**
    **Answer:** Enable per-route throttling in route settings, specifying message rate and burst limits to protect backend services.

48. **Can you use IAM authorizers with WebSocket APIs?**
    **Answer:** No. WebSocket APIs support Lambda authorizers and Cognito user pool authorizers, but not IAM authorization.

49. **How do you scale WebSocket APIs?**
    **Answer:** API Gateway auto-scales across AZs; ensure backend Lambdas and state stores (e.g., DynamoDB) have sufficient capacity.

50. **What are some performance considerations for WebSocket APIs?**
    **Answer:** Monitor connection count, message throughput, and backend integration latencies; use efficient data formats and batch messages when possible.

---

### Section 8: Developer Tools & SDKs

51. **How do you generate SDKs for API Gateway?**
    **Answer:** Use the API Gateway console or CLI (`aws apigateway get-sdk`) to generate SDKs for JavaScript, iOS, Android, and other platforms.

52. **What is the purpose of the `aws-api-gateway-cli-test` tool?**
    **Answer:** A community CLI tool that simplifies testing API Gateway endpoints by handling SigV4 signing for IAM-authorized REST APIs.

53. **How do you enable CORS in HTTP APIs using the console?**
    **Answer:** In route settings, toggle CORS, specify allowed origins, headers, and methods. HTTP APIs automatically add preflight responses.

54. **Explain the Swagger/OpenAPI integration with API Gateway.**
    **Answer:** Import an OpenAPI 3.0 or Swagger 2.0 definition into API Gateway to create APIs programmatically, including models, routes, and integrations.

55. **How can you export your API configuration?**
    **Answer:** Use the console or CLI (`aws apigateway get-export` for REST, `aws apigatewayv2 export-api` for HTTP/WebSocket) to export OpenAPI definitions.

---

### Section 9: CI/CD & Infrastructure as Code

56. **How do you deploy API Gateway using CloudFormation?**
    **Answer:** Define `AWS::ApiGateway::RestApi`, `AWS::ApiGateway::Resource`, `AWS::ApiGateway::Method`, `AWS::ApiGateway::Deployment`, and `AWS::ApiGateway::Stage` resources in a template.

57. **Explain canary deployments in CodeDeploy with API Gateway.**
    **Answer:** Use CodeDeploy’s traffic shifting to route a percentage of traffic to new API Gateway stage versions, enabling automated rollbacks on failure.

58. **How can you version APIs in Terraform?**
    **Answer:** Use distinct `aws_api_gateway_rest_api` resources with different names or use `stage_name` and `variables` to parameterize versions.

59. **Describe a CI/CD pipeline for API Gateway using AWS CodePipeline.**
    **Answer:** Pipeline stages: Source (e.g., CodeCommit), Build (e.g., CodeBuild to package Swagger), Deploy (CloudFormation deploy actions), Integration tests (CodeBuild).

60. **How do you automate API key rotation?**
    **Answer:** Use Lambda function triggered on a schedule (CloudWatch Events) that deletes old API keys and creates new ones, updating usage plans accordingly.

---

### Section 10: Real-World Scenarios & Troubleshooting

61. **A client reports high 429 errors after enabling caching—what could be wrong?**
    **Answer:** Likely missing cache key parameters, causing cache misses and backend overload. Ensure query strings and headers used as keys match client requests.

62. **Your API returns 403 errors for valid JWT tokens—how do you debug?**
    **Answer:** Check authorizer configuration: token audiences, issuer URLs, token validation regex. Inspect CloudWatch execution logs for authorizer errors.

63. **Latency suddenly spikes after deployment—how do you investigate?**
    **Answer:** Compare CloudWatch metrics for IntegrationLatency vs Latency. Enable X-Ray to trace slow integrations. Check backend resource metrics.

64. **Clients from a specific region get CORS failures—what’s the issue?**
    **Answer:** The `Access-Control-Allow-Origin` header may not include their origin. Verify CORS settings on both routes and OPTIONS methods globally.

65. **How to troubleshoot `VPC Link` `503` errors?**
    **Answer:** Check NLB health checks, security group, subnet configurations. Ensure NLB target groups are healthy and VPC Link is associated with correct subnets.

66. **You cannot deploy more than 10 stages—what options do you have?**
    **Answer:** Request a quota increase via AWS Support or consolidate environments using stage variables instead of separate stages.

67. **Why might `aws apigateway get-rest-apis` not list your API?**
    **Answer:** The API is HTTP/WebSocket type or in another region. Ensure correct service (`apigateway` vs `apigatewayv2`) and region context.

68. **What causes `Missing Authentication Token` errors?**
    **Answer:** Hitting an undefined resource/method. Verify endpoint URL (include stage and resource path) and HTTP verb match configured API.

69. **How do you migrate a REST API to an HTTP API?**
    **Answer:** Export OpenAPI definition, update integration and authorizer fields to HTTP API schema, then import into API Gateway v2 and test functionality.

70. **Describe handling large file uploads (>10 MB).**
    **Answer:** Use pre-signed S3 URLs: client uploads directly to S3, then invoke API Gateway to process metadata or trigger Lambdas via S3 events.

---

### Section 11: Comparison & Best Practices

71. **When choose HTTP API over REST API?**
    **Answer:** For low-latency, low-cost scenarios without need for API keys, request/response transformations, or AWS WAF.

72. **Best practices for securing REST APIs.**
    **Answer:** Use least-privilege IAM policies, enforce HTTPS, enable WAF, use Cognito or custom authorizers, rotate API keys, enable resource policies.

73. **How to reduce cold start impacts on Lambda integrations?**
    **Answer:** Use provisioned concurrency, keep Lambdas warm via scheduled invocations, or choose HTTP APIs which can integrate with provisioned concurrency targets.

74. **Structuring resources for microservices.**
    **Answer:** Use separate APIs per microservice or clear resource namespaces, shared authorizers, and aggregated metrics dashboards.

75. **Cost optimization tips for API Gateway.**
    **Answer:** Prefer HTTP APIs, enable caching, remove unused stages/resources, use custom domains only when needed, monitor usage patterns.

---

### Section 12: Limits & Deep Dive

76. **What is the soft limit for Models per REST API?**
    **Answer:** 50 models per REST API (can be increased via support).

77. **Default account-level throttle limit for REST APIs?**
    **Answer:** 10,000 RPS with a 5,000 RPS burst per region.

78. **Explain how multi-region deployments work.**
    **Answer:** Deploy separate API instances in multiple regions, use Route 53 latency-based or geolocation routing, and replicate stage configurations.

79. **How to implement request/response validation without models?**
    **Answer:** Use mapping templates with conditional VTL checks to validate payloads and return custom error messages.

80. **What are the costs associated with caching?**
    **Answer:** Charged per GB-hour of cache capacity provisioned and per million requests served from cache.

81. **Why might API Gateway return a 504 error?**
    **Answer:** Backend integration timed out. Check integration timeout settings and backend performance.

82. **Describe the role of `AuthorizerResultTtlInSeconds`.**
    **Answer:** Caches authorization results for the specified duration to reduce authorizer invocations and improve performance.

83. **When to use `passthroughBehavior`?**
    **Answer:** Determines how unmapped request bodies are handled: `WHEN_NO_MATCH`, `NEVER`, or `WHEN_NO_TEMPLATES` in mapping templates.

84. **Explain the impact of binary media types on performance.**
    **Answer:** Increases latency due to Base64 encoding/decoding overhead; use only when necessary for binary payloads.

85. **How to clean up stale deployments?**
    **Answer:** Use automation (CLI or SDK) to list and delete unused deployments and stages based on naming conventions and age.

86. **Deep dive: Velocity Template Language capabilities.**
    **Answer:** VTL allows loops, conditionals, variable assignment, and macro definitions to manipulate JSON, XML, and other payload formats.

87. **What is the role of `integrationResponses`?**
    **Answer:** Defines status code mappings, headers, and mapping templates for responses returned from integrations to clients.

88. **How to customize error responses globally?**
    **Answer:** Use Gateway Responses to define templates and headers for common errors like `ACCESS_DENIED`, `INVALID_API_KEY`, etc.

89. **Explain request context variables.**
    **Answer:** Variables like `$context.identity.sourceIp`, `$context.requestId`, and `$context.domainName` that provide request metadata in templates.

90. **What is the difference between `method.response.statusCode` and `integration.response.statusCode`?**
    **Answer:** The former is the HTTP status returned to client, the latter is status from the backend integration.

91. **How to use `stageVariables` in integration URIs?**
    **Answer:** Reference via `${stageVariables.varName}` in integration URI strings to parameterize endpoints.

92. **What are the implications of enabling detailed CloudWatch metrics?**
    **Answer:** Increases CloudWatch costs; provides granular data (per-method metrics) for deeper analysis.

93. **How do you handle deprecated endpoints?**
    **Answer:** Mark methods/resources as deprecated in SDKs, use documentation and stage variables to phase out old routes without breaking clients.

94. **What’s the difference between regional and edge-optimized endpoints?**
    **Answer:** Regional endpoints serve clients within the API’s region directly; edge-optimized use CloudFront for global caching and distribution.

95. **How to integrate API Gateway with AWS Step Functions?**
    **Answer:** Use `AWS::ApiGateway::Integration` with `AWS` service type, specifying `states:StartExecution` action, passing input via mapping templates.

96. **Describe setup for custom domain with mutual TLS.**
    **Answer:** Configure custom domain in ACM, upload truststore for client certs, enable mTLS on the domain in API Gateway settings.

97. **How to use SDK execution logging?**
    **Answer:** Enable tracing flags in generated SDKs to capture request/response logs for debugging client-side issues.

98. **Explain the difference between `httpMethod` and `proxy` integration.**
    **Answer:** `httpMethod` requires explicit method and integration settings per route; `proxy` passes whole request to backend without transformations.

99. **What are `authorizerResultTtlInSeconds` best practices?**
    **Answer:** Balance security and performance: shorter TTL for high-security APIs, longer TTL for low-risk to reduce authorizer invocations.

100. **How do you implement green/blue deployments with API Gateway?**
     **Answer:** Use separate stages or domain mappings for blue and green, switch traffic via Route 53 weighted records or stage variables.

101. **What future enhancements would you like to see in API Gateway?**
     **Answer:** Suggestions may include native GraphQL support, improved VTL debugging tools, lower cold-starts for Lambda integrations, or built-in multi-region orchestration.
