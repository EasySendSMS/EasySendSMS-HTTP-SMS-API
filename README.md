# Send SMS HTTP(s) API

The EasySendSMS HTTP(S) API offers a straightforward and reliable way to send SMS messages, perform HLR number validation, and more. This API is designed to be easy to integrate into your existing systems, providing a flexible solution for various messaging needs. Whether you’re sending a single message, validating phone numbers, or managing bulk SMS campaigns, the HTTP(S) API allows you to do so with minimal effort and maximum efficiency.

By leveraging the HTTP(S) API, you can automate your messaging processes, ensuring that your communications are delivered promptly and accurately. The API supports a wide range of functionalities, including message scheduling, delivery reporting, and real-time number validation. These features are essential for businesses that require reliable and scalable messaging solutions.

The API’s design prioritizes ease of use, with clear documentation and examples to help you get started quickly. Whether you’re integrating the API into a simple application or a complex system, the HTTP(S) API offers the tools you need to achieve your messaging objectives efficiently.

## Overview

This page provides a reference for all features available via the HTTP interface for sending SMS.

The HTTP-API allows you to integrate your system (client) to [EasySendSMS](https://easysendsms.com) using the HTTP protocol to send SMS. HTTPS is also supported for secure transactions using [SSL encryption](https://en.wikipedia.org/wiki/SSL).

The Client issues either a GET or POST request to the EasySendSMS HTTP API, supplying a list of required parameters. Our system issues back an HTTP response indicating the status of the sent message.

The HTTP-API is used for one-way messaging only.

> **Note:**
> - 🔐 **API Password:** If you have set a dedicated API password (different from your account password), make sure you are using the correct one in your request.
> - 🌐 **IP Whitelisting:** If API IP whitelisting is enabled, ensure your request is sent from an authorized IP address. Requests from non-whitelisted IPs will be rejected.
> - ⚠️ **URL Encoding Required:** All API parameters must be properly URL-encoded before sending the request — especially `password`, `text`, `sender`, and message content. Special characters like `&`, `%`, `+`, or spaces can break the request if not encoded correctly.

## API Base URL

```http
https://api.easysendsms.app/bulksms
```

**Method:** `GET`, `POST`

## Required Parameters

The following parameters must be included in your API request:

| Parameter | Description | Presence |
| :--- | :--- | :--- |
| **Username** | Your EasySendSMS username | Mandatory |
| **Password** | Your EasySendSMS account password or your API password (if set in your account settings) | Mandatory |
| **From** | Sender Name that the message will appear from.<br><br>**Max Length:** 15 if numeric<br>**Max Length:** 11 if alphanumeric<br>Prefix the plus sign (`+`) to the sender's address if needed (URL encoded). | Mandatory |
| **To** | Mobile number of the recipient, e.g., `61409317436` (Do not use `+` or `00` before the country code). | Mandatory |
| **Text** | The message to be sent. It can be plain text or Unicode, max message length 5 parts.<br><br>153 characters per message for plain text.<br>67 characters per message for Unicode. | Mandatory |
| **Type** | Indicates the type of message:<br><br>**0:** Plain text (GSM 3.38 Character encoding)<br>**1:** Unicode (For any other language) | Mandatory |

## HTTP Response

The HTTP response from our system contains the following:

- **Status Code:** Indicates the status of the SMS request. A successful submission will return "OK".
- **Message ID:** A unique identifier generated, e.g., `760d54eb-3a82-405c-a7a7-0a0096833615`.
- **Error Message:** A descriptive error code will be included if the request fails.

### Status Codes

If the message has been sent successfully, the status code will return as:

```http
OK: 760d54eb-3a82-405c-a7a7-0a0096833615
```

If the message request has an error, it will return an error code, for example:

```http
1001
```

## Example Requests

Below are examples of a GET request using the HTTP interface:

### Send Single SMS (English)

The message has to be url encoded, and the type parameter must be set to `type=0` for English messages.

**Request:**  
*(All parameter values must be URL-encoded)*
```http
https://api.easysendsms.app/bulksms?username=testuser&password=secret&from=Test&to=12345678910&text=Hello%20world&type=0
```

**Output:**
```http
OK: 760d54eb-3a82-405c-a7a7-0a0096833615
```

### Send Bulk SMS

A request containing multiple destination numbers will be aborted immediately if any error other than "Invalid mobile number" [Code: 1005] is encountered. If an "Invalid mobile number" [Code: 1005] is found, that destination number will be skipped, and the request will proceed with the next number. A maximum of 30 numbers can be submitted per request. Duplicate numbers will be ignored.

**Request:**  
*(All parameter values must be URL-encoded)*
```http
https://api.easysendsms.app/bulksms?username=testuser&password=secret&from=Test&to=12345678910,12345678910&text=Hello%20world&type=0
```

**Output:**
```http
OK: 760d54eb-3a82-405c-a7a7-0a0096833615, OK: b9f12a34-6d78-4e8a-9bfa-2a6c7a63f891
```

### Send Single SMS (Unicode)

The message has to be encoded in the UTF-16BE format, and the type parameter must be set to `type=1` for Unicode messages.

**Request:**  
*(All parameter values must be URL-encoded)*
```http
https://api.easysendsms.app/bulksms?username=testuser&password=secret&from=Test&to=12345678910&text=006500610073007900730065006E00640073006D0073002E0063006F006D&type=1
```

**Output:**
```http
OK: 34763d84-683e-4b53-bd9f-fc9eb8c532b7
```

### Alternate Method: URI Encoded Unicode Message

The message has to be uri encoded, and the type parameter must be set to `type=1` for Unicode messages.

Sending Unicode Text with URI Encoding When sending a message containing non-ASCII characters, such as those in languages like Korean, Japanese, or Arabic, you need to use URI encoding to ensure that the text is transmitted correctly over the HTTP protocol.

URI Encoding is a process where certain characters in the text are replaced with a percent sign (`%`) followed by two hexadecimal digits representing the character's byte value in UTF-8.

This is necessary because URLs can only be sent over the Internet using the ASCII character set.  
**Example:** Suppose you want to send the following Unicode message: `안녕하세요 세상`

This message should be URI encoded before sending it via the API. The encoded version would look like this:

**Request:**  
*(All parameter values must be URL-encoded)*
```http
https://api.easysendsms.app/bulksms?username=testuser&password=secret&from=Test&to=12345678910&text=%EC%95%88%EB%85%95%ED%95%98%EC%84%B8%EC%9A%94%20%EC%84%B8%EC%83%81&type=1
```

**Output:**
```http
OK: 34763d84-683e-4b53-bd9f-fc9eb8c532b7
```

## API Rate Limit

EasySendSMS API applies rate limits for its SMS API to maintain a high quality of service.

- Default request rate limit: 30 requests per second per account (can reach up to 150 requests per second per IP address).

Requests exceeding this limit will be rejected with a `429 Too Many Requests` HTTP Status. Retry after 1 second.

## SMS Status

| Status | Description |
| :--- | :--- |
| **Pending** | The message has been sent to the route and not yet received by the handset. |
| **Delivered** | The message has been received by the handset. |
| **Expired** | The carrier has timed out. |
| **Undelivered** | The message failed to reach the handset. |

## API Error Codes

| Code | Description |
| :--- | :--- |
| **1001** | Invalid URL. One of the parameters was not provided or left blank. |
| **1002** | Invalid username or password parameter. |
| **1003** | Invalid type parameter. |
| **1004** | Invalid message. |
| **1005** | Invalid mobile number. |
| **1006** | Invalid sender name. |
| **1007** | Insufficient credit. |
| **1008** | Internal error (do NOT re-submit the same message again). |
| **1009** | Service not available (do NOT re-submit the same message again). |

## Call The API By Code

Below are examples of calling the API using various programming languages.

### 🟢 .NET
[Direct Link to Example](#net)

```csharp
var options = new RestClientOptions("")
{
    MaxTimeout = -1,
};

var client = new RestClient(options);
var request = new RestRequest("https://api.easysendsms.app/bulksms", Method.Post);
request.AddHeader("Content-Type", "application/x-www-form-urlencoded");
request.AddParameter("username", "username");
request.AddParameter("password", "password");
request.AddParameter("to", "12345678900");
request.AddParameter("from", "test");
request.AddParameter("text", "Hello world");
request.AddParameter("type", "0");
RestResponse response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```

### 🐘 PHP
[Direct Link to Example](#php)

```php
$curl = curl_init();

curl_setopt_array($curl, array(
    CURLOPT_URL => 'https://api.easysendsms.app/bulksms',
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_ENCODING => '',
    CURLOPT_MAXREDIRS => 10,
    CURLOPT_TIMEOUT => 0,
    CURLOPT_FOLLOWLOCATION => true,
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => 'POST',
    CURLOPT_POSTFIELDS => 'username=username&password=password&to=12345678900&from=test&text=Hello%20world&type=0',
    CURLOPT_HTTPHEADER => array(
        'Content-Type: application/x-www-form-urlencoded'
    ),
));

$response = curl_exec($curl);
curl_close($curl);
echo $response;
```

### ☕ Java
[Direct Link to Example](#java)

```java
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("https://api.easysendsms.app/bulksms")
    .header("Content-Type", "application/x-www-form-urlencoded")
    .field("username", "username")
    .field("password", "password")
    .field("to", "12345678900")
    .field("from", "test")
    .field("text", "Hello world")
    .field("type", "0")
    .asString();
```

### 🐍 Python
[Direct Link to Example](#python)

```python
import http.client

conn = http.client.HTTPSConnection("api.easysendsms.app")
payload = 'username=username&password=password&to=12345678900&from=test&text=Hello%20world&type=0'
headers = {
    'Content-Type': 'application/x-www-form-urlencoded'
}
conn.request("POST", "/bulksms", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
