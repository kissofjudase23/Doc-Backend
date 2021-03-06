WebServices
- [FAQ](#faq)
- [Auth](#auth)
- [TLS](#tls)
- [RESTful](#restful)
- [HTTP Methods](#http-methods)
- [HTTP Status Code](#http-status-code)
- [GRPC](#grpc)
- [Proto3](#proto3)
- [GraphQL](#graphql)
- [CDN](#cdn)
- [Multi-Hosting](#multi-hosting)
- [Performance](#performance)
- [Profiling](#profiling)


## FAQ
  * Authentication vs Authorization:
    * Authentication:
      *  is the process of ascertaining that somebody really is who they claim to be.
    * Authorization:
      *  refers to rules that determine who is allowed to do what. E.g. Adam may be authorized to create and delete databases, while Usama is only authorised to read.

  * Forward vs Redirect:
    * Redirect (3xx)
      * **Redirect sets the response status to 302, and the new url in a Location header, and sends the response to the browser.** Then the browser, according to the http specification, **makes another request** to the new url
    * Forward
      * **Forward happens entirely on the server. The servlet container just forwards the same request to the target url, without the browser knowing about that.**

  * [HTTP/2](https://http2.github.io/faq/)
    * Key differents to HTTP/1.x
      * is binary, instead of textual
      * is **fully multiplexed**, instead of ordered and blocking **can therefore use one connection for parallelism**
      * **Header Compression**
      * Allows servers to “push” responses proactively into client caches

  * [HTTP/3]

  * [What happens when you type an URL in the brower and press enter](https://medium.com/@maneesha.wijesinghe1/what-happens-when-you-type-an-url-in-the-browser-and-press-enter-bb0aa2449c1a)
    * DNS look up
      * Cache
        * check brower cache
        * check OS cache
        * check router cache
      * Ask the recursive DNS (ISP)
      * Recursvie Name Server will ask
        1. Root Domain Name Server (.)
        2. Top Level Domain Name Server (country code)
        3. 2nd level Domain Name Server
           * hosted by **Authoritative name** server
      * Get the IP address of the host
    * Establish the TCP connection
      * TCP/IP three-way handshake
        1. Client machine sends a SYN packet to the server over the internet asking if it is open for new connections.
        2. If the server has open ports that can accept and initiate new connections, it’ll respond with an ACKnowledgment of the SYN packet using a SYN/ACK packet.
        3. The client will receive the SYN/ACK packet from the server and will acknowledge it by sending an ACK packet.
    * HTTP Request
      * TLS handshake
      * Data Transfer
    * Render the page and display content on the browser

## Auth
 * JWT
   * Ref:
     * https://www.youtube.com/watch?v=7Q17ubqLfaM
   * About JWT
     * JWT is for **authorization**.
     * JWT defines the token format not the protocol.
     * JWT is kept on client side, and can be used to authorize many services as long as they share the smae secret key.
   * Flow:
     * ![flow](./images/JWT.png)
   * JWT Token format:
     * https://jwt.io/
     * header
       * alg: HS256
       * type: JWT
     * payload
       * https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32#section-4.1.1
       * iss (issuer)
       * sub (subject)
       * aud (audience)
       * iat (issued at)
       * exp (expiration time)
       * ...
     * **signature**
       * Used to verify
       * **Generated by header + payload + secret key using HMACSHA256**
       * The secret key should be saved on server side.

 * OAuth2.0
   * OAuth 2.0 and "JWT authentication" have similar appearance when it comes to the (2nd) stage where the Client presents the token to the Resource Server: the token is passed in a header.
   * But "JWT authentication" is not a standard and does not specify how the Client obtains the token in the first place (the 1st stage). That is where the perceived complexity of OAuth comes from: it also defines various ways in which the Client can obtain an access token from something that is called an Authorization Server.
   * Ref:
     * [What are the main differences between JWT and OAuth authentication?](https://stackoverflow.com/questions/39909419/what-are-the-main-differences-between-jwt-and-oauth-authentication)
       * **JWT is just a token format, OAuth 2.0 is a protocol**.
     * [OAuth terminologies and flows explained - OAuth tutorial - Java Brains](https://www.youtube.com/watch?v=3pZ3Nh8tgTE)

 * SSO
   * Single Sign-On

## TLS
  * Ref
    * [Symmetric/Asymmetric/Hybrid Encryption](http://david50.pixnet.net/blog/post/28796015)
    * [TLS handshake](https://www.ibm.com/support/knowledgecenter/SSFKSJ_7.5.0/com.ibm.mq.sec.doc/q009930_.htm)
    * [How SSL and TLS provide authentication](https://www.ibm.com/support/knowledgecenter/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10670_.htm)

  * [Cipher Suite](https://docs.microsoft.com/zh-tw/windows/win32/secauthn/cipher-suites-in-schannel?redirectedfrom=MSDN)
    *![Cipher Suite](./images/cipher_suite.png)
      * Example:
        * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
          * TLS:
            * Indicate the protocol
          * ECDHE:
            * Signifies the **key exchang**e algorithm
          * ECDSA:
            * Signifies the **authentication** algorithm
          * AES_256_CBC:
            * Indicates the **bulk encryption** algorithm
          * SHA384:
            * Indicates the MAC algorithm (for **integrity**)

  * TLS handshake overview
    * ![TLS hand shake](./images/TLS_handshake.png)
    * step1:
      * The SSL or TLS client sends a client **hello message** that lists **cryptographic information** such as the SSL or **TLS version** and, in the client's order of preference, **the CipherSuites supported by the client**.
        * The message also contains a random byte string that is used in subsequent computations. The protocol allows for the client hello to include the data compression methods supported by the client.

    * step2:
      * The SSL or TLS server responds with a server hello message that contains the **CipherSuite chosen by the server** from the list provided by the client, the session ID, and another random byte string. **The server also sends its digital certificate.**
  		* If the server requires a digital certificate for client authentication, the server sends a client certificate request that includes a list of the types of certificates supported and the Distinguished Names of acceptable Certification Authorities (CAs).

    * step3:
    	* The SSL or TLS client verifies the server's digital certificate.

    * step4:
  		* The SSL or TLS client sends the **random byte string  that enables both the client and the server to compute the secret key** to be used for encrypting subsequent message data.
    		* **The random byte string itself is encrypted with the server's public key**.

    * step5:
      * If the SSL or TLS server sent a client certificate request, the client sends a random byte string encrypted with the client's private key, together with the client's digital certificate, or a no digital certificate alert. This alert is only a warning, but with some implementations the handshake fails if client authentication is mandatory.

  	* step6:
    	* The SSL or TLS server verifies the client's certificate.

    * step7:
      * The SSL or TLS client sends the server a finished message, which is encrypted with the secret key, indicating that the client part of the handshake is complete.

    * step8:
      * The SSL or TLS server sends the client a finished message, which is encrypted with the secret key, indicating that the server part of the handshake is complete.

    * step9:
      * For the duration of the SSL or TLS session, the server and client can now **exchange messages that are symmetrically encrypted with the shared secret key**.

  * What happens during certificate verification
    * The **digital signature** is checked
      * The signature verifies a certificate, not a server.
    * The **certificate chain** is checked you should have intermediate CA certificates
    * The expire and activation dates and the validity period are checked.
    * The revocation status of the certificate is checked

  * Certificate Chain checking
    * ![certificate chain](./images/certificate_chain.png)
      * blue arrows are "is identical" 
      * green arrows means "generates" (i.e. by signing, which is not encrypting and you shouldn't call it encrypting)
    * step1:
      * If the certificate is identical to **something in the preset list of "trusted certificates"** (e.g. a trusted root CA cert, or if you've manually added a trusted root certificate), then accept the certificate.
      * If it's not and this is the last certificate in the chain, reject it (you don't trust whoever verified it).

    * step2:
      * Check Expire time, Domain matching

    * step3:
      * Check the **Issuer**, the **signature algorithm**, and the **signature** on the certificate.
        * If there's a next cert in the list of certs you received, verify that that certificate's Subject DN is this one's Issuer DN.
        * Using the public key from the next certificate and the signature algorithm from this certificate, verify the signature on this certificate.

    * step4:
      * Verify that the next certificate has the CA attribute. (certificate authority)

    * step5:
      * Validate the next certificate down the line using this same process.

    * Ref:
      * https://support.dnsimple.com/articles/what-is-ssl-certificate-chain/
      * https://crypto.stackexchange.com/questions/24968/


  * [CA digital signature checking](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/702577/#outline__5)
    * Signature:
      * 用頒發機構的private key，對下級證書的public key 進行加密生成。
        * digital_sign = CA_pr_key + sub_cert_ppu_key
    * Decrypt:
      * 用頒發機構的public key 對digital signatue 進行解密，對比下級證書的public key 和解密後的值是否一致。
    * Flow:
      * 頒發機構 A，用自己的private key將需要生成的下級證書 B 的public key 進行加密，生成數字簽名，然後再帶上相關信息：公鑰，公鑰的指紋，數字簽名，證書名，簽發機構等。
      * 瀏覽器解析下級證書 B 的相關信息，找到簽發機構和數字簽名。
      * 然後，找到簽發機構 A，使用 A 的公鑰去解密數字簽名，然後對比下級證書 B 的公鑰。如果成功則合法，反之，不合法。

## RESTful
  * Key Conecpt:
    * The key principles of REST involve **separating your API into logical resources**. These resources are **manipulated using HTTP requests where the method** (GET, POST, PUT, PATCH, DELETE) has specific meaning.
  * Practices:
    * Use nouns or gerunds but no verbs
      * gerunds(動名詞): MoneyTransfer
    * GET method and query parameters should not alter the state 
    * Use plural nouns
      * Do not mix up singular and plural nouns. Keep it simple and use only plural nouns for all resources.
      * /cars instead of /car
    * Use sub-resources for relations
      * GET /cars/711/drivers/4
    * Provide filtering, sorting, field selection and paging for collections 
      * **Filtering**
        * GET /cars?**color=red**
          * Returns a list of red cars
        * GET /cars?**seats<=2**
          * Returns a list of cars with a maximum of 2 seats
      * **Sorting**
        * GET /cars?**sort=-manufactorer,+model**
          * This returns a list of cars sorted by descending manufacturers and ascending models.
      * **Field Selection**
        * GET /cars?**fields=manufacturer,model,id,color**
      * **Paging**
        * GET /cars?**offset=10&limit=5**
        * Use **limit** and **offset**. It is flexible for the user and common in leading databases. The default should be limit=20 and offset=0
        * To send the total entries back to the user use the custom HTTP header: **X-Total-Count.**
    * Version your API
    * Handle Errors with HTTP status codes
    * Allow overriding HTTP method


  * Example:
    | *         | Resource              | GET                      | POST                   | PUT                   | DELETE |
    | --------- | --------------------- | ------------------------ | ---------------------- | --------------------- |
    | /car      | Return a list of cars | Create a new car         | Bulk Update            | Delete all cars       |
    | /cars/711 | Return a specific car | Method not allowed (405) | Updates a specific car | Delete a specific car |

  * Ref:
    * [TritonHo](https://github.com/TritonHo/slides/blob/master/Taipei%202016-04%20talk/RESTful%20API%20Design-tw-2.1.pdf)
    * [10 Best Practices for Better RESTful API](https://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/)
    * [Best Practices for Designing a Pragmatic RESTful API](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)


## HTTP Methods
  * Methods
    * GET (safe & idempotent):
      * List the resources
    * HEAD (safe & idempotent):
      * read (header only)
      * Returns info about resource (version/length/type)
      * Checking whether a resource has changed.
      * Retrieving metadata about the resource.
      * Testing whether a resource exists and is accessible.
    * POST:
      * create the resource
    * PATCH:
      * update the resource
    * DELETE:
      * delete the resource
    * **Options**
      * Returns info about API (supported methods, Content-Type, etc.)
      * Identifying which HTTP methods a resource supports.
      * Testing whether a resource exists and is accessible.
        * Can be used for health-check
  * Property:
    * Safe
      * Safe methods are HTTP methods that do not modify resources.
    * idempotent
      * An idempotent HTTP method is a HTTP method that can be called many times without different outcomes. It would not matter if the method is called only once, or ten times over. The result should be the same.

## HTTP Status Code
  * Ref:
    * https://httpstatuses.com/
    * https://stackoverflow.com/questions/942951/rest-api-error-return-good-practices
  * Flow:
    * ![http return code flow](./images/http_return_code_flow.png)
  * 2xx: **Success**
    * 200 ok:
      * The general code.
    * 201 Created:
      * Resource created successfully.
    * 202 Accepted:
      * Request accepted and still being processed.
      * This request may take a lot of time.
      * Async job
    * 204 No Content:
      * Request completed successfully but nothing to return.
      * delete, put may use this return code.
  * 3xx: **Redirection**
      * 301 Moved Permanently
      * 302 Found
      * 303 See Other
        * Response can be found elsewhere (e.g. after the client has sent a POST request)
  * 4xx: **Client Error** (Client should not retry)
    * 400 Bad Request:
      * The general code (client side)
    * 401 Unauthorized:
      * Client not authenticated*
    * 403 Forbidden:
      * Client not allowed for the request
    * 404 Not Found:
      * Resource not found
    * 405 Method Not Allowed:
      * The HTTP verb not supported for this API endpoint
    * 406 Not Acceptable:
      * The **requested Content-Type** not supported (Accept header)
    * 415 Unsupported Media Type:
      * The Content-Type of the **request body** not supported (Content-Type header)
    * 409 Conflict:
      * This Resouce has been modified by other
  * 5xx: **Serverl Error** (Client may reasonably retry)
    * 500 Internal Server Error
    * 502 Bad Gateway:
      * Load balancing tier issue
      * bad response from the upstream server (usually only a gateway or proxy server would return 502)
    * 503 Service Unavailable:
      * Service temporarily unavailable (i.e. may be available shortly)
    * 504 Gateway Timeout:
      * Upstream server timeout (usually only a gateway or proxy server would return 504)

## GRPC
  * Ref:
    * [grpc](https://grpc.io/)
  * Why gRPC
    * gRPC is a modern RPC protocol implemented on top of HTTP/2. HTTP/2 is a Layer 7 (Application layer) protocol, that runs on top of a TCP (Layer 4 - Transport layer) protocol, which runs on top of IP (Layer 3 - Network layer) protocol. gRPC has many advantages over traditional HTTP/REST/JSON mechanism such as
      * Binary protocol (HTTP/2)
      * Multiplexing many requests on one connection (HTTP/2)
      * Header compression (HTTP/2)
      * Strongly typed service and message definition (Protobuf)
      * Idiomatic client/server library implementations in many languages
  * Suggest using gRPC for the following cases
    * When the microservices is only internal and when one server needs to talk to the other.
    * When your internal services requires **duplex streaming** with high load of data.

## Proto3
  * Ref:
    * [proto3](https://developers.google.com/protocol-buffers/docs/proto3)
  * Advantages:
    * Platform/language neutral
    * **Backward compatibility**
    * Performance (compared with JSON)
    * Do not need accessor class for mapping
    * Do not need Doc (.proto is the doc)

  * Backward Compatibility:
    * [Rule of Updating A Message Type](https://developers.google.com/protocol-buffers/docs/proto3#updating)

## GraphQL
  * Ref:
    * https://www.howtographql.com/basics/0-introduction/
  * GraphQL is a new API standard that provides a more efficient, powerful and flexible alternative to REST.
  * **At its core, GraphQL enables declarative data fetching where a client can specify exactly what data it needs from an API. Instead of multiple endpoints that return fixed data structures, a GraphQL server only exposes a single endpoint and responds with precisely the data a client asked for**.
  * GraphQL is the better REST
    * Over the past decade, REST has become the standard (yet a fuzzy one) for designing web APIs. It offers some great ideas, such as stateless servers and structured access to resources. However, REST APIs have shown to be too inflexible to keep up with the rapidly changing requirements of the clients that access them.
    * example:
      * https://www.howtographql.com/basics/1-graphql-is-the-better-rest/


## CDN
  * A content delivery network (CDN) is a globally distributed network of proxy servers, serving content from locations closer to the user. Generally, **static files** such as HTML/CSS/JS, photos, and videos are served from CDN, although some CDNs such as Amazon's CloudFront support **dynamic content**. The site's DNS resolution will tell clients which server to contact.
  * Advantages:
    * Performance
      * Users receive content at data centers close to them.
      * Your servers do not have to serve requests that the CDN fulfills.
    * Security
  * Types:
    * Push CDNs:
      * Push CDNs receive new content whenever changes occur on your server. **You take full responsibility for providing content, uploading directly to the CDN and rewriting URLs to point to the CDN.**
      * Sites with a small amount of traffic or sites with content that isn't often updated work well with push CDNs. Content is placed on the CDNs once, instead of being re-pulled at regular intervals.

    * Pull CDNs:
      * **Pull CDNs grab new content from your server when the first user requests the content.** You leave the content on your server and rewrite URLs to point to the CDN. This results in a slower request until the content is cached on the CDN.
      * **A time-to-live (TTL) determines how long content is cached.** Pull CDNs minimize storage space on the CDN, but can create redundant traffic if files expire and are pulled before they have actually changed.
      * **Sites with heavy traffic work well with pull CDNs**, as traffic is spread out more evenly with only recently-requested content remaining on the CDN.


## Multi-Hosting
  * Use Host header as virtual host
  * Virtual Hosting
    * Example:
      * nginx config
        ```txt
        server {
          listen      80;
          server_name example.org www.example.org;
          ...
        }

        server {
          listen      80;
          server_name example.net www.example.net;
          ...
        }
        ```
    * Headers
      * Host header:
        * The Host request header specifies the domain name of the server (for virtual hosting), and (optionally) the TCP port number on which the server is listening.
      * Authority header:
        * HTTP/2, which uses the :authority header instead of host
    * **Issues**:
      * A common issue arises when configuring two or more HTTPS servers listening on a single IP address
      * With this configuration a browser receives the default server’s certificate, i.e. www.example.com regardless of the requested server name.
        * This is caused by SSL protocol behaviour.
        * The SSL connection is established before the browser sends an HTTP request and nginx does not know the name of the requested server. Therefore, it may only offer the default server’s certificate.
        * Solutions:
          * SNI
            * Allows a browser to pass a requested server name during the SSL handshake
        * nginx config:
          ```txt
            server {
                listen          443 ssl;
                server_name     www.example.com;
                ssl_certificate www.example.com.crt;
                ...
            }

            server {
                listen          443 ssl;
                server_name     www.example.org;
                ssl_certificate www.example.org.crt;
                ...
            }
          ```
  * An SSL certificate with serveral names
    * SubjectAltName
      * One way is to use a certificate with several names in the SubjectAltName certificate field, for example, www.example.com and www.example.org.
      * However, the SubjectAltName field length is limited.
    * Wildcard name
      * Another way is to use a certificate with a wildcard name, for example, *.example.org. A wildcard certificate secures all subdomains of the specified domain
      * Limitations:
        * **There shall be only one "*" character** in the name
            * so no "\*.\*.stackexchange.com", to take an actual case.
        * The "*" must appear as **first character**, not elsewhere
            * So no "meta.*.stackexchange.com", to continue on that real-life situation.
        * The "*" must stand for a **complete name component**
            * hence "\*.example.com" but not "\*foo.example.com"
        * Ref:
          * [How many hostnames can be supported by a wildcard ssl certificate? Is there any limit?](https://security.stackexchange.com/questions/70882/how-many-hostnames-can-be-supported-by-a-wildcard-ssl-certificate-is-there-any)
      * Note:
        * SubjectAltName and wildcard name can be combined
        * A certificate may contain exact and wildcard names in the SubjectAltName field, for example, example.org and *.example.org.
    * Server Name Indication (SNI)
      * A more generic solution for running several HTTPS servers on a single IP address is TLS Server Name Indication extension (SNI, RFC 6066), which **allows a browser to pass a requested server name during the SSL handshake**.

## Performance
  * Ref:
    * [High Performance Brower Networking](https://hpbn.co/?utm_source=istlsfastyet&utm_medium=referral&utm_campaign=tls)
    * [TLS has exactly one performance problem: it is not used widely enough](https://istlsfastyet.com/)

## Profiling
  * wrk