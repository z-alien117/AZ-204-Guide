# Dark souls 3 items Advanced Policies

## Control Flow Policy: Conditional Item Retrieval

> In this example, we want to conditionally retrieve item information based on a request parameter, such as the item category. We'll return different item data depending on the condition.

```xml
<policies>
  <inbound>
    <base />
    <choose>
      <when condition="@(context.Request.Url.Query["category"] == "weapons")">
        <!-- Retrieve and return weapon items -->
        <forward-request destination="https://api.dark-souls-3/items/weapons" />
      </when>
      <when condition="@(context.Request.Url.Query["category"] == "armor")">
        <!-- Retrieve and return armor items -->
        <forward-request destination="https://api.dark-souls-3/items/armor" />
      </when>
      <otherwise>
        <!-- Return an error response for unsupported category -->
        <set-status code="400" reason="Bad Request" />
        <set-body>{"error": "Unsupported category."}</set-body>
      </otherwise>
    </choose>
  </inbound>
</policies>
```

> This policy checks the "category" query parameter and forwards the request to the appropriate endpoint or returns an error for unsupported categories.

## Forward Request Policy: Routing to Different Services

In this scenario, we want to route requests to different backend services based on the item type requested.

```xml
<policies>
  <inbound>
    <base />
  </inbound>
  <backend>
    <base />
    <choose>
      <when condition="@(context.Request.Url.Path.Contains("ds3"))">
        <!-- Forward requests for weapons to the weapons backend service -->
        <forward-request destination="https://ds3-backend-api.com" />
      </when>
      <when condition="@(context.Request.Url.Path.Contains("ds2"))">
        <!-- Forward requests for armor to the armor backend service -->
        <forward-request destination="https://ds2-backend-api.com" />
      </when>
      <otherwise>
        <!-- Forward other requests to a default backend service -->
        <forward-request destination="https://default-backend-api.com" />
      </otherwise>
    </choose>
  </backend>
</policies>
```
> This policy routes requests based on the URL path to different backend services.


## Limit Concurrency Policy: Controlling Concurrent Item Retrieval

> In this example, we want to limit the concurrency of item retrieval requests to avoid overloading the backend service.


```xml
<policies>
  <inbound>
    <base />
  </inbound>
  <backend>
    <base />
    <!-- Limit concurrency to 50 requests at a time -->
    <limit-concurrency key="item-retrieval-concurrency" max-count="50" />
  </backend>
</policies>
```
> This policy ensures that no more than 50 item retrieval requests are processed concurrently.

## Log to Event Hub Policy: Logging Item Requests

> In this scenario, we want to log item requests to an Event Hub for monitoring.

```xml
<policies>
  <inbound>
    <base />
  </inbound>
  <backend>
    <base />
  </backend>
  <outbound>
    <base />
    <!-- Log item requests to an Event Hub -->
    <log-to-eventhub logger-id="item-requests-logger" />
  </outbound>
</policies>
```

> This policy logs item requests to an Event Hub for monitoring and analysis.

## Mock Response Policy: Testing Without Backend

> In this example, we want to return a mock response for testing purposes when the backend is not available.

```xml
<policies>
  <inbound>
    <base />
  </inbound>
  <backend>
    <base />
  </backend>
  <outbound>
    <base />
  </outbound>
  <on-error>
    <base />
    <!-- Mock response for testing when an error occurs -->
    <mock-response status="500" content-type="application/json">
      {"error": "Backend is unavailable. This is a mock response for testing."}
    </mock-response>
  </on-error>
</policies>
```

> This policy returns a predefined response when an error occurs, allowing for testing without the actual backend
