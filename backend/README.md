# 🔧 StellarBank Backend API

> High-performance Node.js API server powering cross-border remittance operations on Stellar blockchain

[![Node.js](https://img.shields.io/badge/Node.js-16%2B-green)](https://nodejs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3%2B-blue)](https://typescriptlang.org)
[![Express](https://img.shields.io/badge/Express-4.18%2B-black)](https://expressjs.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15%2B-blue)](https://postgresql.org)
[![Redis](https://img.shields.io/badge/Redis-7%2B-red)](https://redis.io)
[![Stellar](https://img.shields.io/badge/Stellar-SDK-brightgreen)](https://stellar.org)

## 🌟 Features

### 🔐 **Enterprise Security**
- JWT authentication with refresh tokens
- Multi-factor authentication (TOTP/SMS)
- Rate limiting and DDoS protection
- Input validation and sanitization
- OWASP security best practices

### 💸 **Remittance Processing**
- Real-time Stellar blockchain integration
- Multi-currency transaction support
- Automated compliance checking (AML/KYC)
- Transaction status tracking
- Fee calculation and optimization

### 📊 **Comprehensive APIs**
- RESTful API design
- OpenAPI/Swagger documentation
- Real-time WebSocket connections
- Webhook delivery system
- GraphQL query support

### 🎯 **Performance & Scalability**
- Redis caching layer
- Background job processing
- Database connection pooling
- Horizontal scaling support
- Load balancer ready

### 📈 **Monitoring & Analytics**
- Structured logging with Winston
- Performance metrics collection
- Error tracking and alerting
- Transaction analytics
- Compliance reporting

---

## 🚀 Quick Start

### Prerequisites
```bash
# Required
Node.js 16+
PostgreSQL 15+
Redis 7+
npm 8+ or yarn 1.22+

# Optional
Docker & Docker Compose
pgAdmin (database management)
```

### Installation
```bash
# Clone the repository
git clone https://github.com/presidojay1/StellarBank.git
cd StellarBank/backend

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env
# Edit .env with your configuration

# Set up database
npm run migrate
npm run seed

# Start development server
npm run dev
```

### 🌐 API Endpoints
- **Base URL:** http://localhost:8000/api/v1
- **Documentation:** http://localhost:8000/docs
- **Health Check:** http://localhost:8000/health

---

## 🏗️ Project Structure

```
backend/
├── 📁 src/
│   ├── 📁 controllers/        # Request handlers
│   │   ├── auth.controller.ts
│   │   ├── transaction.controller.ts
│   │   ├── user.controller.ts
│   │   └── wallet.controller.ts
│   ├── 📁 middleware/         # Express middleware
│   │   ├── auth.middleware.ts
│   │   ├── validation.middleware.ts
│   │   └── rate-limit.middleware.ts
│   ├── 📁 models/            # Database models
│   │   ├── User.model.ts
│   │   ├── Transaction.model.ts
│   │   └── Wallet.model.ts
│   ├── 📁 routes/            # API routes
│   │   ├── auth.routes.ts
│   │   ├── transaction.routes.ts
│   │   └── user.routes.ts
│   ├── 📁 services/          # Business logic
│   │   ├── stellar.service.ts
│   │   ├── notification.service.ts
│   │   └── compliance.service.ts
│   ├── 📁 utils/             # Utility functions
│   │   ├── logger.ts
│   │   ├── validators.ts
│   │   └── stellar-utils.ts
│   ├── 📁 config/            # Configuration
│   │   ├── database.ts
│   │   ├── redis.ts
│   │   └── stellar.ts
│   └── 📄 server.ts          # Entry point
├── 📁 migrations/           # Database migrations
├── 📁 seeds/               # Database seeds
├── 📁 tests/               # Test files
│   ├── unit/               # Unit tests
│   ├── integration/        # Integration tests
│   └── e2e/               # End-to-end tests
├── 📁 docs/               # API documentation
├── 📄 Dockerfile         # Docker configuration
├── 📄 docker-compose.yml # Development environment
└── 📄 package.json       # Dependencies and scripts
```

---

## 🛠️ Technology Stack

### **Core Framework**
- **Node.js 16+** - JavaScript runtime
- **Express.js 4.18** - Web application framework
- **TypeScript 5.3** - Type-safe JavaScript

### **Database & Caching**
- **PostgreSQL 15** - Primary database
- **Redis 7** - Caching and session storage
- **Knex.js** - SQL query builder
- **Bull Queue** - Background job processing

### **Blockchain Integration**
- **Stellar SDK 11.2** - Stellar blockchain interaction
- **Soroban** - Smart contract deployment
- **Horizon API** - Transaction monitoring

### **Authentication & Security**
- **Passport.js** - Authentication middleware
- **JWT** - Token-based authentication
- **bcrypt** - Password hashing
- **Helmet.js** - Security headers
- **express-rate-limit** - Rate limiting

### **Communication**
- **Twilio** - SMS notifications
- **Nodemailer** - Email delivery
- **Socket.io** - Real-time communication
- **Webhook** - Event-driven notifications

### **Monitoring & Logging**
- **Winston** - Structured logging
- **Morgan** - HTTP request logging
- **Sentry** - Error tracking
- **Prometheus** - Metrics collection

---

## 📚 API Documentation

### **Authentication Endpoints**
```bash
POST   /api/v1/auth/register     # User registration
POST   /api/v1/auth/login        # User login  
POST   /api/v1/auth/refresh      # Token refresh
POST   /api/v1/auth/logout       # User logout
POST   /api/v1/auth/verify       # Email/phone verification
POST   /api/v1/auth/2fa/enable   # Enable 2FA
POST   /api/v1/auth/2fa/verify   # Verify 2FA code
```

### **User Management**
```bash
GET    /api/v1/users/profile     # Get user profile
PUT    /api/v1/users/profile     # Update profile
POST   /api/v1/users/kyc         # Submit KYC documents
GET    /api/v1/users/kyc/status  # Get KYC status
POST   /api/v1/users/settings    # Update settings
```

### **Wallet Operations**
```bash
GET    /api/v1/wallets           # List user wallets
POST   /api/v1/wallets           # Create new wallet
GET    /api/v1/wallets/:id       # Get wallet details
POST   /api/v1/wallets/:id/fund  # Fund wallet
POST   /api/v1/wallets/:id/send  # Send payment
GET    /api/v1/wallets/:id/history # Transaction history
```

### **Transaction Processing**
```bash
POST   /api/v1/transactions      # Create transaction
GET    /api/v1/transactions      # List transactions
GET    /api/v1/transactions/:id  # Get transaction details
POST   /api/v1/transactions/:id/cancel # Cancel transaction
GET    /api/v1/transactions/rates # Get exchange rates
POST   /api/v1/transactions/quote # Get transaction quote
```

### **Compliance & Reporting**
```bash
GET    /api/v1/compliance/limits # Get transaction limits
POST   /api/v1/compliance/report # Report suspicious activity
GET    /api/v1/analytics/summary # Transaction analytics
GET    /api/v1/analytics/volume  # Volume analytics
```

---

## 🔧 Configuration

### **Environment Variables**
```bash
# .env
NODE_ENV=development
PORT=8000
API_VERSION=v1

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/stellarbank
DB_HOST=localhost
DB_PORT=5432
DB_NAME=stellarbank
DB_USER=stellarbank
DB_PASS=your_password

# Redis
REDIS_URL=redis://localhost:6379
REDIS_HOST=localhost
REDIS_PORT=6379

# JWT
JWT_SECRET=your_jwt_secret_key_here
JWT_EXPIRES_IN=24h
JWT_REFRESH_EXPIRES_IN=7d

# Stellar
STELLAR_NETWORK=testnet
STELLAR_HORIZON_URL=https://horizon-testnet.stellar.org
STELLAR_PASSPHRASE=Test SDF Network ; September 2015
STELLAR_ISSUER_SECRET=your_issuer_secret_key

# Twilio (SMS)
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_token
TWILIO_PHONE_NUMBER=+1234567890

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_email_password

# External APIs
COINGECKO_API_KEY=your_coingecko_key
EXCHANGE_RATES_API_KEY=your_rates_key

# Monitoring
SENTRY_DSN=your_sentry_dsn
LOG_LEVEL=info
```

### **Database Configuration**
```javascript
// knexfile.ts
module.exports = {
  development: {
    client: 'postgresql',
    connection: {
      host: process.env.DB_HOST,
      port: process.env.DB_PORT,
      database: process.env.DB_NAME,
      user: process.env.DB_USER,
      password: process.env.DB_PASS,
    },
    migrations: {
      directory: './migrations',
    },
    seeds: {
      directory: './seeds',
    },
  },
};
```

---

## 🗄️ Database Schema

### **Users Table**
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  phone VARCHAR(20) UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  date_of_birth DATE,
  country_code VARCHAR(3) NOT NULL,
  kyc_status VARCHAR(20) DEFAULT 'pending',
  kyc_level INTEGER DEFAULT 1,
  is_active BOOLEAN DEFAULT true,
  two_fa_enabled BOOLEAN DEFAULT false,
  two_fa_secret VARCHAR(255),
  email_verified BOOLEAN DEFAULT false,
  phone_verified BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

### **Wallets Table**
```sql
CREATE TABLE wallets (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  stellar_public_key VARCHAR(56) UNIQUE NOT NULL,
  stellar_secret_key_encrypted TEXT NOT NULL,
  currency_code VARCHAR(10) NOT NULL,
  balance DECIMAL(20,7) DEFAULT 0,
  is_primary BOOLEAN DEFAULT false,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

### **Transactions Table**
```sql
CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  stellar_tx_hash VARCHAR(64) UNIQUE,
  sender_wallet_id UUID REFERENCES wallets(id),
  recipient_wallet_id UUID REFERENCES wallets(id),
  sender_address VARCHAR(56) NOT NULL,
  recipient_address VARCHAR(56) NOT NULL,
  amount DECIMAL(20,7) NOT NULL,
  fee DECIMAL(20,7) NOT NULL,
  currency_code VARCHAR(10) NOT NULL,
  exchange_rate DECIMAL(15,8),
  status VARCHAR(20) DEFAULT 'pending',
  transaction_type VARCHAR(20) NOT NULL,
  memo TEXT,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

---

## 🧪 Testing

### **Unit Tests**
```bash
# Run all tests
npm run test

# Watch mode
npm run test:watch

# Coverage report
npm run test:coverage
```

### **Integration Tests**
```bash
# Run integration tests
npm run test:integration

# Test specific service
npm run test -- --testNamePattern="StellarService"
```

### **E2E Tests**
```bash
# Run end-to-end tests
npm run test:e2e

# Test specific endpoint
npm run test:e2e -- --testNamePattern="auth"
```

### **Test Coverage Goals**
- **Unit Tests:** >90%
- **Integration Tests:** >85%
- **E2E Tests:** Critical API flows

---

## 📦 Build & Deployment

### **Local Build**
```bash
# Build TypeScript
npm run build

# Start production server
npm run start:prod
```

### **Docker Deployment**
```bash
# Build Docker image
npm run docker:build

# Run with Docker Compose
docker-compose up -d

# Scale services
docker-compose up -d --scale api=3
```

### **Production Deployment**
```bash
# Deploy to AWS ECS
aws ecs update-service --cluster stellarbank --service api

# Deploy to Kubernetes
kubectl apply -f k8s/

# Deploy to Heroku
git push heroku main
```

---

## 🔐 Security

### **Authentication Flow**
```
1. User Registration
   ├── Email/phone validation
   ├── Password strength check
   ├── KYC document upload
   └── Account activation

2. Login Process
   ├── Credential verification
   ├── 2FA challenge (if enabled)
   ├── JWT token generation
   └── Session management

3. Protected Endpoints
   ├── JWT token validation
   ├── Permission checking
   ├── Rate limit enforcement
   └── Audit logging
```

### **Security Measures**
- **Input Validation:** Zod schema validation
- **SQL Injection:** Parameterized queries
- **XSS Prevention:** Input sanitization
- **CSRF Protection:** CSRF tokens
- **Rate Limiting:** IP-based limits
- **Encryption:** AES-256 for sensitive data

---

## 📊 Monitoring & Logging

### **Structured Logging**
```javascript
// Logger configuration
import winston from 'winston';

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'stellarbank-api' },
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' }),
  ],
});
```

### **Performance Metrics**
- Request/response times
- Database query performance
- Redis cache hit rates
- Stellar transaction success rates
- API endpoint usage statistics

---

## 🤝 Contributing

### **Development Workflow**
1. Fork the repository
2. Create feature branch: `git checkout -b feature/amazing-feature`
3. Write tests for new functionality
4. Implement the feature
5. Run tests: `npm run test`
6. Run linting: `npm run lint:fix`
7. Commit changes: `git commit -m 'Add amazing feature'`
8. Push to branch: `git push origin feature/amazing-feature`
9. Create Pull Request

### **Code Standards**
- Follow TypeScript best practices
- Use ESLint and Prettier configurations
- Write comprehensive tests
- Document API endpoints
- Update changelog

---

## 🔗 Resources

- **🌐 Stellar SDK:** [https://stellar.github.io/js-stellar-sdk/](https://stellar.github.io/js-stellar-sdk/)
- **📖 Express.js:** [https://expressjs.com/](https://expressjs.com/)
- **🗄️ PostgreSQL:** [https://www.postgresql.org/docs/](https://www.postgresql.org/docs/)
- **⚡ Redis:** [https://redis.io/documentation](https://redis.io/documentation)
- **📊 API Design:** [RESTful API Guidelines](https://github.com/microsoft/api-guidelines)

---

## 📄 License

MIT License - see [LICENSE](../LICENSE) file for details.

---

<div align="center">

**Built with ⚡ by the StellarBank Backend Team**

*Secure, scalable, and stellar-powered*

</div>