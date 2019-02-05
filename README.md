# REST API SDK for Ruby V2

![Home Image](homepage.jpg)

__Welcome to PayPal Ruby SDK__. This repository contains PayPal's Ruby SDK and samples for REST API.

This is a part of the next major PayPal SDK. It includes a simplified interface to only provide simple model objects and blueprints for HTTP calls. This repo currently contains functionality for PayPal Checkout APIs which includes Orders V2 and Payments V2.

## Prerequisites

- Ruby 2.0.0 or above
- Bundler

## Usage
### Binaries

It is not mandatory to fork this repository for using the PayPal SDK. You can refer [PayPal Checkout Server SDK](https://developer.paypal.com/docs/checkout/reference/server-integration) for configuring and working with SDK without forking this code.

For contirbuting or referrring the samples, You can fork/refer this repository. 

### Setting up credentials
Get client ID and client secret by going to https://developer.paypal.com/developer/applications and generating a REST API app. Get <b>Client ID</b> and <b>Secret</b> from there.

```ruby
require 'paypal-checkout-sdk'
  

# Creating Access Token for Sandbox
client_id = "PAYPAL-CLIENT-ID"
client_secret = "PAYPAL-CLIENT-SECRET"
# Creating an environment
environment = PayPal::SandboxEnvironment.new(client_id, client_secret)
client = PayPal::PayPalHttpClient.new(environment)
```

## Examples

### Creating an Order

#### Code: 
```ruby

# Construct a request object and set desired parameters
# Here, OrdersCreateRequest::new creates a POST request to /v2/checkout/orders
request = PayPalCheckoutSdk::Orders::OrdersCreateRequest::new
request.request_body({
                        intent: "CAPTURE",
                        purchase_units: [
                            {
                                amount: {
                                    currency_code: "USD",
                                    value: "100.00"
                                }
                            }
                        ]
                      })

begin
    # Call API with your client and get a response for your call
    response = client.execute(request)

    # If call returns body in response, you can get the deserialized version from the result attribute of the response
    order = response.result
    puts order
rescue BraintreeHttp::HttpError => ioe
    # Something went wrong server-side
    puts ioe.status_code
    puts ioe.headers["debug_id"]
end
```

#### Example Output:
```
Status Code:  201
Status:  CREATED
Order ID:  7F845507FB875171H
Intent:  CAPTURE
Links:
	self: https://api.sandbox.paypal.com/v2/checkout/orders/7F845507FB875171H	Call Type: GET
	approve: https://www.sandbox.paypal.com/checkoutnow?token=7F845507FB875171H	Call Type: GET
	authorize: https://api.sandbox.paypal.com/v2/checkout/orders/7F845507FB875171H/authorize	Call Type: POST
Gross Amount: USD 230.00
```

### Capturing an Order
After approving order above using `approve` link

#### Code:
```ruby
# Here, OrdersCaptureRequest::new() creates a POST request to /v2/checkout/orders
# order.id gives the orderId of the order created above
request = PayPalCheckoutSdk::Orders::OrdersCaptureRequest::new("APPROVED-ORDER-ID")

begin
    # Call API with your client and get a response for your call
    response = client.execute(request) 
    
    # If call returns body in response, you can get the deserialized version from the result attribute of the response
    order = response.result
    puts order
rescue BraintreeHttp::HttpError => ioe
    # Something went wrong server-side
    puts ioe.status_code
    puts ioe.headers["debug_id"]
end
```

#### Example Output:
```
Status Code:  201
Status:  COMPLETED
Order ID:  7F845507FB875171H
Links: 
	self: https://api.sandbox.paypal.com/v2/checkout/orders/70779998U8897342J	Call Type: GET
Buyer:
	Email Address: ganeshramc-buyer@live.com
	Name: test buyer
	Phone Number: 408-411-2134
```

## Running tests

To run integration tests using your client id and secret, clone this repository and run the following command:
```sh
$ bundle install
$ PAYPAL_CLIENT_ID=YOUR_SANDBOX_CLIENT_ID PAYPAL_CLIENT_SECRET=YOUR_SANDBOX_CLIENT_SECRET rspec spec
```

## Samples

You can start off by trying out [creating and capturing an order](/samples/capture_intent_examples/run_all.rb)

To try out different samples for both create and authorize intent check [this link](/samples)

Note: Update the `paypal_client.rb` with your sandbox client credentials or pass your client credentials as environment variable whie executing the samples.
