# AgriWise Testing Architecture and Implementation Details

This document provides a comprehensive overview of the testing infrastructure, methodologies, and specific test suites implemented across the AgriWise application (both frontend and backend).

## 1. Testing Frameworks and Tooling

### Frontend Testing Stack
- **Test Runner:** [Vitest](https://vitest.dev/)
  - Chosen for its speed and native integration with Vite.
- **Component Testing:** [React Testing Library (RTL)](https://testing-library.com/docs/react-testing-library/intro/)
  - Used for rendering React components and asserting on the DOM from a user's perspective.
- **Environment:** `jsdom`
  - Simulates a browser environment for Vitest to run React components without needing a real browser.
- **End-to-End (E2E) Testing:** [Playwright](https://playwright.dev/)
  - Configured via `playwright.config.ts` for full cross-browser testing (Chromium, Firefox, WebKit).

### Backend Testing Stack
- **Test Runner:** [Jest](https://jestjs.io/)
  - Industry standard for Node.js backend testing, providing isolated testing environments.
- **API Testing:** [Supertest](https://github.com/ladjs/supertest)
  - Used alongside Jest to mock HTTP requests directly against the Express app without needing to bind it to a network port.
- **Mocking:** Jest's built-in mocking capabilities (timers, functions, module mocks).

---

## 2. Frontend Test Implementation

The frontend tests are currently co-located with their respective components inside the `src/components/` directory format.

### Types of Frontend Tests
1. **Unit Tests (`*.test.jsx`)**: Ensure specific components render correctly and handle individual state changes or user interactions.
   - Example: `MarketPrices.test.jsx` verifies the component renders correctly and navigation tabs switch to the Expected Calculator views.
2. **Integration Tests (`*.integration.test.jsx`)**: Validate the interactions between components, contexts (like Internationalization/Language Context), and external service calls.
   - Example: `Weather.integration.test.jsx` mocks `fetch` APIs and `geolocation` to test how the weather component handles successful API responses (displaying data) and network failures (displaying error UI boundaries).
3. **Snapshot Tests (`*.snapshot.test.jsx`)**: Prevent UI regression by ensuring the rendered DOM output remains consistent unless purposefully changed.
   - Example: `About.snapshot.test.jsx` keeps track of the static markup of the About page.

### Key Monitored Components
- **Core Market/Financials:** `MarketPrices.test.jsx`, `MarketPlace.test.jsx`, `QualityPriceCalculator.test.jsx`, `Cart.test.jsx`
- **Dashboard & Advisory:** `Weather.test.jsx`, `Weather.integration.test.jsx`, `FrontPage.test.jsx`
- **Government Schemes (Role-based):** `GovtSchemes.test.jsx`, `Finance_farmer.test.jsx`, `Finance_admin.test.jsx`, `FarmingTips_admin.test.jsx`
- **Utility modules:** Validation of LanguageContext and react-i18next translation integrations.

---

## 3. Backend Test Implementation

The backend test suite is centralized within the `backend/__tests__/` directory. It focuses predominantly on validating proper database schema definitions and robust API routing.

### Types of Backend Tests
1. **Model Tests (`*.model.test.js`)**: Ensure Mongoose schemas enforce the correct data types, required fields, and validation logic before saving to MongoDB.
   - Example Models Tested: `contract.test.js`, `farmingTip.model.test.js`, `financeRequest.model.test.js`, `govtScheme.model.test.js`, `product_model.test.js`, `scheme.model.test.js`.
2. **Route/API Integration Tests (`*.test.js` or `*.route.test.js`)**: Map real HTTP requests using Supertest against the Express router to check status codes, JSON formations, and data integrations.
   - Example Route Tests: 
     - `marketRoutes.test.js`: Validates `GET /api/market/prices`, `POST /api/market/quality-price`, and `GET /api/market/forecast-30-days`.
     - `product_route.test.js`: Validates endpoints related to Marketplace product creation and fetching.
3. **Utility Tests**: Ensuring core backend functions (like Quality logic or Price Prediction Model formats) evaluate predictably.
   - Example: `qualityCalculator.test.js`, `predictionModel.test.js`.

### Backend Testing Patterns
- **Database Initialization:** Test suites (like `setup.js`) establish early connection hooks to the test DB configurations using `beforeAll` and tear down post-test runs using `afterAll`.
- **Response Validation:** Assertions strictly expect valid HTTP status blocks (`200 OK`, `400 Bad Request`, `500 Server Error`).
- **Isolation:** Each endpoint test runs sequentially and validates API edge cases (e.g., verifying `Array.isArray(res.body)` when fetching multiple crops).

---

## 4. End-to-End (E2E) Overview

Playwright is configured (`playwright.config.ts`) to execute comprehensive End-to-End flows acting as a real user navigating the web browser.

**Execution Parameters:**
- **Local Dev Server Hook:** Playwright automatically starts the frontend Vite server (`npm run dev`) before initiating the tests.
- **Parallelization:** Configured for native multi-core execution (`fullyParallel: true`).
- **Browsers:** Covers Chromium (Chrome/Edge), WebKit (Safari), and Firefox.
- **Tracing:** Full trace-viewers (`trace: 'on-first-retry'`) configured for debugging CI/CD pipeline failures. 

---

## 5. Running the Tests

Here are the commands to execute the tests per environment:

**Frontend (React/Vite)**
```bash
# Navigate to the frontend directory
cd frontend

# Run all tests in watch mode
npm run test

# Alternatively, run tests using Vitest CLI
npx vitest
```

**Backend (Node/Express)**
```bash
# Navigate to the backend directory
cd backend

# Run suite
npm run test

# Alternatively, run tests using Jest directly
npx jest
```

**End-to-End (Playwright)**
```bash
# Navigate to the frontend directory
cd frontend

# Run E2E tests across all configured web engines
npx playwright test

# Display the Playwright HTML test report
npx playwright show-report
```

---

## 6. CI/CD Implementation

Continuous Integration (CI) and Continuous Deployment (CD) pipelines are established using **GitHub Actions**. The pipelines ensure that code pushed to the `main` branch or submitted via Pull Requests is verified and deployed automatically.

### Frontend CI Pipeline
**Workflow File:** `frontend/.github/workflows/ci.yml`
- **Triggers:** Pushes and Pull Requests to the `main` branch.
- **Environment:** Ubuntu-latest, Node.js v20.
- **Stages:**
  1. **Checkout:** Checks out the source code.
  2. **Setup Node:** Configures Node.js and caches `npm` dependencies.
  3. **Install Dependencies:** Runs `npm ci` for a clean install.
  4. **Linter Checks:** Ensures code adheres to defined styles.
  5. **Tests Validation:** Executes the frontend testing suites.

### Backend CI Pipeline
**Workflow File:** `backend/.github/workflows/ci.yml`
- **Triggers:** Pushes and Pull Requests to the `main` branch.
- **Environment:** Ubuntu-latest.
- **Stages:**
  1. **Checkout:** Checks out the backend codebase.
  2. **Tests Validation:** Executes backend unit and integration tests.

### Continuous Deployment (CD)

The application utilizes auto-deployment mechanisms connected directly to the GitHub repository rather than through explicit GitHub Action deployment YAMLs.

**Frontend Deployment (Vercel):**
- **Platform:** [Vercel](https://vercel.com)
- **Trigger:** Automated deployment on every push to the `main` branch.
- **Process:** Vercel automatically detects the Vite React application, executes `npm run build`, and serves the static output across its Edge Network. It utilizes the `vercel.json` configuration for routing rules.

**Backend Deployment (Render):**
- **Platform:** [Render](https://render.com)
- **Trigger:** Automated deployment on every push to the `main` branch.
- **Process:** Render detects the Node.js application, spins up the environment, executes `npm install`, and starts the production server using the defined startup commands (`node server.js`). A Keep-Alive mechanism prevents the free-tier service from spinning down due to inactivity.

---

## 7. Test Execution Results

Here is the snapshot of the latest test suite execution outputs demonstrating full stability across the project.

**Frontend Execution (Vitest):**
```bash
  ✓ src/components/MarketPlace.test.jsx (8 tests) 
  ✓ src/components/FrontPage.test.jsx (4 tests) 
  ✓ src/components/Weather.integration.test.jsx (2 tests)
  ...

 Test Files  14 passed (14)
      Tests  46 passed (46)
   Start at  09:46:27
   Duration  33.88s (transform 42.44s, setup 45.81s, import 158.62s, tests 20.87s, environment 175.86s)

Exit code: 0
```

**Backend Execution (Jest):**
```bash
  ✓ should create a valid prediction model 
  ✓ should fail validation without required fields 
  ✓ should fetch all reports successfully
  ...

Test Suites: 8 passed, 8 total
Tests:       33 passed, 33 total
Snapshots:   0 total
Time:        4.197 s
Ran all test suites.
```

