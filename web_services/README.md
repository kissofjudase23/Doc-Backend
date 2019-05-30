## WebServices
  * Auth
    - Authentication vs Authorization
      - Authentication is the process of ascertaining that somebody really is who they claim to be.
      - Authorization refers to rules that determine who is allowed to do what. E.g. Adam may be authorized to create and delete databases, while Usama is only authorised to read.
    * JWT
    * OAuth2.0
  * TLS
    - [TLS handshake](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm)
    - [Symmetric/Asymmetric/Hybrid Encryption](http://david50.pixnet.net/blog/post/28796015)
    - [Cipher Suite](https://curl.haxx.se/libcurl/c/CURLOPT_SSL_CIPHER_LIST.html)
  * [HTTP/2](https://http2.github.io/faq/)
    - Key differents to HTTP/1.x 
      - is binary, instead of textual
      - is fully multiplexed, instead of ordered and blocking can therefore use one connection for parallelism
      - Header Compression
      - Allows servers to “push” responses proactively into client caches

  * Design Patterns
    * RestAPI
  * [Forward vs Redirect](https://stackoverflow.com/questions/6068891/difference-between-jsp-forward-and-redirect)
    * Forward
      - redirect sets the response status to 302, and the new url in a Location header, and sends the response to the browser. Then the browser, according to the http specification, makes another request to the new url
    * Redirect
      - forward happens entirely on the server. The servlet container just forwards the same request to the target url, without the browser knowing about that. Hence you can use the same request attributes and the same request parameters when handling the new url. And the browser won't know the url has changed (because it has happened entirely on the server)
  * MicroServices
    * Advantages:
    * Drawbacks:
      * Performance
      * Lambda Limits
        * Account Limits (e.g. aws lambda instance count)
        * Cold Start Time
  * Profiling
    * wrk 