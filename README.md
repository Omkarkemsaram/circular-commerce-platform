# 🔄 Circular Commerce Platform (ReCircle)

[![React](https://img.shields.io/badge/Frontend-React_18_%7C_Vite-61DAFB?logo=react&logoColor=black)](https://reactjs.org/)
[![Node.js](https://img.shields.io/badge/Backend-Node.js_%7C_Express-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![AWS](https://img.shields.io/badge/Cloud-AWS_Elastic_Beanstalk_%7C_DynamoDB_%7C_ElastiCache-232F3E?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

An enterprise-ready, full-stack circular economy marketplace platform designed for sustainable commerce. **ReCircle** empowers users to submit, buy, sell, exchange, and schedule pickups for pre-owned goods with automated AI inspection, intelligent route optimization, dynamic pricing, and credit-based circular incentives.

---

## 🌟 Core Features & Capabilities

- 🛒 **Circular Marketplace:** Categorized listings across Electronics, Fashion, Home & Garden, Tools, Appliances, and more.
- 🤖 **AI-Powered Quality & Return Inspection:** Integrates with AWS Bedrock (Claude) to evaluate item condition, assess return risk, and provide automated inspection reports.
- 📍 **Hyper-Local Discovery & Dynamic Routing:** Geolocation-aware search paired with automated SQS-driven route optimization for efficient multi-stop batch pickups.
- 💰 **Dynamic Valuation & Pricing Engine:** Background workers compute fair circular market pricing based on item depreciation, brand tier, and condition.
- ⚡ **Real-Time Updates & Notifications:** Dual-layer real-time engine featuring Server-Sent Events (SSE) and WebSocket integration backed by Amazon SNS.
- 💎 **Incentive Credit System:** Reward users for participation in the circular economy with spendable credits and reputation tiering.
- 🛡️ **Admin & Moderation Dashboard:** Real-time visibility into transactions, pickup dispatches, flagged items, and operational health.
- 🚀 **Cloud-Native CI/CD:** Fully automated pipeline via GitHub Actions using AWS OIDC, deploying frontend builds to S3 + CloudFront and backend services to Elastic Beanstalk.

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           AWS Cloud (ap-south-2)                            │
│                                                                             │
│  ┌──────────────┐     ┌──────────────────┐     ┌────────────────────┐       │
│  │   Frontend   │     │   CloudFront     │     │  Amazon ElastiCache│       │
│  │  (S3 origin) │────▶│  Distribution    │     │  (Redis 7.x)       │       │
│  └──────────────┘     └────────┬─────────┘     └────────────────────┘       │
│                                │                                            │
│              ┌─────────────────┼─────────────────┐                          │
│              │ /static (S3)    │ /api/* (ALB)     │ WebSocket (APIGW)       │
│              ▼                 ▼                  ▼                          │
│  ┌──────────────┐     ┌──────────────────┐     ┌────────────────────┐       │
│  │   S3 Bucket  │     │ Elastic Beanstalk│     │  API Gateway       │       │
│  │  (Frontend)  │     │  ALB → Express   │     │  (WebSocket / SSE) │       │
│  └──────────────┘     └────────┬─────────┘     └────────────────────┘       │
│                                │                                            │
│                                ▼                                            │
│                       ┌──────────────────┐     ┌────────────────────┐       │
│                       │    DynamoDB      │     │    AWS Bedrock     │       │
│                       │  (Single Table)  │     │  (Claude Haiku)    │       │
│                       └──────────────────┘     └────────────────────┘       │
│                                                                             │
│  ┌──────────────┐     ┌──────────────────┐     ┌────────────────────┐       │
│  │  SQS Routing │     │   SQS Pricing    │     │     Amazon SNS     │       │
│  │    Queue     │     │      Queue       │     │  (Notifications)   │       │
│  └──────┬───────┘     └────────┬─────────┘     └────────────────────┘       │
│         │                      │                                            │
│         ▼                      ▼                                            │
│  ┌──────────────────────────────────────┐                                   │
│  │          Background Workers          │                                   │
│  │   (Pricing & Batch Pickup Routing)   │                                   │
│  └──────────────────────────────────────┘                                   │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Technology Stack

| Layer | Technologies |
|---|---|
| **Frontend** | React 18, Vite, React Router 6, CSS3 / Modern Glassmorphism |
| **Backend API** | Node.js, Express.js, AWS SDK v3, Joi Validator |
| **Database & Cache** | Amazon DynamoDB (Single Table Design), Redis 7.x (ElastiCache) |
| **Asynchronous Queues** | AWS SQS (Pricing queue, Routing queue), Background Workers |
| **Real-Time Delivery** | WebSockets, Server-Sent Events (SSE), Amazon SNS |
| **AI / Machine Learning** | AWS Bedrock (Claude 3.5 Haiku) |
| **Hosting & CI/CD** | AWS Elastic Beanstalk, Amazon S3, CloudFront, GitHub Actions |

---

## 📁 Repository Structure

```
circular-commerce-platform/
├── backend/
│   ├── .ebextensions/        # Elastic Beanstalk configuration files
│   ├── .platform/            # Nginx reverse proxy optimizations
│   ├── scripts/              # Token generation and S3 sync utilities
│   ├── src/
│   │   ├── db/               # DynamoDB client, Redis cache layer & data store
│   │   ├── middleware/       # JWT Authentication, rate limiting & error handlers
│   │   ├── routes/           # REST endpoints (products, credits, pickups, admin)
│   │   ├── services/         # Geocoding, routing, batch collection, pricing
│   │   ├── validators/       # Joi request body schemas
│   │   ├── workers/          # SQS background workers for pricing & batch routing
│   │   ├── server.js         # Express app entrypoint
│   │   └── worker.js         # Dedicated background worker runner
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/       # AI reports, inspection toasts, offline banners
│   │   ├── hooks/            # Graceful fetch & real-time notification hooks
│   │   ├── pages/            # Marketplace, Dashboard, PickupDetail, Checkout
│   │   ├── services/         # Geolocation & API client integration
│   │   ├── App.jsx           # Main router & app layout
│   │   └── styles.css        # Responsive styling system
│   ├── vite.config.js
│   └── package.json
├── infrastructure/           # CloudFormation / CloudFront YAML definitions
├── .github/workflows/        # Automated OIDC-based CI/CD deployment pipeline
└── CI_CD_SETUP.md            # Detailed deployment & IAM configuration guide
```

---

## 🚦 Quickstart Guide

### Prerequisites
- **Node.js** (v18.x or v20.x LTS)
- **npm** (v9+)
- *(Optional)* AWS CLI configured for cloud deployment

---

### 1. Backend Setup

```bash
# Navigate to the backend directory
cd backend

# Install dependencies
npm install

# Start development server
npm run dev
# Server will run on http://localhost:5000 (or PORT specified in .env)
```

To run the background workers independently:
```bash
npm run worker
```

---

### 2. Frontend Setup

```bash
# Navigate to the frontend directory
cd frontend

# Install dependencies
npm install

# Start Vite development server
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

---

## 🔐 Environment Variables

### Backend (`backend/.env`)
```env
PORT=5000
NODE_ENV=development
AWS_REGION=ap-south-2
DYNAMODB_TABLE_NAME=CircularCommerceTable
REDIS_HOST=localhost
REDIS_PORT=6379
SQS_PRICING_QUEUE_URL=https://sqs.ap-south-2.amazonaws.com/...
SQS_ROUTING_QUEUE_URL=https://sqs.ap-south-2.amazonaws.com/...
JWT_SECRET=your_jwt_secret_key
```

### Frontend (`frontend/.env.development`)
```env
VITE_API_BASE=http://localhost:5000/api
```

---

## 🔄 Deployment Pipeline

This repository includes a production-grade CI/CD pipeline configured with **GitHub Actions** and **AWS OIDC**:
- Automatically checks syntax and runs test suites on pull requests.
- Builds and invalidates frontend static assets on **AWS S3 + CloudFront**.
- Deploys backend API to **AWS Elastic Beanstalk**.

For complete IAM role creation and step-by-step instructions, refer to [CI_CD_SETUP.md](./CI_CD_SETUP.md).

---

## 📜 License
This project is open source and available under the [MIT License](LICENSE).
