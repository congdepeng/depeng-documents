```
Author: Cong Depeng
Date: 18/05/2014
```


# Java HTTP client comparison

The Hyper-Text Transfer Protocol (HTTP) have been the most popular protocol in the Internet today. Web services, web applications all increasing the number of applications that require HTTP support.

At the same time, Java have also been the most popular program language for web applications, although the ~~java.net~~ package provides basic functionality for accessing resources via HTTP, but it doesn't provide the full flexibility or functionality needed by most applications. Thus a efficient and feature-rich Java library focused on HTTP and associated protocols for creating and maintaining HTTP resources is very importand and significant.


**Different Java HTTP client libraries**

Of course there already have many different famous implementations of HTTP protocol in Java:
- [The Commons HttpClient](http://hc.apache.org/httpclient-3.x/)
- [Apache HttpComponents](https://hc.apache.org/)
- [Google HTTP Client Library for Java](https://code.google.com/p/google-http-java-client/)
- [Async Http Client](https://github.com/AsyncHttpClient/async-http-client)

##I, The Commons HttpClient

This HttpClient was started in 2001 as a subproject of the Jakarta Commons and was moved out of the Commons in 2004 to be a separate Jakarta project. The Commons project, cradle of HttpClient, left Jakarta in 2007 to become an independent Top Level Project.

The Commons HttpClient project is now end of life, and is no longer being developed. It has been replaced by the Apache HttpComponents project in its HttpClient and HttpCore modules, which offer better performance and more flexibility.

This is an example code of how to use commons httpClient.
```java

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.*;
import org.apache.commons.httpclient.params.HttpMethodParams;

import java.io.*;

public class HttpClientTutorial {
  
  private static String url = "http://www.apache.org/";

  public static void main(String[] args) {
    // Create an instance of HttpClient.
    HttpClient client = new HttpClient();

    // Create a get method instance.
    GetMethod method = new GetMethod(url);
    
    // Provide custom retry handler is necessary
    method.getParams().setParameter(HttpMethodParams.RETRY_HANDLER, 
    		new DefaultHttpMethodRetryHandler(3, false));

    try {
      // Execute the method.
      int statusCode = client.executeMethod(method);

      if (statusCode != HttpStatus.SC_OK) {
        System.err.println("Method failed: " + method.getStatusLine());
      }

      // Read the response body.
      byte[] responseBody = method.getResponseBody();

      // Deal with the response.
      // Use caution: ensure correct character encoding and is not binary data
      System.out.println(new String(responseBody));

    } catch (HttpException e) {
      System.err.println("Fatal protocol violation: " + e.getMessage());
      e.printStackTrace();
    } catch (IOException e) {
      System.err.println("Fatal transport error: " + e.getMessage());
      e.printStackTrace();
    } finally {
      // Release the connection.
      method.releaseConnection();
    }  
  }
}

```

**Advantage**
- Have a long history
- Descended from a famous organization, Apache

**Disdvantage**
- no longer being developed



##II, Apache HttpComponents
 
In 2005, the HttpComponents project at Jakarta was created, with the task of developing a successor to the Commons HttpClient 3.x. In 2007, the HttpComponents project left Jakarta to become an independent Top Level Project, taking the responsibility for maintaining HttpClient with it.

**HttpComponents Submodules**

- **HttpComponents Core**

	Core module is a low level HTTP transport components which supports two I/O models:
	* blocking I/O model(based on classic Java I/O)
	* non-blocking, event driven I/O model(based on Java NIO)
	
- **HttpComponents Client**

	Client module is a HTTP/1.1 protocol implementation base on Core module. It also provides reusable components for client-side authentication, HTTP state and connection management. It is to be observed that we have mentioned that HttpComponents Client is a successor of and replacement for Commons HttpClient 3.x.
	
- **HttpComponents AsyncClient**

	Asynch HttpClient is a HTTP/1.1 compliant HTTP agent implementation based on HttpCore NIO and HttpClient components. It is a complementary module to Apache HttpClient intended for special cases where ability to handle a great number of concurrent connections is more important than performance in terms of a raw data throughput.
	

**Apache HttpComponents Code snippets**

- **Basic Get Method**

  This example demonstrates the use of the ResponseHandler to simplify the process of processing the HTTP response and releasing associated resources.
	```java

 public final static void main(String[] args) throws Exception {
        CloseableHttpClient httpclient = HttpClients.createDefault();
        try {
            HttpGet httpget = new HttpGet("http://localhost/");

            System.out.println("Executing request " + httpget.getRequestLine());

            // Create a custom response handler
            ResponseHandler<String> responseHandler = new ResponseHandler<String>() {

                public String handleResponse(
                        final HttpResponse response) throws ClientProtocolException, IOException {
                    int status = response.getStatusLine().getStatusCode();
                    if (status >= 200 && status < 300) {
                        HttpEntity entity = response.getEntity();
                        return entity != null ? EntityUtils.toString(entity) : null;
                    } else {
                        throw new ClientProtocolException("Unexpected response status: " + status);
                    }
                }

            };
            String responseBody = httpclient.execute(httpget, responseHandler);
            System.out.println("----------------------------------------");
            System.out.println(responseBody);
        } finally {
            httpclient.close();
        }
    }

	
	```



- **Basic Post Method to upload a file**
	
	Example how to use multipart/form encoded POST request.
	```java
	 public static void main(String[] args) throws Exception {
        CloseableHttpClient httpclient = HttpClients.createDefault();
        try {
            HttpPost httppost = new HttpPost("http://localhost:8080/upload");

            FileBody bin = new FileBody(new File("/usr/upload/file1"));//hard code a file name
            StringBody comment = new StringBody("A binary file of some kind", ContentType.TEXT_PLAIN);

            HttpEntity reqEntity = MultipartEntityBuilder.create()
                    .addPart("bin", bin)
                    .addPart("comment", comment)
                    .build();//chained method call


            httppost.setEntity(reqEntity);

            System.out.println("executing request " + httppost.getRequestLine());
            CloseableHttpResponse response = httpclient.execute(httppost);
            try {
                System.out.println("----------------------------------------");
                System.out.println(response.getStatusLine());
                HttpEntity resEntity = response.getEntity();
                if (resEntity != null) {
                    System.out.println("Response content length: " + resEntity.getContentLength());
                }
                EntityUtils.consume(resEntity);
            } finally {
                response.close();
            }
        } finally {
            httpclient.close();
        }
    }
	
	```
	
**Advantage**
- Descended from a famous organization, Apache
- Already 8 years old
- Full of features

**Disdvantage**
- A little bit complex


##III, Google HTTP Client Library for Java

Written by Google, this library is a flexible, efficient, and powerful Java client library for accessing any resource on the web via HTTP. It features a pluggable HTTP transport abstraction that allows any low-level library to be used, such as java.net.HttpURLConnection, Apache HTTP Client, or URL Fetch on Google App Engine. It also features efficient JSON and XML data models for parsing and serialization of HTTP response and request content. The JSON and XML libraries are also fully pluggable, including support for Jackson and Android's GSON libraries for JSON.

I think the main purpose for Google to create this libirary is for Android, Google App Engine and other Google API Client for Java.

This is a simple example that demonstrates how to use Google HTTP Client Library for Java with
the Google+ API.

```java

 public static PlusUrl listPublicActivities(String userId) {
      return new PlusUrl(
          "https://www.googleapis.com/plus/v1/people/" + userId + "/activities/public");
    }
  }

  private static void parseResponse(HttpResponse response) throws IOException {
    ActivityFeed feed = response.parseAs(ActivityFeed.class);
    if (feed.getActivities().isEmpty()) {
      System.out.println("No activities found.");
    } else {
      if (feed.getActivities().size() == MAX_RESULTS) {
        System.out.print("First ");
      }
      System.out.println(feed.getActivities().size() + " activities found:");
      for (Activity activity : feed.getActivities()) {
        System.out.println();
        System.out.println("-----------------------------------------------");
        System.out.println("HTML Content: " + activity.getActivityObject().getContent());
        System.out.println("+1's: " + activity.getActivityObject().getPlusOners().getTotalItems());
        System.out.println("URL: " + activity.getUrl());
        System.out.println("ID: " + activity.get("id"));
      }
    }
  }

  private static void run() throws IOException {
    HttpRequestFactory requestFactory =
        HTTP_TRANSPORT.createRequestFactory(new HttpRequestInitializer() {
            @Override
          public void initialize(HttpRequest request) {
            request.setParser(new JsonObjectParser(JSON_FACTORY));
          }
        });
    PlusUrl url = PlusUrl.listPublicActivities(USER_ID).setMaxResults(MAX_RESULTS);
    url.put("fields", "items(id,url,object(content,plusoners/totalItems))");
    HttpRequest request = requestFactory.buildGetRequest(url);
    parseResponse(request.execute());
  }

  public static void main(String[] args) {
    if (API_KEY.startsWith("Enter ")) {
      System.err.println(API_KEY);
      System.exit(1);
    }
    try {
      try {
        run();
        return;
      } catch (HttpResponseException e) {
        System.err.println(e.getMessage());
      }
    } catch (Throwable t) {
      t.printStackTrace();
    }
    System.exit(1);
  }


```


**Advantage**
- Created by Google
- Flexible, efficient, and powerful 
- Good integrated with Google products

**Disdvantage**
- Not that famous than Apache HttpComponents


##IV, Async Http Client

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
**Advantage**
- Simple to use
- Asynchronously （based on Natty）
- Supports the WebSocket Protocol

**Disdvantage**
- The develop group [Ning](http://www.ning.com/what-is-ning/) is not that famous that other candidates

##V, Conclusion

- [Apache HttpComponents](https://hc.apache.org/) is competent for most common requirements of anyone building HTTP-aware client and server applications.
- [Google HTTP Client Library for Java](https://code.google.com/p/google-http-java-client/) is suitable when you develop for Android, Google App Engine and other Google API Client with Java.
- [Async Http Client](https://github.com/AsyncHttpClient/async-http-client) will be chosen when you have many asynchronously process of the HTTP responses.The library also supports the WebSocket Protocol. Of course, Apache HttpComponents also have a AsyncClient, it all depends on your choice.



