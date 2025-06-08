
# Add Payment Page to Your Web App

Stripe is the most popular payment gateway, but I'll use Razorpay as it's more supported in India at the time of this writing. 

In Test Mode, Razorpay allows you to generate API key+secret (in your account settings) even without a merchant website.



# 1. In an Expressjs (Backend) + Reactjs (Frontend) App

Algorithmically, this is what you'll need to do:

## I. ON YOUR BACKEND SERVER

1. **Install Razorpay SDK**
   - Install the `Razorpay SDK` library on your server using `npm install razorpay` to interact with Razorpay's API.

2. **Initialize Razorpay SDK**
   - Import the Razorpay SDK and initialize an `instance of the SDK` with your Razorpay API key and secret.

3. **Create an /api/create-order API**
   - Use the SDK instance to create an order through a 3-step approach:

     **a. Client-Side**
     - When you want to create an order, make a request to your local backend endpoint (e.g., `/api/create-order`), which you would have defined in your Express server.

     **b. Backend Processing**
     - The backend (your Express server) receives this request and calls the Razorpay SDK method `razorpay.orders.create(options)`.
     - This method takes a JSON object `options` as an argument (which at least includes the amount and currency) and sends a POST request to the Razorpay API at its endpoint `/v1/orders/`, sending the order details specified in `options`.

     **c. Response**
     - The Razorpay API responds with an `order` object, which may include the order ID, amount, currency, and other details.
     - Your backend then forwards this information back to the client.

Thus your Express Backend is acting as an intermediary between your frontend (client) and the Razorpay API. You can also skip this intermediary but that would expose your sensitive Razorpay API key & secret in your frontend code. 

---
 
## II. ON YOUR FRONTEND FOLDER



1. **Import the Razorpay Checkout Library**

    For that include a `script` in the `index.html` file.

2. **Create a `Payment.js` Component File**

   The `Payment.js` component file will consist of two main parts:

   **a. `Pay now` Button**
   - Create a button labeled `Pay now`.
   - Attach an `onclick` event handler that triggers an asynchronous `handlePayment` function.

   **b. Functional Component: `Payment`**

   The `Payment` component will have two parts:

   **i. Asynchronous `handlePayment` Function**

   1. **Store API Response**
      - Make a request to your backend API `/api/create-order` using the `fetch` method with `POST` in `await` mode.
      - Store the response in an object called `response`.

   2. **Convert Response to JSON**
      - Convert the `response` object to JSON in `await` mode.
      - Store the JSON data in an object called `orderData`.

   3. **Create `options` Object**
      - Create a JSON object named `options`.
      - Deconstruct all fields from the `response` object into `options`.

   **ii. Payment Modal**
   - Create a payment modal using `Razorpay(options)` and then `open` it to display the Razorpay UI.
   - The `Razorpay` object is provided by the `Razorpay Checkout library`.

---

## Summary 

Click "Pay Now" → React calls /create-order → Express generates Order ID → Razorpay UI opens → Payment happens → Express verifies payment.

## Payment Acknowledgement Workflow

Remember the payment modal that you created above, say it was `paymentObject`. Then `paymentObject.open()` opened the Razorpay payment dialog box. 

Remember this paymentObject was created using 
`const paymentObject = new window.Razorpay(options);`

`options` is a json object which contains elements attributing what's displayed in the payments dialog box. But one of its elements is `handler` which handles what would happen after the payment is successful using some `function(response)`.

You might either 
- print a success message as an `alert()` 
- or call some `handlePayment()` function which displays some order details at `{baseURL}/payment-success` 
- or directly cause the payment dialog box to open in another route by writing `window.location.href = /payment-success`. This route would render a `<PaymentSuccess />` component. This component can send a POST request to an `/api/verify-payment` endpoint in the backend. If a `success` response is received from this endpoint, it can render an acknowledgement UI with order details using `return()`.

I prefer the third strategy due to modularity of the code.

So the flowchart looks like this:

<img src="https://i.imgur.com/CJyZoHR.png" alt="Payment Acknowledgement Workflow" style="margin: 10px 0;"/>

Then the `/payment-success` route opens the `<PaymentSuccess />` component using the Reacter Router as follows:

<img src="https://i.imgur.com/jMaNKJ6.png" alt="Opening PaymentSuccess Component" style="margin: 10px 0;"/>

In the backend server :

The `/api/create-order` endpoint uses the `razorpay.orders.create(options)` SDK function to call Razorpay API to create order.

The `/api/verify-payment` endpoint verifies authenticity of payment by matching the payment's `razorpay_signature` with the `expected` signature generated by the `crypto` library from a hash combination of `order_id` & `payment_id`. 


# 2. In a Nextjs App








# 3. Integrating WhatsApp Business

Our purpose is to to send WhatsApp order details to a restaurant owner from your after a successful payment. 

We can utilize a WhatsApp Business API provider (e.g., Twilio, Gupshup, MessageBird) to send WhatsApp messages. We'll use Twilio.

To use Twilio, you need an account with a Twilio Account SID (~ API key) and Auth Token  (~ API secret). These credentials authenticate your application to interact with Twilio’s APIs. You should use them as env variables.



