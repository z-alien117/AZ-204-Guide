# Dark souls 3 items policies

## Inbound Section: Rate Limiting

> In the **inbound** section, you can apply rate limiting to ensure fair usage of the API. Let's set a rate limit of 100 requests per minute for each client:

```xml
<policies>
  <inbound>
    <rate-limit-by-key calls="100" renewal-period="60" counter-key="@(context.Request.IpAddress)" />
  </inbound>
  <backend>
    <base />
  </backend>
  <outbound>
    <base />
  </outbound>
  <on-error>
    <base />
  </on-error>
</policies>
```
> This policy **limits** the number of requests a client can make in a minute based on their IP address. If a client exceeds the limit, they will receive a rate limit exceeded response.

## Backend Section: Data Transformation

> In the backend section, you can transform data before sending it to the client. Let's assume our API provides item data in JSON format, but we want to include additional details in the response:

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
    <set-body>
      <items>
        <item>
          <name>Dark Sword</name>
          <description>
            Pitch-black straight sword of the Darkwraith, survivor of the land swallowed by darkness. The Darkwraiths were the first red orb invaders, and originators of a unique sword technique inspired by their thick, broad blades.
          </description>
          <skill>
            <name>Stomp</name>
            <description>
                Use one's weight to lunge forward with a low stance and increased poise, and follow with strong attack for an upward slash.
            </description>
          </skill>
        </item>
        <!-- More item entries here -->
      </items>
    </set-body>
  </outbound>
  <on-error>
    <base />
  </on-error>
</policies>
```

> This policy enriches the response body with additional item details, including names, descriptions, and possible skills.

## Outbound Section: Caching

> In the outbound section, you can implement caching to reduce the load on your API and improve response times. Let's cache the item data for 5 minutes

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
    <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" />
    <cache-store duration="300" />
  </outbound>
  <on-error>
    <base />
  </on-error>
</policies>
```
> This policy caches the item data for 5 minutes, reducing the need to fetch the data from the backend for subsequent requests within that timeframe.

## On-Error Section: Custom Error Handling

> In the on-error section, you can handle errors gracefully. Let's say an item is not found in the database, and we want to provide a user-friendly error message

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
    <set-body>
      <error>
        <message>Item not found. Please check the item name and try again.</message>
      </error>
    </set-body>
    <return-response>
      <set-status code="404" reason="Not Found" />
    </return-response>
  </on-error>
</policies>
```

> This policy generates a user-friendly error response when an item is not found, indicating that the requested item does not exist.