# MeowFi Backend API

A comprehensive backend API for the MeowFi memecoin trading platform, built with Node.js, Express, TypeScript, and PostgreSQL.

## Features

- 🔐 **Authentication**: Wallet-based authentication with JWT tokens
- 📊 **Analytics**: Real-time analytics and metrics tracking
- 🔄 **Blockchain Integration**: Automatic transaction indexing and event monitoring
- 📈 **Analytics Dashboard**: Comprehensive analytics endpoints
- 👥 **User Management**: User profiles, preferences, and statistics
- 🏆 **Leaderboards**: Daily, weekly, monthly, and all-time leaderboards
- 🔔 **Notifications**: Real-time notifications via WebSocket
- ⚡ **Caching**: In-memory caching for improved performance
- 🔒 **Security**: Rate limiting, input validation, and admin controls

## Tech Stack

- **Runtime**: Node.js with TypeScript
- **Framework**: Express.js
- **Database**: PostgreSQL with Prisma ORM
- **Cache**: In-memory cache
- **WebSocket**: Socket.IO
- **Blockchain**: Ethers.js
- **Authentication**: JWT

## Prerequisites

- Node.js 18+ 
- PostgreSQL 14+
- Access to Sepolia testnet RPC endpoint

## Installation

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Set up environment variables:**
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

3. **Set up database:**
   ```bash
   # Generate Prisma client
   npx prisma generate
   
   # Run migrations
   npx prisma migrate dev
   
   # (Optional) Seed database
   npm run db:seed
   ```

## Running the Application

### Development Mode

```bash
# Start the API server
npm run dev

# In separate terminals, start workers:
npm run worker:indexer    # Blockchain event indexer
npm run worker:analytics   # Analytics calculation worker
```

### Production Mode

```bash
# Build the application
npm run build

# Start the server
npm start

# Start workers (using PM2 or similar)
npm run worker:indexer
npm run worker:analytics
```

## API Endpoints

### Authentication
- `POST /api/v1/auth/wallet/connect` - Connect wallet and get JWT token
- `POST /api/v1/auth/wallet/verify` - Verify wallet signature
- `POST /api/v1/auth/refresh` - Refresh JWT token
- `POST /api/v1/auth/logout` - Logout and invalidate session

### Users
- `GET /api/v1/users/me` - Get current user profile
- `GET /api/v1/users/:walletAddress` - Get user by wallet address
- `PUT /api/v1/users/me` - Update user profile
- `GET /api/v1/users/me/preferences` - Get user preferences
- `PUT /api/v1/users/me/preferences` - Update user preferences
- `GET /api/v1/users/me/stats` - Get user statistics

### Transactions
- `GET /api/v1/transactions` - List transactions (with pagination)
- `GET /api/v1/transactions/:txHash` - Get transaction by hash
- `GET /api/v1/transactions/user/:walletAddress` - Get user transactions
- `POST /api/v1/transactions/track` - Track a new transaction
- `GET /api/v1/transactions/stats/summary` - Get transaction statistics

### Token
- `GET /api/v1/token/info` - Get token information
- `GET /api/v1/token/metrics` - Get token metrics history
- `GET /api/v1/token/supply` - Get token supply information
- `GET /api/v1/token/rate` - Get current exchange rate
- `GET /api/v1/token/price` - Get token price

### Analytics
- `GET /api/v1/analytics/overview` - Get overview statistics
- `GET /api/v1/analytics/volume` - Get volume statistics
- `GET /api/v1/analytics/users` - Get user statistics
- `GET /api/v1/analytics/transactions` - Get transaction analytics
- `GET /api/v1/analytics/price-history` - Get price history
- `GET /api/v1/analytics/trends` - Get trend data

### Leaderboard
- `GET /api/v1/leaderboard/:period` - Get leaderboard (DAILY, WEEKLY, MONTHLY, ALL_TIME)
- `GET /api/v1/leaderboard/user/:walletAddress` - Get user rank

### Notifications
- `GET /api/v1/notifications` - Get user notifications
- `GET /api/v1/notifications/unread` - Get unread notification count
- `PUT /api/v1/notifications/:id/read` - Mark notification as read
- `PUT /api/v1/notifications/read-all` - Mark all notifications as read
- `DELETE /api/v1/notifications/:id` - Delete notification

### Admin (requires admin API key)
- `GET /api/v1/admin/dashboard` - Admin dashboard data
- `GET /api/v1/admin/users` - List all users
- `GET /api/v1/admin/transactions` - List all transactions
- `POST /api/v1/admin/rate/update` - Update exchange rate
- `GET /api/v1/admin/metrics` - Get detailed metrics
- `GET /api/v1/admin/logs` - Get admin action logs
- `POST /api/v1/admin/notifications/broadcast` - Broadcast notification

## WebSocket Events

Connect to the WebSocket server to receive real-time updates:

### Client → Server
- `subscribe:transaction` - Subscribe to transaction updates
- `subscribe:notifications` - Subscribe to notifications

### Server → Client
- `transaction:update` - Transaction status update
- `transaction:new` - New transaction created
- `notification:new` - New notification
- `analytics:update` - Analytics data update
- `rate:update` - Exchange rate update
- `system:announcement` - System announcement

## Workers

### Blockchain Indexer
Monitors the blockchain for new events and indexes them into the database.

```bash
npm run worker:indexer
```

### Analytics Worker
Calculates metrics and leaderboards on a schedule.

```bash
npm run worker:analytics
```

## Database Migrations

```bash
# Create a new migration
npx prisma migrate dev --name migration_name

# Apply migrations in production
npx prisma migrate deploy

# Generate Prisma client after schema changes
npx prisma generate
```

## Environment Variables

See `.env.example` for all required environment variables.

## Security

- All admin endpoints require `X-Admin-Key` header
- Rate limiting is applied to all endpoints
- JWT tokens expire after 7 days (configurable)
- Input validation using Zod schemas
- CORS is configured for frontend origin

## Development

```bash
# Run in development mode with hot reload
npm run dev

# Run linting
npm run lint

# Run tests
npm test
```

## Production Deployment

1. Set all environment variables
2. Build the application: `npm run build`
3. Run database migrations: `npx prisma migrate deploy`
4. Start the server: `npm start`
5. Start workers using a process manager (PM2, systemd, etc.)

## License

MIT

