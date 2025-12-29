# Backend Testing Guide

This guide will help you test if your backend is working correctly.

## Prerequisites

1. **Database Setup**: Make sure PostgreSQL is running and configured
2. **Environment Variables**: Set up your `.env` file
3. **Dependencies**: Install all packages with `npm install`

## Quick Start Testing

### 1. Start the Backend Server

```bash
cd backend
npm run dev
```

You should see:
```
🚀 Server running on port 3000
📡 WebSocket server running on port 3001
🌍 Environment: development
```

### 2. Test Health Endpoint

The simplest test - check if the server is running:

**Using curl:**
```bash
curl http://localhost:3000/health
```

**Using browser:**
Open: http://localhost:3000/health

**Expected response:**
```json
{
  "status": "ok",
  "timestamp": "2025-11-22T12:00:00.000Z"
}
```

**Using PowerShell:**
```powershell
Invoke-WebRequest -Uri http://localhost:3000/health | Select-Object -ExpandProperty Content
```

### 3. Test API Endpoints

#### Get Token Info (No auth required)
```bash
curl http://localhost:3000/api/v1/token/info
```

#### Get Exchange Rate
```bash
curl http://localhost:3000/api/v1/token/rate
```

#### Get Analytics Overview
```bash
curl http://localhost:3000/api/v1/analytics/overview
```

#### Get Transaction Stats
```bash
curl http://localhost:3000/api/v1/transactions/stats/summary
```

## Testing with Authentication

### 1. Connect Wallet (Get JWT Token)

You'll need to sign a message with your wallet. Here's a test script:

```javascript
// In browser console (with MetaMask connected)
const message = `Sign this message to authenticate with MeowFi\n\nWallet: ${window.ethereum.selectedAddress}\nTimestamp: ${Date.now()}`;
const signature = await window.ethereum.request({
  method: 'personal_sign',
  params: [message, window.ethereum.selectedAddress]
});

// Then use the signature to authenticate
fetch('http://localhost:3000/api/v1/auth/wallet/connect', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    walletAddress: window.ethereum.selectedAddress,
    signature: signature,
    message: message
  })
})
.then(r => r.json())
.then(data => {
  console.log('Token:', data.data.token);
  localStorage.setItem('auth_token', data.data.token);
});
```

### 2. Use Token for Authenticated Requests

```bash
# Replace YOUR_TOKEN with the token from step 1
curl -H "Authorization: Bearer YOUR_TOKEN" http://localhost:3000/api/v1/users/me
```

## Testing Checklist

### Basic Functionality
- [ ] Health endpoint returns 200 OK
- [ ] API routes are accessible (not 404)
- [ ] CORS is working (no CORS errors in browser console)

### Database Connection
- [ ] Server starts without database errors
- [ ] Can query database (test with analytics endpoint)

### Authentication
- [ ] Can connect wallet and get JWT token
- [ ] Token works for authenticated endpoints
- [ ] Invalid token returns 401

### API Endpoints
- [ ] Token endpoints return data
- [ ] Analytics endpoints return data
- [ ] Transaction endpoints work
- [ ] User endpoints require authentication

## Common Issues

### Port Already in Use
```bash
# Windows: Find process using port 3000
netstat -ano | findstr :3000

# Kill the process (replace PID with actual process ID)
taskkill /PID <PID> /F
```

### Database Connection Error
- Check PostgreSQL is running
- Verify DATABASE_URL in .env is correct
- Run migrations: `npx prisma migrate dev`

### CORS Errors
- Check CORS_ORIGIN in .env matches your frontend URL
- Default is `http://localhost:8080`

## Automated Testing Script

See `test-api.js` for a simple Node.js test script.

## WebSocket Testing

Test WebSocket connection:

```javascript
// In browser console
const socket = io('http://localhost:3000', {
  auth: {
    token: localStorage.getItem('auth_token')
  }
});

socket.on('connect', () => {
  console.log('WebSocket connected!');
});

socket.on('analytics:update', (data) => {
  console.log('Analytics update:', data);
});
```

## Next Steps

Once basic testing passes:
1. Test with your frontend application
2. Test blockchain indexer (if running)
3. Test analytics worker (if running)
4. Load test with multiple requests

