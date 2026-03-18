# User Management API - Quick Start Guide

## Overview

The User Management API allows you to programmatically create, retrieve, update, and delete user accounts in your organization. This guide covers the essential endpoints to get you started with basic user operations.

**Base URL:** `https://api.example-saas.com/v1`

**Authentication:** All API requests require a valid API key passed in the `Authorization` header.

---

## Authentication

Include your API key in the header of every request:
```http
Authorization: Bearer YOUR_API_KEY
```

**Example:**
```bash
curl -H "Authorization: Bearer sk_live_abc123xyz" \
     https://api.example-saas.com/v1/users
```

---

## Quick Start: Create Your First User

Follow these steps to create a user account via the API:

### 1. Generate an API Key

Navigate to **Settings > API Keys** in your dashboard and click **Generate New Key**. Store this key securely—it won't be shown again.

### 2. Make Your First Request

Use the `/users` endpoint to create a new user:

**Endpoint:** `POST /users`

**Request Body:**
```json
{
  "email": "jane.doe@example.com",
  "first_name": "Jane",
  "last_name": "Doe",
  "role": "member"
}
```

**Example Request:**
```bash
curl -X POST https://api.example-saas.com/v1/users \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "jane.doe@example.com",
    "first_name": "Jane",
    "last_name": "Doe",
    "role": "member"
  }'
```

**Response (200 OK):**
```json
{
  "id": "usr_abc123",
  "email": "jane.doe@example.com",
  "first_name": "Jane",
  "last_name": "Doe",
  "role": "member",
  "status": "active",
  "created_at": "2025-01-13T18:30:00Z"
}
```

---

## Core Endpoints

### List All Users

Retrieve a paginated list of all users in your organization.

**Endpoint:** `GET /users`

**Query Parameters:**

| Parameter | Type    | Description                          | Default |
|-----------|---------|--------------------------------------|---------|
| limit     | integer | Number of users to return (max 100)  | 20      |
| offset    | integer | Number of users to skip              | 0       |
| role      | string  | Filter by role: admin, member, guest | all     |

**Example Request:**
```bash
curl -X GET "https://api.example-saas.com/v1/users?limit=10&role=member" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Response (200 OK):**
```json
{
  "data": [
    {
      "id": "usr_abc123",
      "email": "jane.doe@example.com",
      "first_name": "Jane",
      "last_name": "Doe",
      "role": "member",
      "status": "active"
    }
  ],
  "has_more": false,
  "total_count": 1
}
```

---

### Retrieve a Specific User

Get details for a single user by their ID.

**Endpoint:** `GET /users/{user_id}`

**Path Parameters:**

| Parameter | Type   | Description           |
|-----------|--------|-----------------------|
| user_id   | string | The unique user ID    |

**Example Request:**
```bash
curl -X GET https://api.example-saas.com/v1/users/usr_abc123 \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Response (200 OK):**
```json
{
  "id": "usr_abc123",
  "email": "jane.doe@example.com",
  "first_name": "Jane",
  "last_name": "Doe",
  "role": "member",
  "status": "active",
  "created_at": "2025-01-13T18:30:00Z",
  "last_login": "2025-01-13T19:15:00Z"
}
```

---

### Update a User

Modify user details such as name, email, or role.

**Endpoint:** `PATCH /users/{user_id}`

**Request Body (all fields optional):**
```json
{
  "email": "jane.smith@example.com",
  "first_name": "Jane",
  "last_name": "Smith",
  "role": "admin"
}
```

**Example Request:**
```bash
curl -X PATCH https://api.example-saas.com/v1/users/usr_abc123 \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "role": "admin"
  }'
```

**Response (200 OK):**
```json
{
  "id": "usr_abc123",
  "email": "jane.doe@example.com",
  "first_name": "Jane",
  "last_name": "Doe",
  "role": "admin",
  "status": "active",
  "updated_at": "2025-01-13T20:00:00Z"
}
```

---

### Delete a User

Permanently remove a user from your organization.

**Endpoint:** `DELETE /users/{user_id}`

**Example Request:**
```bash
curl -X DELETE https://api.example-saas.com/v1/users/usr_abc123 \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Response (204 No Content):**

No body returned. The user has been successfully deleted.

---

## Error Handling

The API uses standard HTTP status codes to indicate success or failure.

### Common Status Codes

| Code | Meaning              | Description                                      |
|------|----------------------|--------------------------------------------------|
| 200  | OK                   | Request succeeded                                |
| 201  | Created              | Resource successfully created                    |
| 204  | No Content           | Request succeeded, no content returned           |
| 400  | Bad Request          | Invalid request parameters                       |
| 401  | Unauthorized         | Missing or invalid API key                       |
| 404  | Not Found            | Resource does not exist                          |
| 429  | Too Many Requests    | Rate limit exceeded                              |
| 500  | Internal Server Error| Something went wrong on our end                  |

### Error Response Format

When an error occurs, the API returns a JSON response with details:
```json
{
  "error": {
    "type": "invalid_request",
    "message": "The email field is required",
    "code": "missing_required_field"
  }
}
```

---

## Rate Limits

API requests are limited to **1,000 requests per hour** per API key. Rate limit information is included in response headers:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 995
X-RateLimit-Reset: 1673650800
```

If you exceed the rate limit, you'll receive a `429 Too Many Requests` response. Wait until the reset time before making additional requests.

---

## Best Practices

### 1. Use Pagination for Large Datasets
When retrieving user lists, use the `limit` and `offset` parameters to paginate results rather than fetching all users at once.

### 2. Handle Errors Gracefully
Always check response status codes and implement retry logic with exponential backoff for transient errors (5xx status codes).

### 3. Secure Your API Keys
- Never commit API keys to version control
- Rotate keys regularly
- Use environment variables to store keys in production

### 4. Validate Input Data
Validate user data on your end before sending requests to reduce unnecessary API calls and improve response times.

---

## Next Steps

- **[Complete API Reference](link)** - Explore all available endpoints and parameters
- **[Webhook Guide](link)** - Set up real-time notifications for user events
- **[Authentication Deep Dive](link)** - Learn about OAuth and advanced authentication methods
- **[SDKs and Libraries](link)** - Use our official client libraries for Python, JavaScript, and Ruby

---

## Support

Need help? Contact our developer support team:

- **Email:** api-support@example-saas.com
- **Documentation:** docs.example-saas.com
- **Status Page:** status.example-saas.com

---

*Last updated: January 13, 2025*