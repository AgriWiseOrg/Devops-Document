# AgriWise - Agricultural Intelligence Platform

![AgriWise](https://img.shields.io/badge/AgriWise-Agricultural%20Intelligence-green)
![Node.js](https://img.shields.io/badge/Node.js-v18+-339933?logo=node.js)
![React](https://img.shields.io/badge/React-19.2-61DAFB?logo=react)
![MongoDB](https://img.shields.io/badge/MongoDB-Database-47A248?logo=mongodb)
![Express](https://img.shields.io/badge/Express-5.2-000000?logo=express)

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Technology Stack](#technology-stack)
- [Getting Started](#getting-started)
- [Environment Configuration](#environment-configuration)
- [Testing](#testing)
- [Docker & Containerization](#docker--containerization)
- [API Documentation](#api-documentation)
- [Deployment](#deployment)
- [CI/CD Pipeline](#cicd-pipeline)
- [Contributing](#contributing)
- [Troubleshooting](#troubleshooting)

---

## 🌾 Overview

**AgriWise** is a comprehensive agricultural intelligence platform designed to empower farmers, buyers, and agricultural stakeholders with data-driven insights. The platform provides:

- **Real-time Market Prices**: Live commodity prices from government APIs
- **AI-Powered Price Prediction**: Machine learning models for future price forecasting
- **Demand Forecasting**: 30-day demand predictions using linear regression
- **Quality-Based Pricing**: Calculate crop prices based on quality parameters
- **Government Schemes**: Access to agricultural schemes and financial assistance
- **Marketplace**: Direct farmer-to-buyer trading platform
- **Multi-language Support**: i18n support for regional languages
- **Weather Integration**: Real-time weather data for farming decisions

---

## 🏗️ Architecture

AgriWise follows a **MERN stack architecture** with a clear separation between frontend and backend:

```
┌─────────────────────────────────────────────────────────┐
│                    Client Layer                         │
│  React 19 + Vite + TailwindCSS + Framer Motion         │
└────────────────────┬────────────────────────────────────┘
                     │ HTTP/REST API
┌────────────────────▼────────────────────────────────────┐
│                  API Gateway Layer                      │
│           Express.js 5.2 + CORS + Middleware           │
└────────────────────┬────────────────────────────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
┌───────▼─────┐ ┌───▼──────┐ ┌──▼──────────┐
│  Business   │ │  ML/AI   │ │  External   │
│   Logic     │ │  Engine  │ │  APIs       │
│  (Routes)   │ │ (Utils)  │ │ (Gov Data)  │
└───────┬─────┘ └───┬──────┘ └──┬──────────┘
        │           │            │
        └───────────┼────────────┘
                    │
┌───────────────────▼─────────────────────────────────────┐
│              Data Persistence Layer                     │
│                  MongoDB Atlas                          │
│   (Users, Products, MarketPrices, Predictions, etc.)   │
└─────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

### Root Directory
```
AgriWise/
├── backend/              # Node.js/Express backend server
├── frontend/             # React frontend application
├── check_db_state.js     # Database state verification utility
└── .gitignore            # Git ignore configuration
```

### Backend Structure (`/backend`)

```
backend/
├── models/               # Mongoose database schemas
│   ├── User.js          # User authentication model
│   ├── FoodProduct.js   # Food product catalog
│   ├── MarketPrice.js   # Historical market price data
│   ├── DemandForecast.js # Demand prediction records
│   ├── PredictionModel.js # ML model parameters
│   ├── Cart.js          # Shopping cart
│   ├── Products.js      # Marketplace products
│   ├── GovtScheme.js    # Government schemes
│   ├── FarmingTip.js    # Agricultural tips
│   ├── FinanceRequest.js # Finance applications
│   └── ...
│
├── routes/              # API route handlers
│   ├── schemes.js       # Government schemes endpoints
│   ├── finance.js       # Finance request handling
│   ├── cartroutes.js    # Shopping cart operations
│   ├── support.js       # Customer support system
│   ├── products.js      # Marketplace product CRUD
│   ├── govtSchemes.js   # Govt scheme applications
│   ├── farmingTips.js   # Farming tips API
│   └── latestUpdates.js # News and updates
│
├── utils/               # Utility functions and helpers
│   ├── qualityCalculator.js  # Quality-based price calculation
│   ├── predictionEngine.js   # ML prediction algorithms
│   └── csvParser.js          # CSV data parsing utility
│
├── scripts/             # Database seeding and maintenance
│   ├── seed_market_data.js      # Seed market prices
│   ├── seed_full_market_data.js # Complete data seeding
│   ├── seed_demand_smart.js     # Demand forecast seeding
│   ├── seed_model_data.js       # ML model training data
│   └── fix_db_indexes.js        # Database index repair
│
├── data/                # Static data files
│   └── commodity_price.csv  # Historical commodity prices (228KB)
│
├── __tests__/           # Backend unit and integration tests
│   ├── marketRoutes.test.js      # Market API tests
│   ├── predictionModel.test.js   # ML model tests
│   ├── qualityCalculator.test.js # Quality calc tests
│   ├── farmingTip.model.test.js  # Model validation tests
│   ├── govtScheme.model.test.js
│   └── ...
│
├── server.js            # Main Express application entry point
├── package.json         # Backend dependencies
├── babel.config.js      # Babel configuration for Jest
└── .env                 # Environment variables (not in git)
```

#### Key Backend Files Explained

| File | Purpose | Key Features |
|------|---------|--------------|
| `server.js` | Main application server | Authentication, market APIs, ML prediction endpoints, middleware setup |
| `models/MarketPrice.js` | Market price schema | Stores historical crop prices with date, crop name, and price |
| `models/PredictionModel.js` | ML model storage | Stores trained linear regression parameters (slope, intercept) |
| `utils/qualityCalculator.js` | Price adjustment logic | Calculates price based on grade, moisture, damage parameters |
| `utils/predictionEngine.js` | ML algorithms | Linear regression implementation for price forecasting |
| `routes/support.js` | Support ticket system | Handles farmer/buyer support requests and ticket management |
| `scripts/seed_market_data.js` | Data initialization | Populates database with historical market data from CSV |

### Frontend Structure (`/frontend`)

```
frontend/
├── src/
│   ├── components/          # React components
│   │   ├── LandingPage.jsx       # Homepage for visitors
│   │   ├── FrontPage.jsx         # Farmer dashboard
│   │   ├── BuyerFrontPage.jsx    # Buyer dashboard
│   │   ├── MarketPrices.jsx      # Market analysis module
│   │   ├── MarketPlace.jsx       # Product marketplace
│   │   ├── QualityPriceCalculator.jsx # Quality pricing tool
│   │   ├── Support.jsx           # Farmer support system
│   │   ├── BuyerSupport.jsx      # Buyer support system
│   │   ├── GovtSchemes.jsx       # Government schemes
│   │   ├── MyCrops.jsx           # Farmer's crop management
│   │   ├── Cart.jsx              # Shopping cart
│   │   ├── Weather.jsx           # Weather information
│   │   ├── LoginPage.jsx         # Authentication
│   │   ├── About.jsx             # About page
│   │   ├── Contact.jsx           # Contact page
│   │   ├── Privacy.jsx           # Privacy policy
│   │   ├── Features.jsx          # Feature showcase
│   │   ├── CartContext.jsx       # Cart state management
│   │   └── GovtSchemes/          # Govt schemes subcomponents
│   │
│   ├── __tests__/           # Component tests (Jest + React Testing Library)
│   │   ├── MarketPrices.test.jsx
│   │   ├── QualityPriceCalculator.test.jsx
│   │   ├── FrontPage.test.jsx
│   │   └── GovtSchemes.test.jsx
│   │
│   ├── assets/              # Static assets (images, icons)
│   ├── App.jsx              # Main application component with routing
│   ├── App.css              # Global styles
│   ├── main.jsx             # React entry point
│   ├── index.css            # TailwindCSS imports
│   └── setupTests.js        # Jest configuration
│
├── public/                  # Public static files
├── index.html               # HTML template
├── package.json             # Frontend dependencies
├── vite.config.js           # Vite build configuration
├── babel.config.cjs         # Babel config for Jest
└── eslint.config.js         # ESLint rules
```

#### Key Frontend Components Explained

| Component | Purpose | Key Features |
|-----------|---------|--------------|
| `MarketPrices.jsx` | Market intelligence dashboard | Price trends, 30-day forecasts, AI predictions, quality calculator |
| `QualityPriceCalculator.jsx` | Price estimation tool | Calculates crop value based on quality parameters |
| `Support.jsx` | Farmer support system | Ticket creation, tracking, dispute resolution |
| `BuyerSupport.jsx` | Buyer support system | Purchase inquiries, negotiation tracking |
| `MarketPlace.jsx` | Product catalog | Browse and purchase crops from farmers |
| `GovtSchemes.jsx` | Scheme discovery | Search and apply for government agricultural schemes |
| `MyCrops.jsx` | Crop management | Farmers can list and manage their crops for sale |
| `CartContext.jsx` | Global state | React Context for cart state management |

---

## 🛠️ Technology Stack

### Backend
- **Runtime**: Node.js 18+
- **Framework**: Express.js 5.2.1
- **Database**: MongoDB (Mongoose 9.1.5)
- **Authentication**: Custom JWT-ready implementation
- **External APIs**: 
  - Government of India Data API (commodity prices)
  - Google Cloud Translate API
- **Testing**: Jest 30.2.0, Supertest 7.2.2
- **Data Processing**: csv-parser, axios

### Frontend
- **Framework**: React 19.2.0
- **Build Tool**: Vite 7.2.4
- **Styling**: TailwindCSS 4.1.18
- **Animations**: Framer Motion 12.29.2
- **Routing**: React Router DOM 7.13.0
- **Internationalization**: i18next, react-i18next
- **Testing**: Jest 30.2.0, React Testing Library 16.3.2, MSW 2.12.9
- **HTTP Client**: Axios 1.13.4

### DevOps & Tools
- **Version Control**: Git
- **Package Manager**: npm
- **Linting**: ESLint 9.39.1
- **Code Formatting**: Babel
- **Environment Management**: dotenv

---

## 🚀 Getting Started

### Prerequisites

Ensure you have the following installed:
- **Node.js**: v18.0.0 or higher ([Download](https://nodejs.org/))
- **npm**: v9.0.0 or higher (comes with Node.js)
- **MongoDB**: Local instance or MongoDB Atlas account ([Setup Guide](https://www.mongodb.com/cloud/atlas))
- **Git**: For version control ([Download](https://git-scm.com/))

### Installation Steps

#### 1. Clone the Repository
```bash
git clone https://github.com/AgriWiseOrg/AgriWise.git
cd AgriWise
```

#### 2. Backend Setup
```bash
cd backend

# Install dependencies
npm install

# Create environment file
cp .env.example .env
# Edit .env with your configuration (see Environment Configuration section)

# Seed the database with initial data
node scripts/seed_full_market_data.js

# Start the development server
node server.js
```

The backend server will start on `http://localhost:5001`

#### 3. Frontend Setup
```bash
cd ../frontend

# Install dependencies
npm install

# Start the development server
npm run dev
```

The frontend will start on `http://localhost:5173`

#### 4. Verify Installation
- Open browser to `http://localhost:5173`
- You should see the AgriWise landing page
- Try logging in or registering a new account
- Navigate to Market Prices to verify API connectivity

---

## ⚙️ Environment Configuration

### Backend Environment Variables (`backend/.env`)

Create a `.env` file in the `backend/` directory with the following variables:

```env
# MongoDB Configuration
MONGO_URI=mongodb://localhost:27017/agriwise
# For MongoDB Atlas:
# MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/agriwise?retryWrites=true&w=majority

# Server Configuration
PORT=5001
NODE_ENV=development

# Google Cloud Translation API (Optional)
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account-key.json

# External API Keys
GOV_DATA_API_KEY=579b464db66ec23bdd000001cdd3946e44ce4aad7209ff7b23ac571b

# JWT Secret (for future authentication enhancement)
JWT_SECRET=your_super_secret_key_here_change_in_production

# CORS Configuration
ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000
```

### Frontend Environment Variables (`frontend/.env`)

Create a `.env` file in the `frontend/` directory:

```env
# Backend API URL
VITE_API_URL=http://localhost:5001/api

# Environment
VITE_ENV=development
```

### Security Best Practices
- **Never commit `.env` files** to version control
- Use different credentials for development, staging, and production
- Rotate API keys regularly
- Use environment-specific MongoDB databases
- Enable MongoDB authentication in production

---

## 🧪 Testing

AgriWise has comprehensive test coverage for both backend and frontend.

### Backend Testing

#### Run All Backend Tests
```bash
cd backend
npm test
```

#### Run Specific Test File
```bash
npm test -- marketRoutes.test.js
```

#### Run Tests in Watch Mode
```bash
npm test -- --watch
```

#### Backend Test Coverage
```bash
npm test -- --coverage
```

#### Available Backend Tests

| Test File | Coverage | Purpose |
|-----------|----------|---------|
| `marketRoutes.test.js` | API endpoints | Tests market price, forecast, and prediction APIs |
| `predictionModel.test.js` | ML models | Validates prediction algorithm accuracy |
| `qualityCalculator.test.js` | Utility functions | Tests quality-based price calculations |
| `farmingTip.model.test.js` | Database models | Validates Mongoose schema constraints |
| `govtScheme.model.test.js` | Database models | Tests scheme model validation |
| `sample.test.js` | Basic setup | Verifies Jest configuration |

### Frontend Testing

#### Run All Frontend Tests
```bash
cd frontend
npm test
```

#### Run Tests in Watch Mode
```bash
npm test -- --watch
```

#### Frontend Test Coverage
```bash
npm test -- --coverage
```

#### Available Frontend Tests

| Test File | Coverage | Purpose |
|-----------|----------|---------|
| `MarketPrices.test.jsx` | Component rendering | Tests market price display and interactions |
| `QualityPriceCalculator.test.jsx` | Form validation | Tests calculator input and output |
| `FrontPage.test.jsx` | Dashboard | Validates farmer dashboard rendering |
| `GovtSchemes.test.jsx` | Scheme listing | Tests scheme search and display |

### Test Configuration

#### Backend Jest Config (`backend/package.json`)
```json
{
  "scripts": {
    "test": "jest"
  }
}
```

#### Frontend Jest Config (`frontend/package.json`)
```json
{
  "jest": {
    "testEnvironment": "jsdom",
    "setupFilesAfterEnv": ["<rootDir>/src/setupTests.js"]
  }
}
```

### Writing New Tests

#### Backend Test Example
```javascript
// backend/__tests__/newFeature.test.js
const request = require('supertest');
const app = require('../server');

describe('New Feature API', () => {
  it('should return expected data', async () => {
    const response = await request(app)
      .get('/api/new-feature')
      .expect(200);
    
    expect(response.body).toHaveProperty('data');
  });
});
```

#### Frontend Test Example
```javascript
// frontend/src/components/__tests__/NewComponent.test.jsx
import { render, screen } from '@testing-library/react';
import NewComponent from '../NewComponent';

describe('NewComponent', () => {
  it('renders correctly', () => {
    render(<NewComponent />);
    expect(screen.getByText('Expected Text')).toBeInTheDocument();
  });
});
```

---

## 🐳 Docker & Containerization

### Docker Strategy

AgriWise uses Docker for consistent development and production environments.

### Dockerfile - Backend

Create `backend/Dockerfile`:

```dockerfile
# Backend Dockerfile
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application files
COPY . .

# Expose port
EXPOSE 5001

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s \
  CMD node -e "require('http').get('http://localhost:5001/api/health', (r) => {process.exit(r.statusCode === 200 ? 0 : 1)})"

# Start server
CMD ["node", "server.js"]
```

### Dockerfile - Frontend

Create `frontend/Dockerfile`:

```dockerfile
# Multi-stage build for optimized production image

# Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Stage 2: Production
FROM nginx:alpine

# Copy built files from builder
COPY --from=builder /app/dist /usr/share/nginx/html

# Copy custom nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### Nginx Configuration

Create `frontend/nginx.conf`:

```nginx
server {
    listen 80;
    server_name localhost;
    root /usr/share/nginx/html;
    index index.html;

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # SPA routing - redirect all to index.html
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

### Docker Compose

Create `docker-compose.yml` in the root directory:

```yaml
version: '3.8'

services:
  # MongoDB Database
  mongodb:
    image: mongo:7.0
    container_name: agriwise-mongodb
    restart: unless-stopped
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: secure_password_here
      MONGO_INITDB_DATABASE: agriwise
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
      - ./backend/scripts:/docker-entrypoint-initdb.d
    networks:
      - agriwise-network

  # Backend API
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: agriwise-backend
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - MONGO_URI=mongodb://admin:secure_password_here@mongodb:27017/agriwise?authSource=admin
      - PORT=5001
    ports:
      - "5001:5001"
    depends_on:
      - mongodb
    volumes:
      - ./backend:/app
      - /app/node_modules
    networks:
      - agriwise-network
    command: node server.js

  # Frontend Application
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: agriwise-frontend
    restart: unless-stopped
    ports:
      - "80:80"
    depends_on:
      - backend
    networks:
      - agriwise-network

volumes:
  mongodb_data:
    driver: local

networks:
  agriwise-network:
    driver: bridge
```

### Docker Commands

#### Build and Start All Services
```bash
# Build images
docker-compose build

# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Stop and remove volumes (WARNING: deletes database)
docker-compose down -v
```

#### Individual Service Management
```bash
# Rebuild specific service
docker-compose build backend

# Restart specific service
docker-compose restart frontend

# View service logs
docker-compose logs -f backend

# Execute commands in container
docker-compose exec backend npm test
docker-compose exec mongodb mongosh
```

#### Database Seeding in Docker
```bash
# Seed database after containers are running
docker-compose exec backend node scripts/seed_full_market_data.js
```

### Production Docker Optimization

#### .dockerignore Files

Create `backend/.dockerignore`:
```
node_modules
npm-debug.log
.env
.git
.gitignore
__tests__
*.test.js
test_*.js
*.log
.DS_Store
```

Create `frontend/.dockerignore`:
```
node_modules
npm-debug.log
.git
.gitignore
dist
build
*.test.jsx
*.test.js
coverage
.DS_Store
```

---

## 📚 API Documentation

### Base URL
- **Development**: `http://localhost:5001/api`
- **Production**: `https://your-domain.com/api`

### Authentication Endpoints

#### Register User
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "farmer@example.com",
  "password": "securePassword123",
  "role": "farmer"  // or "buyer"
}

Response: 201 Created
{
  "message": "Registration successful",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "farmer@example.com",
    "role": "farmer"
  }
}
```

#### Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "farmer@example.com",
  "password": "securePassword123"
}

Response: 200 OK
{
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "farmer@example.com",
    "role": "farmer"
  }
}
```

### Market Data Endpoints

#### Get Current Market Prices
```http
GET /api/market/prices

Response: 200 OK
[
  {
    "id": 1,
    "crop": "Wheat (Sarbati)",
    "mandi": "Indore Mandi, Madhya Pradesh",
    "price": "₹2,350",
    "trend": "up"
  },
  ...
]
```

#### Get Price History
```http
GET /api/market/history?crop=Wheat

Response: 200 OK
{
  "crop": "Wheat",
  "period": "6m",
  "data": [
    { "month": "Jan", "price": 2200 },
    { "month": "Feb", "price": 2250 },
    ...
  ]
}
```

#### Get Available Crops
```http
GET /api/market/crops

Response: 200 OK
["Wheat", "Rice", "Soybean", "Cotton", ...]
```

#### Get 30-Day Forecast
```http
GET /api/market/forecast-30-days?crop=Wheat

Response: 200 OK
{
  "crop": "Wheat",
  "data": [
    { "date": "2026-02-10", "price": 2350.45 },
    { "date": "2026-02-11", "price": 2355.20 },
    ...
  ]
}
```

### AI Prediction Endpoints

#### Predict Future Price
```http
POST /api/predict-price
Content-Type: application/json

{
  "product": "Wheat",
  "region": "Madhya Pradesh",
  "month": "March"
}

Response: 200 OK
{
  "product": "Wheat",
  "predictedPrice": 2450.75,
  "currency": "INR",
  "confidence": "85%",
  "factors": {
    "trend": "Increasing",
    "sampleSize": 120,
    "lastTrained": "2026-02-09T18:30:00.000Z"
  }
}
```

#### Calculate Quality-Based Price
```http
POST /api/market/quality-price
Content-Type: application/json

{
  "crop": "Wheat",
  "grade": "A",
  "params": {
    "moisture": 12.5,
    "damage": 2.0
  }
}

Response: 200 OK
{
  "basePrice": 2350,
  "adjustedPrice": 2420.50,
  "adjustments": {
    "gradeBonus": 5,
    "moisturePenalty": -2,
    "damagePenalty": -1
  }
}
```

### Government Schemes

#### Get All Schemes
```http
GET /api/govt-schemes

Response: 200 OK
[
  {
    "_id": "...",
    "name": "PM-KISAN",
    "description": "Direct income support to farmers",
    "eligibility": "Small and marginal farmers",
    "benefits": "₹6000 per year"
  },
  ...
]
```

### Marketplace Endpoints

#### Get Products
```http
GET /api/products

Response: 200 OK
[
  {
    "_id": "...",
    "name": "Organic Wheat",
    "price": 2500,
    "quantity": 100,
    "unit": "quintal",
    "seller": "farmer@example.com"
  },
  ...
]
```

#### Add to Cart
```http
POST /api/cart/add
Content-Type: application/json

{
  "userId": "507f1f77bcf86cd799439011",
  "productId": "507f191e810c19729de860ea",
  "quantity": 5
}
```

---

## 🚢 Deployment

### Deployment Options

#### Option 1: Traditional VPS Deployment (DigitalOcean, AWS EC2, Linode)

**Prerequisites:**
- Ubuntu 20.04+ server
- Domain name (optional)
- SSL certificate (Let's Encrypt recommended)

**Steps:**

1. **Server Setup**
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Install MongoDB
wget -qO - https://www.mongodb.org/static/pgp/server-7.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt update
sudo apt install -y mongodb-org
sudo systemctl start mongod
sudo systemctl enable mongod

# Install Nginx
sudo apt install -y nginx

# Install PM2 (Process Manager)
sudo npm install -g pm2
```

2. **Clone and Setup Application**
```bash
# Clone repository
cd /var/www
sudo git clone https://github.com/AgriWiseOrg/AgriWise.git
cd AgriWise

# Backend setup
cd backend
npm install --production
cp .env.example .env
# Edit .env with production values
sudo nano .env

# Seed database
node scripts/seed_full_market_data.js

# Start with PM2
pm2 start server.js --name agriwise-backend
pm2 save
pm2 startup

# Frontend setup
cd ../frontend
npm install
npm run build
```

3. **Nginx Configuration**
```bash
sudo nano /etc/nginx/sites-available/agriwise
```

Add configuration:
```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    # Frontend
    location / {
        root /var/www/AgriWise/frontend/dist;
        try_files $uri $uri/ /index.html;
    }

    # Backend API
    location /api {
        proxy_pass http://localhost:5001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable site:
```bash
sudo ln -s /etc/nginx/sites-available/agriwise /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

4. **SSL Certificate (Let's Encrypt)**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

#### Option 2: Docker Deployment

```bash
# On server
git clone https://github.com/AgriWiseOrg/AgriWise.git
cd AgriWise

# Create production .env files
nano backend/.env
nano frontend/.env

# Build and start
docker-compose -f docker-compose.prod.yml up -d

# Seed database
docker-compose exec backend node scripts/seed_full_market_data.js
```

#### Option 3: Cloud Platform Deployment

**Heroku:**
```bash
# Backend
cd backend
heroku create agriwise-api
heroku addons:create mongolab:sandbox
git push heroku main

# Frontend
cd ../frontend
heroku create agriwise-app
heroku buildpacks:set heroku/nodejs
git push heroku main
```

**Vercel (Frontend):**
```bash
cd frontend
npm install -g vercel
vercel --prod
```

**Railway/Render (Backend):**
- Connect GitHub repository
- Set environment variables
- Deploy automatically on push

### Environment-Specific Configurations

#### Production Checklist
- [ ] Set `NODE_ENV=production`
- [ ] Use strong MongoDB credentials
- [ ] Enable MongoDB authentication
- [ ] Use HTTPS/SSL certificates
- [ ] Set secure CORS origins
- [ ] Enable rate limiting
- [ ] Set up monitoring (PM2, New Relic, Datadog)
- [ ] Configure log rotation
- [ ] Set up automated backups
- [ ] Enable firewall (UFW)
- [ ] Implement JWT authentication
- [ ] Hash passwords with bcrypt
- [ ] Set up CDN for static assets

---

## 🔄 CI/CD Pipeline

### GitHub Actions Workflow

Create `.github/workflows/ci-cd.yml`:

```yaml
name: AgriWise CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  # Backend Tests
  backend-test:
    runs-on: ubuntu-latest
    
    services:
      mongodb:
        image: mongo:7.0
        ports:
          - 27017:27017
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
          cache-dependency-path: backend/package-lock.json
      
      - name: Install Backend Dependencies
        working-directory: ./backend
        run: npm ci
      
      - name: Run Backend Tests
        working-directory: ./backend
        run: npm test
        env:
          MONGO_URI: mongodb://localhost:27017/agriwise_test
      
      - name: Upload Backend Coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./backend/coverage/lcov.info
          flags: backend

  # Frontend Tests
  frontend-test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
          cache-dependency-path: frontend/package-lock.json
      
      - name: Install Frontend Dependencies
        working-directory: ./frontend
        run: npm ci
      
      - name: Run Frontend Tests
        working-directory: ./frontend
        run: npm test
      
      - name: Build Frontend
        working-directory: ./frontend
        run: npm run build
      
      - name: Upload Frontend Coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./frontend/coverage/lcov.info
          flags: frontend

  # Docker Build
  docker-build:
    runs-on: ubuntu-latest
    needs: [backend-test, frontend-test]
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Login to DockerHub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      
      - name: Build and Push Backend
        uses: docker/build-push-action@v4
        with:
          context: ./backend
          push: true
          tags: agriwise/backend:latest,agriwise/backend:${{ github.sha }}
      
      - name: Build and Push Frontend
        uses: docker/build-push-action@v4
        with:
          context: ./frontend
          push: true
          tags: agriwise/frontend:latest,agriwise/frontend:${{ github.sha }}

  # Deploy to Production
  deploy:
    runs-on: ubuntu-latest
    needs: docker-build
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Deploy to Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /var/www/AgriWise
            git pull origin main
            docker-compose pull
            docker-compose up -d
            docker-compose exec backend node scripts/seed_full_market_data.js
```

### Required GitHub Secrets
- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`
- `SERVER_HOST`
- `SERVER_USER`
- `SSH_PRIVATE_KEY`

---

## 🤝 Contributing

We welcome contributions to AgriWise! Please follow these guidelines:

### Development Workflow

1. **Fork the repository**
2. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make your changes**
   - Follow existing code style
   - Write tests for new features
   - Update documentation

4. **Run tests**
   ```bash
   # Backend
   cd backend && npm test
   
   # Frontend
   cd frontend && npm test
   ```

5. **Commit with conventional commits**
   ```bash
   git commit -m "feat: add new market analysis feature"
   git commit -m "fix: resolve price calculation bug"
   git commit -m "docs: update API documentation"
   ```

6. **Push and create Pull Request**
   ```bash
   git push origin feature/your-feature-name
   ```

### Code Style Guidelines

- **JavaScript**: Use ES6+ features
- **React**: Functional components with hooks
- **Naming**: camelCase for variables, PascalCase for components
- **Comments**: Document complex logic
- **Formatting**: 2-space indentation

### Pull Request Checklist
- [ ] Tests pass locally
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] No console.log statements
- [ ] Meaningful commit messages
- [ ] PR description explains changes

---

## 🔧 Troubleshooting

### Common Issues

#### 1. MongoDB Connection Failed
**Error**: `MongooseServerSelectionError: connect ECONNREFUSED`

**Solutions:**
```bash
# Check if MongoDB is running
sudo systemctl status mongod

# Start MongoDB
sudo systemctl start mongod

# Check connection string in .env
# Ensure MONGO_URI is correct
```

#### 2. Port Already in Use
**Error**: `EADDRINUSE: address already in use :::5001`

**Solutions:**
```bash
# Find process using port
lsof -i :5001  # Mac/Linux
netstat -ano | findstr :5001  # Windows

# Kill process
kill -9 <PID>  # Mac/Linux
taskkill /PID <PID> /F  # Windows
```

#### 3. Frontend Can't Connect to Backend
**Error**: `Network Error` or `ERR_CONNECTION_REFUSED`

**Solutions:**
- Verify backend is running on port 5001
- Check `VITE_API_URL` in frontend/.env
- Ensure CORS is enabled in backend
- Check firewall settings

#### 4. Tests Failing
**Error**: Various test failures

**Solutions:**
```bash
# Clear Jest cache
npm test -- --clearCache

# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install

# Check Node version
node --version  # Should be 18+
```

#### 5. Docker Container Crashes
**Error**: Container exits immediately

**Solutions:**
```bash
# View container logs
docker-compose logs backend

# Check environment variables
docker-compose config

# Rebuild containers
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

#### 6. Database Seeding Issues
**Error**: `No data in database` or seeding script fails

**Solutions:**
```bash
# Verify CSV file exists
ls -la backend/data/commodity_price.csv

# Run seeding script with logs
node backend/scripts/seed_full_market_data.js

# Check MongoDB data
mongosh
use agriwise
db.marketprices.countDocuments()
```

### Getting Help

- **Documentation**: Check this README first
- **Issues**: Search [GitHub Issues](https://github.com/AgriWiseOrg/AgriWise/issues)
- **Discussions**: Join [GitHub Discussions](https://github.com/AgriWiseOrg/AgriWise/discussions)
- **Email**: support@agriwise.com

---

## 📄 License

This project is licensed under the ISC License.

---

## 👥 Team

**AgriWise Development Team**
- Project Lead: Anantha Sagar
- Backend Developer: Anantha Sagar
- Frontend Developer: K CHakri
- FrontEnd/Backend:Madhav
- DevOps Engineer: C.Pranav Sharma
- Testing Engineer: Nikhil Aditya

---

## 🙏 Acknowledgments

- Government of India Open Data Platform for commodity price data
- MongoDB Atlas for database hosting
- React and Express.js communities
- All contributors and testers

---

## 📞 Contact

- **Website**: https://agriwise.com
- **Email**: support@agriwise.com
- **GitHub**: https://github.com/AgriWiseOrg/AgriWise
- **Twitter**: @AgriWiseApp

---

**Made with ❤️ for Indian Farmers**
