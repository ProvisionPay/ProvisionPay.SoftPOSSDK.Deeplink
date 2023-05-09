
[![NuGet version (ProvisionPay.SoftPOSSDK.Deeplink)](https://img.shields.io/nuget/v/ProvisionPay.SoftPOSSDK.Deeplink.svg?style=flat-square)](https://www.nuget.org/packages/ProvisionPay.SoftPOSSDK.Deeplink)

# SoftPOS Deeplink Dotnet SDK

It is a class library developed to reduce integration time in Softpos applications.

# Installation



Dotnet Deeplink Sdk is available on [NuGet](https://www.nuget.org/packages/ProvisionPay.SoftPOSSDK.Core/).

| ProvisionPay.SoftPOSSDK.Core|                                                        |
| :--------------- | :------------------------------------------------------ |
| Package Manager  | `PM>` `Install-Package ProvisionPay.SoftPOSSDK.Deeplink -Version 1.0.0.1`                 |
| .NET CLI         |  `>`  `dotnet add package ProvisionPay.SoftPOSSDK.Deeplink --version 1.0.0.1   `             |


# 1. Initialize

Initialize method is used for authentication which requires SDK to create credentials as Application User Id and Password. Those credentials needs to be created with your service provider. Mentioned operational process in this documentation is derived from SoftposSDKDeeplink class. 
```csharp
var credentials = new Credentials("Your Application User Id", "Your Pasword");

ISoftPosConfiguration sdkConfig = new SoftPosConfiguration()
    .WithRegion(SoftposSDKRegion.Region1)
    .WithEnvironment(EnvironmentType.Test)
    .WithCredentials(credentials)
    .Build();

ISoftposSDKDeeplink sdk = new SoftposSDKDeeplink(sdkConfig);
```
Throws: HostException, AuthenticationException, UnauthorizedException

# 2. Create Payment Session Token
CreatePaymentSessionToken method is used for initiation of the transaction process and generates payment session token to trace the initated transaction. Payment session token should be sent to the mobile side.

Please see the method below. 

```csharp
var createPaymentSessionTokenRequest = new CreatePaymentSessionTokenRequest()
{
    UserHash = "User1",
    Amount = 1,
    CallBackURL = "https://yourapplink",
    CurrencyCode= Constants.CurrencyCode.TRY,
    OrderID = "YourOrderId",
    TransactionType = Constants.TransactionType.Sale
};

var createPaymentSessionResponse = sdk.CreatePaymentSessionToken(createPaymentSessionTokenRequest);

string paymentSessionToken =  createPaymentSessionResponse.Body.PaymentSessionToken;
```
Throws: CreatePaymentSessionTokenException, HostException, ArgumentNullException, UnauthorizedException
# 3. Get Transaction By Payment Session Token
GetTransactionbyPaymentSessionToken method is used for listing the transaction detail as receipt with the input of paymentSessionToken. 

Please see the method detail below. 
```csharp
var transactionByPaymentSessionTokenRequest = new GetTransactionByPaymentSessionTokenRequest()
{
    PaymentSessionToken = paymentSessionToken
};

var getTransactionByPaymentSessionToken = sdk.GetTransactionByPaymentSessionToken(transactionByPaymentSessionTokenRequest);
```
Throws: ArgumentNullException, HostException, UnauthorizedException
# 3. Get Payment Session Status
GetPaymentSessionStatus method is used for the final status of the paymentSessionToken. 

Please see the detail below.
```csharp
var paymentSessionStatusRequest = new GetPaymentSessionStatusRequest()
{
    PaymentSessionToken = paymentSessionToken
};

var getPaymentSessionStatus = sdk.GetPaymentSessionStatus(paymentSessionStatusRequest);

```
Throws: ArgumentNullException, HostException, UnauthorizedException
# 5. Handle Webhook
Handle Webhook model identifies the detail of how to handle the webhook requests by converting the encrypted data to transaction detail.
```csharp

APICredentials apiCredentials = new APICredentials("secretkey", "accesskey");

string privateKey = "Your private key 32 character";

WebHookTransactionDetailResponse webHookTransactionDetail = sdk.HandleWebhook(apiCredentials, privateKey, Request);


```
Request variable must be HttpRequestMessage type in Net framework or HttpRequest type in Net core.

Throws: PaymentSessionTokenException, ApiKeyNotFoundException, ApiKeyMismatchException, APICredentialException, HostException, DecryptException, PrivateKeyNotFoundException
# 6. Handle Callback Data
It is used to handle data on the server-side when a callback is made from the SoftPOS application to the web application.
```csharp

var privateKey = "Your private key 32 character";

HandleCallbackDataResponse handlCallbackData = sdk.HandleCallbackData(Request, privateKey);

```
Request variable must be HttpRequestMessage type in Net framework or HttpRequest type in Net core.

Throws: DecryptException, HostException, DataNotFoundException, PaymentSessionTokenException, PrivateKeyNotFoundException
# 7. IoC Support
SoftposSDKDeeplink implementation
```csharp
services.AddTransient<ISoftPosConfiguration, SoftPosDeeplinkDemoConfiguration>();

services.AddTransient<ISoftposSDKDeeplink, SoftposSDKDeeplink>();
```

ISoftPosConfiguration implementation
```csharp
public class SoftPosDeeplinkDemoConfiguration : ISoftPosConfiguration
{
    public EnvironmentType Environment => EnvironmentType.Test;

    public Credentials Credentials => new Credentials("Your Application User Id", "Your Password");

    public SoftposSDKRegion Region => SoftposSDKRegion.Region1;
}
```
# 8. Supported Framework

NET 4.5 , NET 4.51 , NET 4.52 , NET 4.6 , NET 4.61 , NET 4.62 , NET 4.57 , NET 4.571 , NET 4.572 , NET 4.8 , NET 4.81 , NET Core  5 , NET Core  6 , NET Core 7