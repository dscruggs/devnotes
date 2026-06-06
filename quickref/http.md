# HTTP Notes

Split into **client** (making requests) and **server** (building APIs) sections.

**Client:** [Why HTTP](#why-http) · [JSON](#json) · [DNS](#dns) · [URIs](#uris) · [Headers](#headers) · [HTTP Methods](#http-methods) · [Paths](#paths) · [HTTPS](#https)

**Server:** [Servers](#servers) · [JSON (server)](#json-1) · [Error handling](#error-handling) · [Storage](#storage) · [Authentication](#authentication) · [Authorization](#authorization) · [Webhooks](#webhooks)

---

## HTTP Clients

### Why HTTP
- HTTP is a simple request-response system
- the client asks another computer for some info, the server sends back a response w/ that info
- when you go to a website the browser sends a request for all the info that renders
- `URL` is Uniform Resource Locator (it's the address of another computer/server on the internet)
    - part of it specifies where to reach the server, and part tells the server what info we want
- `client` and `server` are just terms, anything can be either. Client sends requests and receives responses. Servers receive requests and send responses for the most part
- `net/http` is go's package for http

### JSON
- JavaScript Object Notation
- json comes in the body of an HTTP response as a stream of bytes. Typically best practice is to decode this into a struct in Go
- `XML` is a more verbose text based format a little bit like HTML but without predefined tags
- XML also works for sending data from the browser to a web server, but JSON is usually easier
- `map[string]interface{}` can be used if you don't know the structure of the JSON data, is a string key and value of any type


### DNS
- Web addresses (like google.com) are just aliases for IP addresses. Companies like cloudflare have mappings between them
- `Domain Name System (DNS)` is like a phone book of the internet. It resolves domain names to their associated IP addresses to perform the conversion
- `ICANN` is a nonprofit who manages DNS for the internet. ICANNs root nameserves are included in computer networking configs and from there it gathers the domain records for the domain name submitted. ICANN is the publisher of the "phonebook"
- a `subdomain` is a prefix on a domain name, and allows a domain to route network traffic to many different servers/resources


### URIs
- `URI` is Uniform Resource Identifier, is a unique character sequence than identifies a resource that is almost always accessed via the internet. URIs have syntax rules so any program can interpret them
    - URIs come in two main types `URLs` and `URNs`
- URL has a lot of different parts. Go has a method for parsing that shows many of them:
```go
return ParsedURL{
	protocol: "http",
	username: "testuser",
	password: "testpass",
	hostname: "testdomain.com",
	port:     "8080",
	pathname: "/testpath",
	search:   "testsearch=testvalue",
	hash:     "testhash",
}
```
- every part doesn't always need to be preset. Only protocol and domain/hostname are required
- a few protocol examples are http, ftp, mailto, https
- only protocols with an [authority component](https://www.rfc-editor.org/rfc/rfc3986#section-3.2) need double forward slashes like http `//`
- `ports` in a URL is a virtual point where network connections are made. Ports on computers are managed by the OS. Whenever yu connect to another computer over the internet you're connecting on a specific port which is listened to by a program. There can only be 1 program per port at a time
- default ports for http is 80, default for https is 443. 
- You always need to specify when you're not using the default port in a URL. For example 8080 is used by devs for apps in dev mode
- on static sites the URL path usually mirrors the server's filesystem hierarchy, but this is just a convention. It's however the server returns it
- `paths` are mainly just another type of parameter than can be pased to the server when making a request
- `query` parameters are not always present, but can be used however the server chooses, just like the path

### Headers
- headers are just key value pairs attached with the request to pass more info between the client and server
- browser dev tools are useful for inspecting http requests
- authentication is a common use for headers. Or something like which OS or devie the request is coming from

### HTTP Methods
- HTTP methods GET, POST, etc. map to CRUD operations by convention, not by default. You can do whatever you want in those methods
- status codes:
    - 100-199: Informational responses. These are very rare.
    - 200-299: Successful responses. Hopefully, most responses are 200's!
    - 300-399: Redirection messages. These are typically invisible because the browser or HTTP client will automatically do the redirect.
    - 400-499: Client errors. You'll see these often, especially when trying to debug a client application
    - 500-599: Server errors. You'll see these sometimes, usually only if there is a bug on the server.
- `put` is usually more for updating records. It's idempotent. `post` is for creating records and is not idempotent

### Paths
- `paths`come after the domain (or port) in a URL
- many static sites just use the file structure in their server system as their paths
- `REST` is just a convention many dynamic servers follow
    - they're stateless and allow the client and server to be implemented independently of eachother as long as the names of the resources are well established
    - typically the last part of the URL denotes the resource
- `query parameters` are also key/value pairs and can be used by the request. They're optional and appended after the path

### HTTPS
- basically just a secure form of HTTP. Requires the client to use `ssl` or `tls` to encrypt requests before sending
- security is not the same as privacy. The info you send is secure but https does not mask who you are or who you are sending the data to

## HTTP Servers

### Servers
- `async` in typescript basically exists because javascript runs single threaded, and async allows you to have programs yield the event loop while waiting on IO so you can handle many concurrent requests like on a server
    - `async` pauses execution with `await` while waiting for promises to resolve
    - `promise` is just an object that represents the eventual completion of an asynchronous op and its resulting value`
    - `error handling` is still done with try/catch blocks
- set up a basic http server at https://github.com/dscruggs/http_server
- there's a concept of `listening` on a port, that's what a server is doing. Listening for incoming connections and then doing something with the request that comes through there
- `index.html` is the default thing served for most endpoints by most file serving libraries
    - /index.html will be served from /
    - /pages/index.html will be served from /pages
    - /pages/about/index.html will be served from /pages/about
- file serving servers usually just mirror the directory structure
- `handlers` basically just handle requests to specific routes. Express uses an API based approach instead of a return based approach like FastAPI. So like to send a response you use the response object which gets passed to the handler
- `middleware` is a function that basically adds additional functionality to a handler without having to write it every time. the signature looks like this:
    ```ts 
    type Middleware = (req: Request, res: Response, next: NextFunction) => void;
    ```
    - in express can use it like this `app.use(middlewareLogging);` or in the route. You can use as many middlewares as you want `app.get('/users', middlewareLogging, middlewareAuth, ...,  handlerGetUsers);`
    - the example I used this for is to log all failed requests, kinda nice. Maybe you just have a handler for all logging?
    - can set up middleware to only run on certain routes
- monolithic vs decoupled design
    - monolith means the front end and back end/api code are together. This is a lot easier to get started but harder to scale if you want to put things on separate hardware
    - decoupled is easier to scale and more flexible but this results in a lot more complex system to manage
    - at a minumum if you're doing monolith keep API and front end decoupled
    - for deployment you need to worry about coordination and deploying multiple things in decoupled architectures
- `namespace` in this sense is just a container/logical naming to group related code identifiers. So like a top level route like `/admin` is a namespace for admin related functions

### JSON
- json does not have to be in a request/response body. That's just the normal convention. There's a lot of different formats that can be specified in a header. The http/1 protocol is just text and http/2 converted to binary frames. Http/3 fixes some issues with http/2 and uses a different protocol than TCP (I think UDP/Quic?)

### Error handling
- error handling middleware has this signature 
    ```ts
    function errorHandler(
    err: Error,
    req: Request,
    res: Response,
    next: NextFunction,
    )
    ```
    - this is defined last after other app.use() and routes
    - can just pass errors to this middleware
- extending base error classes allows for more structured and readable errors. It's good in web apps to correlate them with HTTP status codes or at least in your api error handler to do the conversion

### Storage
- disk is anything that isn't memory basically, memory is going to only be relevant in the single program you're running on that machine. Need disk to coordinate across machines and time (like DBs or files)
- REST
    - the convention here is you have a set of resources which can be accessed and made and used by a GET/POST/etc. methods.
    - to GET a resource like `GET /api/chirps` would usually give you a collection of that resource, and to get a `singleton` or single instance of a resource you do `GET /api/chirps/<id>`
    - the resource names are conventionally plural (chirps not chirp)
- can use something like an environment variable check for the environemnt (dev/prod/etc.) to gate features or endpoints off from being used (like risky ones that delete all users)
- ON DELETE CASCADE is a nice feature to clean up your DB when you delete important records with many foreign keys elsewhere. Need to think about that carefully

### Authentication
- hashing is a big part of password so that you don't store plain text in dbs for security. There's other layers of security on top of that
- length is the most important part of strong passwords. Special characters should always be allowed too
- types of auth:
    - password + ID (username and password)
    - 3rd party auth (sign in w/ google)
    - magic links
    - API keys
- JWTs (Json web token). Cryptographically signed json object w/ info about the user. They're a different way to handle auth. Popular choice for APIs that are consumed by web apps and mobile apps
    - once this is created by the server, it can't be changed without the server knowing
    - lifecycle
        - user submits username/password
        - JWT with user id created and sent to client
        - client sends jwt in all future logged in requests
        - on every authenticated request, server validates JWT
        - eventually that JWT expires and they need to log in again
    - so you can basically use these to contain all the info about the user in requests and it's crypographically signed so you know it wasn't altered. Kindof like a safe state for web apps?
    - JWTs are not encrypted so don't store sensitive info in there
    - one drawback of JWTs is they can't really be "revoked" since it's not talking to a DB when it's validating things. So the solution is a `refresh token` which are long lived and stored in a DB, and can be used to get fresh JWTs. Those can be revoked, so the worst that can happen is someone has access for the rest of the JWTs life (which should be short)
    - refresh tokens can just be plain strings since it's just a token to validate
- `cookies` are small pieces of data that a server sends to a client. The client stores the cookie and sends it back to the server on subsequent reqests. This could be anything from items in a shopping cart to user's name/other tracking info/jwt/etc.
    - server decides what to store in a cookie, the client just stores it and sends it back
    - cookies work through HTTP headers, specifically the `Set-Cookie` header. Browsers automatically send any cookies they have back to the server in the `Cookie` header
    - So basically if you're making APIs for the browser you should think about cookies, if not they're not as useful (like just plain APIs or mobile apps, etc.). There are some nice guardrails with httpOnly cookies than ensure 3rd party javascript can't access cookies

### Authorization
- `authentication` is "who are you" `authorization` is "what are you allowed to do"
- can be role based or user based
- can basically just use the info about the user/role to do logic on who's allowed to do what

### Webhooks
- a `webhook` is just an event that's sent to your server by an external service when something happens
- so like an example is payment processing. On your server you let a user buy something, in the background that's sent off to stripe who processes it and then sends back a webhook so that you know to unlock the students membership (in this case it'd be a POST request)
- the main difference between a webhook and a typical HTTP request is the system making the request is an automated system, not a human being on a webpage or an app. So webhook handlers should be idempotent because the system on the other end might retry the request multiple times
- `idempotent` is i'm pretty sure pulled straight from linear algebra, but it means the same result no matter how many times you do it
- many times the client is sending their own IDs with webhooks so building things idempotent is a little easier
- be careful never to acknowledge a webhook reqest unless you processed it successfully, because if you send back a 2xx code it will likely assume it was successful and not retry
- when you're writing the services you get to define APIs but if you're integrating from a 3rd party typically they'll define the API and you'll just implement it
- for security webhooks should use API keys or another auth method

here's some notes from chatgpt on webhooks and using them in real life:

```markdown
In real life, the **provider’s docs** define everything about the webhook: endpoint URL, event types, payload schema, required headers, and how to verify authenticity. Your job is to **expose a URL** and **follow their spec**. Typical flow:

1. **Read provider docs**  
   - Event names (e.g., `customer.subscription.updated`)  
   - Payload schema (JSON fields they send)  
   - Signature verification (e.g., `Stripe-Signature` header)  
   - Retry behavior and expected response codes

2. **Expose an endpoint in your API**  
   Example: `POST /webhooks/provider`  
   You parse JSON, verify signature, then process the event.

3. **Register the endpoint in the provider’s dashboard**  
   You give them the URL so they know where to send events.

4. **Verify signatures**  
   Providers almost always sign webhooks using a shared secret so you can confirm it’s legitimate.

5. **Handle retries & idempotency**  
   Providers retry if you return non‑2xx. You usually store event IDs to avoid processing duplicates.

6. **Test with provider tools**  
   Most providers have webhook test endpoints or CLI tools that send sample events.

So: you don’t guess the payload. You **copy the spec** and build your handler around it. In this assignment, the “spec” is the provided shape:

{
  "event": "user.upgraded",
  "data": { "userId": "..." }
}


If you want, tell me which provider you’re interested in (Stripe, PayPal, etc.) and I can outline their specific setup flow and signature verification patterns.
```

