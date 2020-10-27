# ThestingThingsOut

## ThestingThingsOut



Testing gitbook plugins how and if they work

{% tabs %}
{% tab title="C\#" %}
```aspnet
private object SubmitRequest(string sfpUrl, object request, string sfpLogin, string sfpPassword)
{
    // don't keep your connection alive, it's a simple request/response server call
    // for details on NoKeepAliveWebClient, see https://github.com/saferpay/jsonapi/blob/master/snippets/NoKeepAliveWebClient.cs
    using (var client = new NoKeepAliveWebClient())
    {
        string authInfo = $"{sfpLogin}:{sfpPassword}";
        client.Headers[HttpRequestHeader.Authorization] = "Basic " + Convert.ToBase64String(Encoding.UTF8.GetBytes(authInfo));
        client.Headers[HttpRequestHeader.Accept] = "application/json";
        client.Headers[HttpRequestHeader.ContentType] = "application/json; charset=utf-8";
        client.Encoding = Encoding.UTF8;
        try
        {
            var responseData = client.UploadString(sfpUrl, JsonConvert.SerializeObject(request));
            return JsonConvert.DeserializeObject(responseData);
        }
        catch (WebException we)
        {
            if (we.Response is HttpWebResponse response)
            {
                Trace.WriteLine($"Web exception occured: {response.StatusCode} {response.StatusDescription}");
                if (response.ContentLength > 0)
                {
                    using (var rs = we.Response.GetResponseStream())
                    using (var sr = new StreamReader(rs))
                    {
                        Trace.WriteLine($"{sr.ReadToEnd()}");
                    }
                }
            }
            else
            {
                Trace.WriteLine($"Web exception occured: {we.Message} ({we.Status}");
            }
            throw;
        }
    }
}
```
{% endtab %}

{% tab title="Java" %}
```java
public static JsonObject sendRequest(URL sfpUrl, JsonObject request, String sfpLogin, String sfpPassword) throws IOException {
    //encode credentials
    String credential = sfpLogin + ":" + sfpPassword;
    String encodedCredentials = DatatypeConverter.printBase64Binary(credential.getBytes());
//create connection
HttpURLConnection connection = (HttpURLConnection) sfpUrl.openConnection();
connection.setRequestProperty("connection", "close");
connection.setRequestProperty("Content-Type", "application/json; charset=utf-8");
connection.setRequestProperty("Accept", "application/json");
connection.setRequestProperty("Authorization", "Basic " + encodedCredentials);
connection.setRequestMethod("POST");
connection.setDoOutput(true);
connection.setUseCaches(false);

//write JSON to output stream
JsonWriter writer = Json.createWriter(connection.getOutputStream());
writer.writeObject(request);
writer.close();

//send request
int responseCode = connection.getResponseCode();

//get correct input stream
InputStream readerStream = responseCode == 200 ? connection.getInputStream() : connection.getErrorStream();
JsonObject response = Json.createReader(readerStream).readObject();

return response;

}
```
{% endtab %}

{% tab title="PHP" %}
```php
//This is an EXAMPLE of the payload-Array.
$payload = array(
'RequestHeader' => array(
'SpecVersion' => "1.7",
'CustomerId' => "[YOUR CUSTOMERID]",
'RequestId' => "aScdFewDSRFrfas2wsad3",
'RetryIndicator' => 0,
'ClientInfo' => array(
'ShopInfo' => "My Shop",
'OsInfo' => "Windows Server 2013"
)
),
'TerminalId' => "[YOUR TERMINALID]",
'PaymentMethods' => array("DIRECTDEBIT","VISA"),
'Payment' => array(
'Amount' => array(
'Value' => "21065",
'CurrencyCode' => "EUR"
),
'OrderId' => "123test",
'PayerNote' => "A Note",
'Description' => "Test_Order_123test"
),
'Payer' => array(
'IpAddress' => "192.168.178.1",
'LanguageCode' => "en"
),
'ReturnUrls' => array(
'Success' => "https://myshop.com/success",
'Fail' => "https://myshop.com/fail"
),
'Notification' => array(
'PayerEmail' => "payee@mailcom",
'MerchantEmail' => "merchant@mail.com",
'NotifyUrl' => "https://myshop/callback"
),
'DeliveryAddressForm' => array(
'Display' => true,
'MandatoryFields' => array("CITY","COMPANY","COUNTRY","EMAIL","FIRSTNAME","LASTNAME","PHONE","SALUTATION","STATE","STREET","ZIP")
)
);
//$username and $password for the http-Basic Authentication
//$url is the SaferpayURL eg. https://www.saferpay.com/api/Payment/v1/PaymentPage/Initialize
//$payload is a multidimensional array, that assembles the JSON structure. Example see above
function do_curl($username,$password,$url, $payload){
//Set Options for CURL
$curl = curl_init($url);
curl_setopt($curl, CURLOPT_HEADER, false);
//Return Response to Application
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
//Set Content-Headers to JSON
curl_setopt($curl, CURLOPT_HTTPHEADER,array("Content-type: application/json","Accept: application/json; charset=utf-8"));
//Execute call via http-POST
curl_setopt($curl, CURLOPT_POST, true);
//Set POST-Body
//convert DATA-Array into a JSON-Object
curl_setopt($curl, CURLOPT_POSTFIELDS, json_encode($payload));
//WARNING!!!!!
//This option should NOT be "false", otherwise the connection is not secured
//You can turn it of if you're working on the test-system with no vital data
//PLEASE NOTE:
//Under Windows (using WAMP or XAMP) it is necessary to manually download and save the necessary SSL-Root certificates!
//To do so, please visit: http://curl.haxx.se/docs/caextract.html and Download the .pem-file
//Then save it to a folder where PHP has write privileges (e.g. the WAMP/XAMP-Folder itself)
//and then put the following line into your php.ini:
//curl.cainfo=c:\path\to\file\cacert.pem
curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 2);
//HTTP-Basic Authentication for the Saferpay JSON-API.
//This will set the authentication header and encode the password & username in Base64 for you
curl_setopt($curl, CURLOPT_USERPWD, $username . ":" . $password);
//CURL-Execute & catch response
$jsonResponse = curl_exec($curl);
//Get HTTP-Status
//Abort if Status != 200
$status = curl_getinfo($curl, CURLINFO_HTTP_CODE);
if ($status != 200) {
//IF ERROR
//Get http-Body (if aplicable) from CURL-response
$body = json_decode(curl_multi_getcontent($curl), true);
//Build array, containing the body (Response data, like Error-messages etc.) and the http-status-code
$response = array(
"status" => $status . " <|> " . curl_error($curl),
"body" => $body
);
} else {
//IF OK
//Convert response into an Array
$body = json_decode($jsonResponse, true);
//Build array, containing the body (Response-data) and the http-status-code
$response = array(
"status" => $status,
"body" => $body
);
}
//IMPORTANT!!!
//Close connection!
curl_close($curl);
//$response, again, is a multi-dimensional Array, containing the status-code ($response["status"]) and the API-response (if available) itself ($response["body"])
return $response;
}
```
{% endtab %}
{% endtabs %}



{% api-method method="post" host="/Payment/v1/PaymentPage/Initialize" path="" %}
{% api-method-summary %}
Request Url
{% endapi-method-summary %}

{% api-method-description %}
This method can be used to start a transaction with the Payment Page which may involve either DCC and / or 3d-secure
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="RequestHeader" type="object" required=true %}
General information about the request.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-headers %}
{% api-method-parameter name="ConfigSet" type="string" required=false %}
This parameter let you define your payment page config \(PPConfig\) by name. If this parameters is not set, your default PPConfig will be applied if available. When the PPConfig can't be found \(e.g. wrong name\), the Saferpay basic style will be applied to the payment page.
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-query-parameters %}
{% api-method-parameter name="TerminalId" type="integer" required=true %}
Saferpay terminal id
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Contains general informations about the response.
{% endapi-method-response-example-description %}

```
{
  "ResponseHeader": {
    "SpecVersion": "[current Spec-Version]",
    "RequestId": "Id of the request"
  },
  "Token": "234uhfh78234hlasdfh8234e1234",
  "Expiration": "2015-01-30T12:45:22.258+01:00",
  "RedirectUrl": "https://www.saferpay.com/vt2/api/..."
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

