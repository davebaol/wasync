## wAsync: A WebSockets/HTTP Client Library for Asynchronous Communication

wAsync is a Java based library allowing asynchronous communication with any WebServer supporting the WebSocket or Http Protocol.
You can browser the [javadoc](http://atmosphere.github.com/wasync/apidocs/) As simple as

```java

        Client client = AtmosphereClientFactory.getDefault().newclient();

        RequestBuilder request = client.newRequestBuilder()
                .method(Request.METHOD.GET)
                .uri("http://wordnik.com")
                .encoder(new Encoder<String, Reader>() {        // Stream the request body
                    @Override
                    public Reader encode(String s) {
                        return new StringReader(s);
                    }
                })
                .decoder(new Decoder<String, Reader>() {
                    @Override
                    public Reader decode(String s) {
                        return new StringReader(s);
                    }
                })
                .transport(WEBSOCKET)                        // Try WebSocket
                .transport(LONG_POLLING);                    // Fallback to Long-Polling

        Socket socket = client.create();
        socket.on(new Function<Reader>() {
            @Override
            public void on(Reader r) {
                // Read the response
            }
        }).on(new Function<IOException>() {

            @Override
            public void on(Throwable t) {
                // Some IOException occurred
            }

        }).open(request.build()).fire("echo");
```
By default, the [FunctionResolver](http://atmosphere.github.com/wasync/apidocs/org/atmosphere/wasync/FunctionResolver.html) will associate the Decoder's type will be used to invoke the appropriate Function, if defined. For
example,

```java
   Decoder<String, POJO> d = new Decoder<String, POJO>() {
             @Override
             public POJO decode(String s) {
                 return new POJO(s);
             }
         }
```
will be associated to
```java
   Function<String> f = new Function<POJO>() {
             @Override
             public void on(POJO t) {

             }
        }
```
You can also implement your own FunctionResolver to associate the Function with Decoder
```java
         Socket socket = client.create();
         socket.on("myEvent", new Function<Reader>() { ...}
```
where myEvent could be read from the response's body.


You can download the jar or use Maven
```xml
          <dependency>
              <groupId>org.atmosphere</groupId>
              <artifactId>wasync</artifactId>
              <version>1.0.0</version>
          </dependency>

```