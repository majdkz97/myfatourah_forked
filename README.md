# myfatoorah_flutter

In order to simplify the integration of your application with MyFatoorah payment platforms, we have developed 
a cutting-edge plugin that works smoothly with your application and provide you with a simple way to embed our payment 
functions within your application. 

The plugin will save your efforts and time instead of integrating with our API using normal API calls, and will allow
you to have the setup ready in a quick, modern and secured way.


## Prerequisites

In order to have the plugin integration working on live environment, please refer to the section 
[Prerequisites](https://myfatoorah.readme.io/v2.0/docs/prerequisites-2) 
and read it for more details

# Integration 


## Installation

##### 1. Add MyFatoorah plugin to your pubspec.yaml file.

    dependencies:
        myfatoorah_flutter: ^1.0.20	    
	  
##### 2. Install the plugin by running the following command.

    $ flutter pub get

## Usage
Inside your Dart code do the following:

##### 1. To start using MyFatoorah plugin, import it into your Flutter app. 
 
    import 'package:myfatoorah_flutter/myfatoorah_flutter.dart';

#####  2. Initiate MyFatoorah Plugin inside initState().

    MFSDK.init(<Put API URL here>, <Put your API token key here>);
    
* You can get the `API URL` and `API Token Key` for testing from [here](https://myfatoorah.readme.io/docs/test-token)	
* Once your testing is finished, simply replace the testing API URL / API token key with the live information, click
[here](https://myfatoorah.readme.io/docs/live-token) for more information.   
     
#####  3. (Optional).

* Use the following lines if you want to set up the properties of AppBar.

        MFSDK.setUpAppBar(
            title: "MyFatoorah Payment",
            titleColor: Colors.white,  // Color(0xFFFFFFFF)
            backgroundColor: Colors.black, // Color(0xFF000000)
            isShowAppBar: true); // For Android platform only

* And use this line, if you want to hide the AppBar. Note, if the platform is iOS, this line will not affected

        MFSDK.setUpAppBar(isShowAppBar: false);
	

### Initiate / Execute Payment


* Initiate Payment: this step will simply return you all available Payment Methods for the account with the actual
 charge that the customer will pay on the gateway.
  
        var request = new MFInitiatePaymentRequest(0.100, MFCurrencyISO.KUWAIT_KWD);
    
        MFSDK.initiatePayment(request, MFAPILanguage.EN,
                (MFResult<MFInitiatePaymentResponse> result) => {
    
              if(result.isSuccess()) {
                print(result.response.toJson().toString())
              }
              else {
                print(result.error.message)
              }
            });

  
* Execute Payment: once the payment has been initiated, this step will do execute the actual transaction creation at 
MyFatoorah platform and will return to your application the URL to redirect your customer to make the payment.
  
        // The value 1 is the paymentMethodId of KNET payment method.
        // You should call the "initiatePayment" API to can get this id and the ids of all other payment methods
        String paymentMethod = 1;
    
        var request = new MFExecutePaymentRequest(paymentMethod, 0.100);
    
        MFSDK.executePayment(context, request, MFAPILanguage.EN,
                (String invoiceId, MFResult<MFPaymentStatusResponse> result) => {
    
              if(result.isSuccess()) {
                print(result.response.toJson().toString())
              }
              else {
                print(result.error.message)
              }
            });
  
* As a good practice, you don't have to call the Initiate Payment function every time you need to execute payment, but
 you have to call it at least once to save the PaymentMethodId that you will need to call Execute Payment
  
### Direct Payment / Tokenization

You have to know the following steps to understand how it works:
  
* Get the payment method that allows Direct Payment by calling initiatePayment to get paymentMethodId
* Collect card info from user MFCardInfo(cardNumber: "51234500000000081", cardExpiryMonth: "05", cardExpiryYear: "21", cardSecurityCode: "100", saveToken: false)
* If you want to save your credit card info and get a token for next payment you have to set saveToken: true and you will get the token in the response read more in Tokenization
* If you want to execute a payment through a saved token you have use 

      MFCardInfo(cardToken: "put your token here")
      
* Now you are ready to execute the payment, please check the following sample code.
      
  
    // The value 2 is the paymentMethodId of Visa/Master payment method.
    // You should call the "initiatePayment" API to can get this id and the ids of all other payment methods
    String paymentMethod = 2;

    var request = new MFExecutePaymentRequest(paymentMethod, 0.100);
	
	// var mfCardInfo = new MFCardInfo(cardToken: "Put your token here");

    var mfCardInfo = new MFCardInfo("2223000000000007", "05", "21", "100",
        bypass3DS: false, saveToken: true);

    MFSDK.executeDirectPayment(context, request, mfCardInfo, MFAPILanguage.EN,
            (String invoiceId, MFResult<MFDirectPaymentResponse> result) => {

          if(result.isSuccess()) {
            print(result.response.toJson().toString())
          }
          else {
            print(result.error.message)
          }
        });
		
		
		
### Send Payment (Offline)
This will allow you to generate a payment link that can be sent by any channel we support and collect it once it's 
paid by your customer

    var request = MFSendPaymentRequest(invoiceValue: 0.100, customerName: "Customer name",
		notificationOption: MFNotificationOption.LINK);

    MFSDK.sendPayment(context, MFAPILanguage.EN, request, 
            (MFResult<MFSendPaymentResponse> result) => {
      
      if(result.isSuccess()) {
        print(result.response.toJson().toString())
      }
      else {
        print(result.error.message)
      }
    });
	
	
	
### Payment Enquiry
This will enable your application to get the full details about a certain invoice / payment
    
    var request = MFPaymentStatusRequest(invoiceId: "12345");

    MFSDK.getPaymentStatus(MFAPILanguage.EN, request,
            (MFResult<MFPaymentStatusResponse> result) => {

          if(result.isSuccess()) {
            print(result.response.toJson().toString())
          }
          else {
            print(result.error.message)
          }
        });

  

