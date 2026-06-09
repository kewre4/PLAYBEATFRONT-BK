# PlayBeat Digital API Documentation

## Base URL

**Production**: `https://api.playbeat.digital`
**Development**: `http://localhost:3000`

---

## Authentication

All API requests should include:
```
Content-Type: application/json
Authorization: Bearer <token> (if required)
```

---

## Payment Methods

### Get Available Payment Methods

Returns all available payment methods for the current currency.

```
GET /api/payments/methods
```

**Response:**
```json
{
  "success": true,
  "methods": [
    {
      "id": "stripe",
      "label": "Debit / Credit Card",
      "type": "card",
      "icon": "stripe",
      "currencies": ["USD", "PKR"]
    },
    {
      "id": "jazzcash",
      "label": "JazzCash Wallet",
      "type": "wallet",
      "icon": "jazzcash",
      "currencies": ["PKR"]
    },
    {
      "id": "alfalah",
      "label": "Bank Alfalah Transfer",
      "type": "bank_transfer",
      "icon": "alfalah",
      "currencies": ["PKR"]
    },
    {
      "id": "meezan",
      "label": "Meezan Bank Transfer",
      "type": "bank_transfer",
      "icon": "meezan",
      "currencies": ["PKR"]
    }
  ]
}
```

---

## Stripe Payments (International)

### Create Checkout Session

Initiates a Stripe checkout for debit/credit card payments.

```
POST /api/payments/stripe/create-checkout
```

**Request Body:**
```json
{
  "items": [
    {
      "name": "Premium Subscription",
      "price": 29.99,
      "quantity": 1,
      "images": ["https://example.com/image.jpg"]
    }
  ],
  "orderId": "order-12345",
  "customerEmail": "customer@example.com",
  "successUrl": "https://playbeat.digital/payment/success",
  "cancelUrl": "https://playbeat.digital/payment/cancel"
}
```

**Response:**
```json
{
  "success": true,
  "sessionId": "cs_test_a1Bx2C3y4Z5x6c7v8b9n0m1l2k3j",
  "url": "https://checkout.stripe.com/pay/cs_test_..."
}
```

**Frontend Usage:**
```javascript
const response = await fetch('/api/payments/stripe/create-checkout', {
  method: 'POST',
  body: JSON.stringify({
    items: [...],
    orderId: 'order-123',
    customerEmail: 'user@example.com'
  })
});
const data = await response.json();
window.location.href = data.url; // Redirect to Stripe checkout
```

### Create Payment Intent

For custom Stripe form integration (server-side).

```
POST /api/payments/stripe/create-payment-intent
```

**Request Body:**
```json
{
  "amount": 2999,
  "currency": "usd",
  "orderId": "order-12345"
}
```

**Response:**
```json
{
  "success": true,
  "clientSecret": "pi_test_...c_secret_...",
  "paymentIntentId": "pi_test_..."
}
```

### Get Session Details

Retrieve details of a completed Stripe checkout session.

```
GET /api/payments/stripe/session/:sessionId
```

**Response:**
```json
{
  "success": true,
  "session": {
    "id": "cs_test_...",
    "payment_status": "paid",
    "customer_email": "customer@example.com",
    "metadata": {
      "orderId": "order-12345"
    }
  }
}
```

### Webhook Handler

Stripe sends events to this endpoint (configured in Stripe Dashboard).

```
POST /api/payments/stripe/webhook
```

**Events Handled:**
- `payment_intent.succeeded` — Payment completed
- `payment_intent.payment_failed` — Payment failed
- `checkout.session.completed` — Checkout completed

---

## JazzCash Payments (Pakistan)

### Initiate Payment

Start a JazzCash wallet payment.

```
POST /api/payments/jazzcash/initiate
```

**Request Body:**
```json
{
  "amount": 5000,
  "orderId": "order-12345",
  "mobileNumber": "03001234567",
  "description": "Payment for subscription"
}
```

**Response:**
```json
{
  "success": true,
  "txnRefNo": "JZ1234567890",
  "response": {
    "ProcessID": "12345",
    "ppAmount": "5000.00",
    "ppMobileNumber": "03001234567"
  }
}
```

**Frontend Usage:**
```javascript
const response = await fetch('/api/payments/jazzcash/initiate', {
  method: 'POST',
  body: JSON.stringify({
    amount: 5000,
    orderId: 'order-123',
    mobileNumber: '03001234567'
  })
});
const data = await response.json();
// Redirect user to JazzCash payment page or show confirmation
```

### Handle Callback

JazzCash redirects to this endpoint after payment.

```
POST /api/payments/jazzcash/callback
```

**Response:**
Redirects to frontend success/failed page with status.

### Get Payment Status

Check the status of a JazzCash transaction.

```
GET /api/payments/jazzcash/status/:txnRefNo
```

**Response:**
```json
{
  "success": true,
  "status": "paid",
  "amount": 5000,
  "txnRefNo": "JZ1234567890"
}
```

---

## Bank Alfalah Payments (Pakistan)

### Get Bank Details

Display bank account details for manual transfer.

```
GET /api/payments/alfalah/bank-details
```

**Response:**
```json
{
  "success": true,
  "bankDetails": {
    "bankName": "Bank Alfalah",
    "accountNumber": "00681011050474",
    "accountTitle": "PLAYBEAT DIGITAL (PRIVATE LIMITED)",
    "branchCode": "0010",
    "swiftCode": "ALFAPKKA"
  }
}
```

### Initiate Payment

Request a bank transfer reference.

```
POST /api/payments/alfalah/initiate
```

**Request Body:**
```json
{
  "amount": 10000,
  "orderId": "order-12345",
  "description": "Game top-up payment"
}
```

**Response:**
```json
{
  "success": true,
  "transactionId": "ALF20240101001",
  "bankDetails": {
    "accountNumber": "00681011050474",
    "accountTitle": "PLAYBEAT DIGITAL (PRIVATE LIMITED)"
  },
  "instructions": "Transfer the amount to the above account and reference this ID in the description"
}
```

### Handle Callback

Bank Alfalah redirects after payment confirmation.

```
POST /api/payments/alfalah/callback
```

---

## Meezan Bank Payments (Pakistan)

### Get Bank Details

Display Meezan Bank account details.

```
GET /api/payments/meezan/bank-details
```

**Response:**
```json
{
  "success": true,
  "bankDetails": {
    "bankName": "Meezan Bank",
    "iban": "PK86MEZN0015040115102971",
    "accountTitle": "PLAYBEAT DIGITAL PRIVATE LIMITED",
    "accountNumber": "0015040115102971"
  }
}
```

### Initiate Payment

Request a Meezan Bank transfer reference.

```
POST /api/payments/meezan/initiate
```

**Request Body:**
```json
{
  "amount": 10000,
  "orderId": "order-12345",
  "description": "Payment for services"
}
```

**Response:**
```json
{
  "success": true,
  "transactionId": "MEZ20240101001",
  "bankDetails": {
    "iban": "PK86MEZN0015040115102971",
    "accountTitle": "PLAYBEAT DIGITAL PRIVATE LIMITED"
  }
}
```

### Handle Success

Meezan Bank redirects after successful payment.

```
POST /api/payments/meezan/success
```

### Handle Failure

Meezan Bank redirects after failed payment.

```
POST /api/payments/meezan/failed
```

---

## Orders

### Create Order

Create a new order before payment.

```
POST /api/orders
```

**Request Body:**
```json
{
  "items": [
    {
      "name": "Product A",
      "price": 100,
      "quantity": 2
    }
  ],
  "customerEmail": "customer@example.com",
  "customerName": "John Doe",
  "gateway": "stripe"
}
```

**Response:**
```json
{
  "success": true,
  "order": {
    "_id": "order-12345",
    "items": [...],
    "total": 200,
    "status": "pending",
    "createdAt": "2024-01-01T00:00:00Z"
  }
}
```

### Get Order

Retrieve order details.

```
GET /api/orders/:orderId
```

**Response:**
```json
{
  "success": true,
  "order": {
    "_id": "order-12345",
    "items": [...],
    "total": 200,
    "status": "paid",
    "gateway": "stripe",
    "txnRef": "ch_1234567890",
    "createdAt": "2024-01-01T00:00:00Z"
  }
}
```

### Update Order Status

Update the payment status of an order.

```
PUT /api/orders/:orderId/status
```

**Request Body:**
```json
{
  "status": "paid",
  "txnRef": "ch_1234567890",
  "gateway": "stripe"
}
```

**Response:**
```json
{
  "success": true,
  "order": {
    "_id": "order-12345",
    "status": "paid"
  }
}
```

---

## Health Check

### Backend Status

Check if the backend is running and connected.

```
GET /api/health
```

**Response:**
```json
{
  "success": true,
  "status": "ok",
  "timestamp": "2024-01-01T00:00:00Z",
  "version": "1.0.0"
}
```

---

## Error Handling

### Error Response Format

All errors follow this format:

```json
{
  "success": false,
  "message": "Error description",
  "code": "ERROR_CODE",
  "details": {}
}
```

### Common Error Codes

| Code | Status | Description |
|------|--------|-------------|
| `INVALID_REQUEST` | 400 | Missing or invalid parameters |
| `AUTHENTICATION_FAILED` | 401 | Invalid or missing token |
| `FORBIDDEN` | 403 | Not authorized for this action |
| `NOT_FOUND` | 404 | Resource not found |
| `GATEWAY_ERROR` | 502 | Payment gateway error |
| `SERVER_ERROR` | 500 | Internal server error |

### Example Error

```json
{
  "success": false,
  "message": "Amount must be greater than 0",
  "code": "INVALID_REQUEST",
  "details": {
    "field": "amount",
    "value": -100
  }
}
```

---

## Rate Limiting

- **General API**: 100 requests per 15 minutes
- **Payment Endpoints**: 20 requests per 15 minutes

**Headers:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1234567890
```

---

## CORS

The backend accepts requests from:
- `https://playbeat.digital` (production)
- `http://localhost:8080` (development)

---

## Testing Payment Methods

### Stripe Test Cards
- **Visa**: `4242 4242 4242 4242`
- **Mastercard**: `5555 5555 5555 4444`
- **Expiry**: Any future date (e.g., 12/25)
- **CVC**: Any 3 digits

### JazzCash Testing
- Use sandbox credentials
- Test with actual Easypaisa/JazzCash number format

### Bank Transfers
- Manual payment, displays bank details
- Verify payment outside the system

---

## Webhook Events

### Stripe Webhook

Configure webhook endpoint in Stripe Dashboard:
```
Endpoint URL: https://api.playbeat.digital/api/payments/stripe/webhook
Events:
  - payment_intent.succeeded
  - payment_intent.payment_failed
  - checkout.session.completed
```

### Signature Verification

Always verify webhook signatures:
```javascript
const sig = req.headers['stripe-signature'];
const event = stripe.webhooks.constructEvent(
  req.body,
  sig,
  process.env.STRIPE_WEBHOOK_SECRET
);
```

---

## Code Examples

### Complete Checkout Flow (JavaScript)

```javascript
import * as api from './api';

async function checkout() {
  // 1. Get payment methods
  const methods = await api.getPaymentMethods();
  
  // 2. Create order
  const order = await api.createOrder({
    items: [...],
    customerEmail: 'user@example.com'
  });
  
  // 3. User selects payment method
  const selectedMethod = 'stripe';
  
  // 4. Process payment
  if (selectedMethod === 'stripe') {
    const checkout = await api.createStripeCheckout({
      items: [...],
      orderId: order._id,
      customerEmail: 'user@example.com'
    });
    window.location.href = checkout.url;
  } else if (selectedMethod === 'jazzcash') {
    const payment = await api.initiateJazzcash({
      amount: order.total,
      orderId: order._id
    });
    // Redirect to JazzCash
  }
}
```

---

## Support

For API issues:
1. Check response status and error code
2. Verify request parameters
3. Check backend logs on Render
4. Contact support team

