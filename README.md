# ThirdParty-API-communication.
This setup contains third party API integration with other rest client.

## 1.ExampleClient.java
we need to configure in application "ExampleClient.java"

```java
package com.example.aadharOrErpSomeText.middlewareapi;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.json.simple.parser.ParseException;

import okhttp3.FormBody;
import okhttp3.MediaType;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

public class ExampleClient {

	private static final String ERP_SERVER_ADDRESS = "http://test.domain.com";
	private static final String ERP_USER = "testUser";
	private static final String ERP_PWD = "password";
	private OkHttpClient httpClient = null;
	private Response httpResponse;
	public static final MediaType JSON = MediaType.parse("application/json; charset=utf-8");
	public static final MediaType MULTPART = MediaType.parse("application/x-www-form-urlencoded");

	private static String sidHeader = null;

	public ERPClient() throws IOException {
		httpClient = new OkHttpClient();
		sidHeader = getResponseHeader("Set-Cookie");
	}

	public String get(String url) throws IOException {
		// Request request = new Request.Builder().url(url).build();
		Request request = new Request.Builder().addHeader("Cookie", sidHeader).url(ERP_SERVER_ADDRESS + url).build();
		try (Response response = httpClient.newCall(request).execute()) {
			return response.body().string();
		}
	}

	public String post(String url) throws IOException {
		// RequestBody body = RequestBody.create(JSON);
		RequestBody body = RequestBody.create(MULTPART, "");
		Request request = new Request.Builder().url(url).post(body).build();
		httpResponse = httpClient.newCall(request).execute();
		return httpResponse.body().string();
	}
```


## 2."methodExample" method.
### Get method calling on follow.
```java
public JSONObject getMethod(String id) throws IOException, ParseException {
	String getCustomer = get("/api/V1/Customer/" + id);

	ExampleMessageParser exampleMessageParser = new ExampleMessageParser();
	JSONObject jsonObject = exampleMessageParser.parserMethod(getCustomer);
	return jsonObject;
}
```
### Get method parsing.
```java
package com.example.exampleAadhaarOrErpOrSomething.middlewareapi;

import java.util.Iterator;
import java.util.Map;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.json.simple.parser.ParseException;

public class ExampleMessageParser {
	public static JSONObject parserMethod(String response) throws ParseException {
		JSONParser jsonParser = new JSONParser();
		JSONObject inputJson = (JSONObject) jsonParser.parse(response);
		JSONArray dataJson = (JSONArray) inputJson.get("data");

		JSONObject outputJson = new JSONObject();
		for(int i = 0; i< dataJson.size(); i++) {
			JSONObject internalJSON = (JSONObject) dataJson.get(i);
			System.out.println(internalJSON.get("lon_gkey"));
		}
		
		return outputJson;
	}
}
```

## 3."methodExample" method.
### Post method calling on follow.
```java
public String methodExample(SomeBean someBean) throws IOException {
	final RequestBody formBody = new FormBody.Builder()
		.add("meter_id", jsonObject.get("name").toString())
		.build();
	final Request request = new Request.Builder().url(ERP_SERVER_ADDRESS + "/customer/"+someBean.getId())
			.addHeader("Cookie", sidHeader).post(formBody).build();

	final OkHttpClient httpClient = new OkHttpClient();
	final Response httpResponse = httpClient.newCall(request).execute();
	httpResponse.headers().get("Set-Cookie");

	return httpResponse.body().string();
}
```
## 4.APIClient.java
### post call- this will call other APIs.
```java
@Component
public class APIClient {

	public static void postMessage(ExampleBean exampleBean) {
		final String url = urlPrefix + "/api/customer";

		RestTemplate restTemplate = new RestTemplate();
		ExampleBean result = restTemplate.postForObject(url, exampleBean, ExampleBean.class);

		// System.out.println(result);
	}
```	
### get call- this will call other APIs.
```java
@Component
public class APIClient {

	public static void getMessage(Long id) {
		final String url = urlPrefix + "/api/customer/"+ id;
		
		RestTemplate restTemplate = new RestTemplate();
		restTemplate.getForObject(url, String.class);
	}
```	

## Conclusion
End of this integration we can communicate with third party APIs.

## References
To make edit this document please use [edit readme.md](https://www.makeareadme.com/#rendered-1).
