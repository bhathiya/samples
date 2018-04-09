HTTP package provides HTTP, HTTP2 and WebSocket implementations. These implementations provide 2 types of endpoints which are Client Endpoints and Service endpoints. Client endpoints are used to connect to and interact with HTTP services. A sample client endpoint can be defined like this with the service URL it needs to connect with.

``` java
endpoint http:ClientEndpoint clientEndpoint {
   targets: [{
                 uri: "https://my-simple-backend.com"
             }]
   };
```

The defined client endpoint can be used to call the service.

``` java
http:Request req = {};
// Send a GET request to the specified endpoint
var response = clientEndpoint -> get("/get?id=123", req);
```

Client Endpoint implementation supports connection pooling and it can be configured to have a limit for maximum active connections which can be made with the remote endpoint. It also supports connection eviction after a given period of idle time. Client Endpoint also supports follow-redirects, so developers don’t have to manually handle 3xx HTTP status codes. 

Client endpoints support resilience in different ways such as load balancing, circuit breaking, endpoint timeouts and a retry mechanism. In case of load balancing, it can be used in the round-robin manner or failover manner. Circuit breakers are useful in case of remote service failures. When some remote service goes down, client connections may wait for some time before timeout. Multiple such client requests can waste resources in the system heavily. To avoid that, circuit breakers can be configured so that they trip after a certain amount of failure requests are made. Once a circuit breaker trips, it won’t allow the client to make calls to the particular endpoint anymore. However, circuit breaker time to time allows the client to call the backend endpoint so that it can know the latest status of the endpoint. Ballerina circuit breaker supports tripping on HTTP error status codes and I/O errors. Failure threshold can be configured based on a rolling window. (for eg. 5 failures within 10 seconds). Client endpoints also support a retry mechanism which allows a client to keep retrying failed requests for a given number of times in a perodic manner. 

See [Client Endpoint Example](https://ballerinalang.org/docs/by-example/http-client-connector), [Circuit Breaker Example](https://ballerinalang.org/docs/by-example/http-circuit-breaker), [HTTP Redirects Example](https://ballerinalang.org/docs/by-example/http-redirects)

A service can be exposed via a service endpoint, and it represents a collection of network-accessible entry points. A resource represents one such entry point and can have its own path, HTTP methods, body format, 'consumes' content type, 'produces' content type, CORS headers etc. A service endpoint can be defined like this.

```java
@Description {value:"Attributes associated with the service endpoint is defined here."}
endpoint http:ServiceEndpoint helloWorldEP {
   port:9090
};
```

Then a service can be defined and bind to above Service endpoint.

```java
@Description {value:"By default Ballerina assumes that the service is to be exposed via HTTP/1.1."}
@http:ServiceConfig { basePath:"/helloWorld" }
service<http:Service> helloWorld bind helloWorldEP {
   @Description {value:"All resources are invoked with arguments of server connector and request"}
   sayHello (endpoint conn, http:Request req) {
       http:Response res = {};
       // A util method that can be used to set string payload.
       res.setStringPayload("Hello, World!");
       // Sends the response back to the client.
       _ = conn -> respond(res);
   }
}
```

When a request is received to a service, the best-matched resource is calculated based on a set of predefined criteria and the request is dispatched to the best matching resource. 

Server endpoints support interceptors (aka. filters) which are automatically invoked when a request is received to a service. When multiple filters are configured, those are executed in the specified order. These can be used for any request validation tasks including authentication and authorization, which should happen before executing the actual service. 

See [Service Endpoint Example](https://ballerinalang.org/docs/by-example/http-data-binding), [HTTP CORS Example](https://ballerinalang.org/docs/by-example/http-cors), [HTTP Failover Example](https://ballerinalang.org/docs/by-example/http-failover), [HTTP Load Balancer Example](https://ballerinalang.org/docs/by-example/http-load-balancer)

Service endpoints can be exposed via SSL. It supports Mutual SSL, Hostname Verification and Server Name Indication (SNI). It also supports Application Layer Protocol Negotiation (ALPN) so that client and server can agree on a single application layer protocol in cases of multiple application protocols are supported on a single server-side port. Client endpoints support Certificate Revocation List (CRL) and Online Certificate Status Protocol (OCSP), whereas Service endpoints support OCSP Stapling. 

See [Mutual SSL Example](https://ballerinalang.org/docs/by-example/mutual-ssl)

Both client and service endpoints support HTTP2, keep-alive, chunking, HTTP caching, and data compression/decompression. 

See [Caching Example](https://ballerinalang.org/docs/by-example/caching), [HTTP Disable Chunking Example](https://ballerinalang.org/docs/by-example/http-disable-chunking)

HTTP package supports 2 types of logging functionalities, which are HTTP trace logs and HTTP access logs. HTTP trace logs allow writing request and response trace logs to console or publish those to a socket. As the name suggests, trace logs are logged at TRACE level. So the log level of trace logs has to be set to TRACE for the trace logs to be enabled. HTTP Access logs can be written to console or files. 

See [HTTP Trace Logs Example](https://ballerinalang.org/docs/by-example/http-trace-logs)

HTTP package provides WebSocket support. WebSocket endpoints also have 2 types as client and server. WebSocket client endpoints support callback services and both types of endpoints support ping/pong control frames. 

See [WebSocket Basic Example](https://ballerinalang.org/docs/by-example/websocket-basic-sample), [HTTP to WebSocket Upgrade Example](https://ballerinalang.org/docs/by-example/http-to-websocket-upgrade)
