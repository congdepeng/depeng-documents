
# Java HTTP client comparison

The Hyper-Text Transfer Protocol (HTTP) have been the most popular protocol used on the Internet today. Web services, network-enabled appliances and the growth of network computing continue to expand the role of the HTTP protocol beyond user-driven web browsers, while increasing the number of applications that require HTTP support.

At the same time, Java have also been the most popular program language for web applications, although the java.net package provides basic functionality for accessing resources via HTTP, it doesn't provide the full flexibility or functionality needed by many applications. thus a efficient and feature-rich Java library focused on HTTP and associated protocols for creating and maintaining HTTP resources is very importand and significant.


##Different Java HTTP client libraries

Of course there already have many different famous implements of HTTP protocol in Java:

- [The Commons HttpClient](http://hc.apache.org/httpcomponents-client-ga/)
- [Apache HttpComponents](https://hc.apache.org/)
- [Google HTTP Client Library for Java](https://code.google.com/p/google-http-java-client/)
- [Async Http Client](https://github.com/AsyncHttpClient/async-http-client)

##The Commons HttpClient and Apache HttpComponents

This HttpClient was started in 2001 as a subproject of the Jakarta Commons, based on code developed by the Jakarta Slide project. It was promoted out of the Commons in 2004, graduating to a separate Jakarta project. In 2005, the HttpComponents project at Jakarta was created, with the task of developing a successor to HttpClient 3.x and to maintain the existing codebase until the new one is ready to take over. The Commons project, cradle of HttpClient, left Jakarta in 2007 to become an independent Top Level Project. Later in the same year, the HttpComponents project also left Jakarta to become an independent Top Level Project, taking the responsibility for maintaining HttpClient with it.

The Commons HttpClient project is now end of life, and is no longer being developed. It has been replaced by the Apache HttpComponents project in its HttpClient and HttpCore modules, which offer better performance and more flexibility.

### 



##Google HTTP Client Library for Java

Written by Google, this library is a flexible, efficient, and powerful Java client library for accessing any resource on the web via HTTP. It features a pluggable HTTP transport abstraction that allows any low-level library to be used, such as java.net.HttpURLConnection, Apache HTTP Client, or URL Fetch on Google App Engine. It also features efficient JSON and XML data models for parsing and serialization of HTTP response and request content. The JSON and XML libraries are also fully pluggable, including support for Jackson and Android's GSON libraries for JSON.

I think the main purpose for Google to create this libirary is for Android, Google App Engine and other Google API Client for Java.

##Async Http Client

Async Http Client library purpose is to allow Java applications to easily execute HTTP requests and asynchronously process the HTTP responses. The library also supports the WebSocket Protocol. The Async HTTP Client library is simple to use. 

This is a example of using a Future if you want to receive and process the response in your handler:



```java

import com.ning.http.client.*;
import java.util.concurrent.Future;

AsyncHttpClient asyncHttpClient = new AsyncHttpClient();
asyncHttpClient.prepareGet("http://www.ning.com/").execute(new AsyncCompletionHandler<Response>(){

    @Override
    public Response onCompleted(Response response) throws Exception{
        // Do something with the Response
        // ...
        return response;
    }

    @Override
    public void onThrowable(Throwable t){
        // Something wrong happened.
    }
});


```


The bright point of this library is WebSocket supported:


```java

	WebSocket websocket = c.prepareGet(getTargetUrl())
      .execute(new WebSocketUpgradeHandler.Builder().addWebSocketListener(
          new WebSocketTextListener() {

          @Override
          public void onMessage(String message) {
          }

          @Override
          public void onOpen(WebSocket websocket) {
              websocket.sendTextMessage("...").sendBinaryMessage("...");
          }

          @Override
          public void onClose(.WebSocket websocket) {
              latch.countDown();
          }

          @Override
          public void onError(Throwable t) {
          }
      }).build()).get();

```