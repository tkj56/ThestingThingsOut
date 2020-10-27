# ThestingThingsOut

## ThestingThingsOut



Testing gitbook plugins how and if they work

{% tabs %}
{% tab title="C\#" %}
```text
var test = new Test();
```
{% endtab %}

{% tab title="Java" %}
```text
var test = new Test();
```
{% endtab %}

{% tab title="Javascript" %}
```javascript
console.log("Test");
```
{% endtab %}
{% endtabs %}

## Table of contents

```text
{% math %}(ax^2 + bx + c = 0){% endmath %} 
```

* [Summary](https://github.com/tkj56/ThestingThingsOut/tree/5adb26068a91912cad80d542538d6ec8e396e684/SUMMARY.md)

```python
print("elefant")
```

```text
var a = new Test();
```

```javascript
alert("some js test");
console.log('single quote')
```

```java
decimal deci = new Decimal();
```



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

