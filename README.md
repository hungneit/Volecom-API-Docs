# Volecom Dealer API Documentation

## Overview

The Volecom Dealer API provides programmatic access to manage TikTok and Facebook advertising accounts. This RESTful API uses API key authentication and returns JSON responses.

**Base URL:** `https://your-domain.com/api/dealer`

**Authentication:** All endpoints require an API key passed via the `X-API-Key` header.

---

## Authentication

### API Key Header

All API requests must include your API key in the request header:

```
X-API-Key: your-api-key-here
```

**Response Codes:**
- `200` - Success
- `400` - Bad Request
- `403` - Invalid or missing API Key
- `404` - Resource not found
- `500` - Internal Server Error

---

## TikTok Ad Accounts

### 1. Get TikTok Accounts List

Retrieves all TikTok advertising accounts associated with the dealer.

**Endpoint:** `GET /tiktok/accounts`

**Headers:**
```
X-API-Key: your-api-key-here
```

**Response:**

```json
{
  "code": "200",
  "message": "TikTok accounts fetched successfully",
  "data": [
    {
      "id": 123,
      "uid": "7123456789012345678",
      "status": "Approved",
      "balance": 1500.50,
      "validBalance": 1450.00,
      "totalMoney": 5000.00,
      "timezone": "Etc/GMT+7",
      "autoDepositEnabled": true,
      "autoDepositThreshold": 100.00,
      "autoDepositAmount": 500.00,
      "partners": [
        {
          "idbc": "BC123456"
        },
        {
          "idbc": "BC789012"
        }
      ]
    }
  ]
}
```

**Response Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `id` | Integer | Internal account ID |
| `uid` | String | TikTok advertiser ID |
| `status` | String | Account status (e.g., "Approved", "Pending") |
| `balance` | Number | Current account balance (USD) |
| `validBalance` | Number | Available balance for spending (USD) |
| `totalMoney` | Number | Total money deposited to date (USD) |
| `timezone` | String | Account timezone |
| `autoDepositEnabled` | Boolean | Whether auto-deposit is enabled |
| `autoDepositThreshold` | Number | Balance threshold for auto-deposit (USD) |
| `autoDepositAmount` | Number | Amount to deposit automatically (USD) |
| `partners` | Array | List of BC partners shared with this account |

**Error Response:**

```json
{
  "code": "403",
  "message": "Invalid API Key"
}
```

---

### 2. Deposit/Withdraw from TikTok Account

Perform a deposit or withdrawal transaction on a TikTok advertising account.

**Endpoint:** `POST /tiktok/accounts/{uid}/transaction`

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | String | TikTok advertiser ID |

**Headers:**
```
X-API-Key: your-api-key-here
Content-Type: application/json
```

**Request Body:**

```json
{
  "amount": 100.00,
  "type": "Top-up"
}
```

**Request Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `amount` | Number | Yes | Transaction amount (must be positive) |
| `type` | String | Yes | Transaction type: "Top-up" or "Withdraw" |

**Success Response (Top-up):**

```json
{
  "code": "200",
  "status": "SUCCESS",
  "message": "Successfully topped up the ad account!"
}
```

**Pending Response:**

```json
{
  "code": "202",
  "status": "PENDING",
  "message": "Transaction is being processed"
}
```

**Error Responses:**

```json
{
  "code": "404",
  "message": "Account not found or does not belong to the dealer."
}
```

```json
{
  "code": "400",
  "message": "Invalid or missing 'amount'."
}
```

```json
{
  "code": "400",
  "message": "Transaction 'type' must be 'Top-up' or 'Withdraw'."
}
```

```json
{
  "code": "400",
  "status": "FAILED",
  "message": "Your wallet balance is not enough. Please top up!"
}
```

**Notes:**
- The amount is deducted from your dealer wallet balance
- Transaction type must be exactly "Top-up" or "Withdraw" (case-insensitive)
- Minimum deposit amount may apply (check with support)

---

### 3. Add BC Partner to TikTok Account

Share a TikTok ad account with a Business Center (BC) partner.

**Endpoint:** `POST /tiktok/accounts/{uid}/partners`

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | String | TikTok advertiser ID |

**Headers:**
```
X-API-Key: your-api-key-here
Content-Type: application/json
```

**Request Body:**

```json
{
  "idbc": "BC123456"
}
```

**Request Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `idbc` | String | Yes | Business Center ID to share with |

**Success Response:**

```json
{
  "code": "200",
  "message": "Partner added successfully",
  "data": {
    "idbc": "BC123456"
  }
}
```

**Error Responses:**

```json
{
  "code": "404",
  "message": "Account not found"
}
```

```json
{
  "code": "400",
  "message": "Missing partner IDBC"
}
```

**Notes:**
- The account status will be automatically set to "Approved" after adding a partner
- Each account can be shared with multiple BC partners

---

### 4. Remove BC Partner from TikTok Account

Remove a Business Center partner from a TikTok ad account.

**Endpoint:** `DELETE /tiktok/accounts/{uid}/partners/{idbc}`

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | String | TikTok advertiser ID |
| `idbc` | String | Business Center ID to remove |

**Headers:**
```
X-API-Key: your-api-key-here
```

**Success Response:**

```json
{
  "code": "200",
  "message": "Partner removed successfully",
  "data": {
    "idbc": "BC123456"
  }
}
```

**Error Responses:**

```json
{
  "code": "404",
  "message": "Account not found"
}
```

```json
{
  "code": "400",
  "message": "Partner not found or removal failed"
}
```

---

## Facebook Ad Accounts

### 5. Get Facebook Accounts List

Retrieves all Facebook advertising accounts associated with the dealer.

**Endpoint:** `GET /facebook/accounts`

**Headers:**
```
X-API-Key: your-api-key-here
```

**Response:**

```json
{
  "code": "200",
  "message": "Facebook accounts fetched successfully",
  "data": [
    {
      "id": 456,
      "adsId": 123456789012345,
      "facebookAdsId": "act_123456789012345",
      "adsStatus": "Active",
      "balance": 250.75,
      "spendingCap": 1000.00,
      "totalMoney": 800.00,
      "timezone": "America/Los_Angeles",
      "currency": "USD"
    }
  ]
}
```

**Response Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `id` | Integer | Internal account ID |
| `adsId` | Long | Numeric Facebook Ad Account ID |
| `facebookAdsId` | String | Facebook Ad Account ID with "act_" prefix |
| `adsStatus` | String | Account status (e.g., "Active", "Disabled") |
| `balance` | Number | Current account balance (USD) |
| `spendingCap` | Number | Spending limit cap (USD) |
| `totalMoney` | Number | Total money deposited to date (USD) |
| `timezone` | String | Account timezone |
| `currency` | String | Account currency code |

**Error Response:**

```json
{
  "code": "403",
  "message": "Invalid API Key"
}
```

---

### 6. Top-up Facebook Ad Account

Deposit funds into a Facebook advertising account.

**Endpoint:** `POST /facebook/accounts/{adsId}/topup`

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `adsId` | Long | Facebook Ad Account numeric ID |

**Headers:**
```
X-API-Key: your-api-key-here
Content-Type: application/json
```

**Request Body:**

```json
{
  "amount": 100.00
}
```

**Request Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `amount` | Number | Yes | Top-up amount in USD (minimum $10, must be whole number) |

**Success Response:**

```json
{
  "response": "alert-success",
  "message": "Successfully topped up the Facebook ad account!"
}
```

**Pending Response:**

```json
{
  "response": "alert-pending",
  "message": "Top-up request is being processed"
}
```

**Error Responses:**

```json
{
  "response": "not-found",
  "message": "Account not found"
}
```

```json
{
  "response": "insufficient-balance",
  "message": "Your wallet balance is not enough. Please top up!"
}
```

```json
{
  "response": "alert-fail",
  "message": "Top-up failed. Please contact support."
}
```

```json
{
  "response": "nolimit-bad-request",
  "message": "Invalid request to payment provider"
}
```

**HTTP Status Codes:**

| Status | Response Type | Description |
|--------|--------------|-------------|
| `200` | alert-success | Top-up completed successfully |
| `202` | alert-pending | Top-up is being processed |
| `400` | alert-fail, insufficient-balance, nolimit-bad-request | Request failed or invalid |
| `404` | not-found | Account not found |

**Notes:**
- Minimum top-up amount is $10 USD
- Amount must be a whole number (no decimals)
- A top-up fee percentage may be applied based on account settings
- The total amount (amount + fee) will be deducted from your dealer wallet

---

## Common Response Codes

| Code | Status | Description |
|------|--------|-------------|
| `200` | OK | Request successful |
| `202` | Accepted | Request accepted and being processed |
| `400` | Bad Request | Invalid request parameters |
| `403` | Forbidden | Invalid or missing API key |
| `404` | Not Found | Resource not found or access denied |
| `500` | Internal Server Error | Server error occurred |

---

## Error Handling

All error responses follow a consistent format:

```json
{
  "code": "400",
  "message": "Detailed error message"
}
```

or

```json
{
  "response": "error-type",
  "message": "Detailed error message"
}
```

**Common Error Messages:**

- `"Invalid API Key"` - Your API key is incorrect or expired
- `"Account not found"` - The account doesn't exist or doesn't belong to you
- `"Invalid or missing 'amount'"` - The amount parameter is missing or invalid
- `"Your wallet balance is not enough. Please top up!"` - Insufficient dealer wallet balance

---

## Rate Limiting

**Note:** Rate limiting policies may be enforced to ensure fair usage. Contact support for details on current limits.

---

## Best Practices

1. **Secure Your API Key:** Never expose your API key in client-side code or public repositories
2. **Handle Errors Gracefully:** Always check response codes and handle errors appropriately
3. **Use HTTPS:** All API calls should be made over HTTPS
4. **Monitor Balance:** Regularly check your dealer wallet balance before initiating transactions
5. **Retry Logic:** Implement exponential backoff for failed requests
6. **Cache Account Lists:** Cache account lists and update periodically to reduce API calls

---

## Support

For technical support or questions about the API:
- **Email:** volecomagency@gmail.com
- **Documentation Updates:** Check this document regularly for updates

---

## Changelog

### Version 1.0.0 (Current)
- Initial release
- TikTok ad account management
- Facebook ad account management
- Deposit/withdrawal functionality
- BC partner management

---

**Last Updated:** February 4, 2026
