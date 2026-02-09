Agriwise Software Testing Architecture & Execution
1. Executive Summary
This document details the software testing methodologies employed in the Agriwise platform. It strictly focuses on the utilization of Jest for Unit Testing (Frontend and Backend) and Supertest for API Integration Testing. The testing strategy validates critical domain modules such as Farming Tips, Government Schemes, and Market Intelligence.

2. Frontend Unit Testing (Jest)
Context: The Frontend architecture relies on distinct logic to serve different user personas (Admin vs. Farmer). Location: 
frontend/src/components/GovtSchemes/FarmingTips_admin.test.jsx
 Tooling: Jest (Test Runner and Assertion Library).

2.1 Technical Implementation Strategy
The primary objective of frontend testing in Agriwise is Logic Validation. We utilize Jest to validate the behavior of frontend functions and component states.

State Verification: We use Jest to assert that the application state updates correctly in response to inputs. For example, when an Admin inputs a new "Farming Tip", Jest verifies that the internal data structure reflects this change.
Snapshot Testing: Jest captures the structure of the UI components (like the FarmingTips_admin dashboard) and compares them against a baseline to ensure no unexpected changes occur in the UI layout.
Function Mocking: External dependencies and API calls are mocked using standard Jest mocks (jest.fn()). This ensures that the frontend logic is tested in complete isolation without requiring a running server.
2.2 Agriwise Domain Validation
Advisory Logic: Tests verify that the "Farming Tips" module correctly processes raw data. For instance, we assert that a "Water Saving" tip object is correctly formatted before rendering.
Data Handling: We validate that the "Add Tip" forms correctly construct the payload for "Pest Control" advisories, ensuring the Title and Description fields are present and valid strings.
3. Backend Unit Testing (Jest)
Context: The Backend serves as the authoritative source of business logic for the Agriwise ecosystem. Location: 
backend/
tests
/scheme.model.test.js
 Tooling: Jest (Test Runner).

3.1 Technical Implementation Strategy
Backend testing focuses on Pure Logic Testing. We use Jest to execute backend functions in isolation to ensure business rules are enforced.

Object Validation: implementation ensures that data objects instantiated in the backend adhere to strict rules. Jest assertions confirm that a "Scheme" object contains all necessary keys (e.g., Benefit Amount, Eligibility) before any further processing.
Business Rules: We verify algorithms and logic, such as ensuring that the minLand requirement for a scheme is logically less than the maxLand.
Error Handling Paths: Jest is used to invoke backend functions with invalid inputs (e.g., missing Scheme Name) to verify that the functions throw the expected errors or return the correct error objects.
3.2 Agriwise Domain Validation
Government Schemes: We validate the structure of the Scheme logic. The test suite confirms that a "PM Kisan" scheme object is valid only when it includes specific attributes like benefit ("₹6000/year").
Role-Based Validation: We verify the logic that differentiates between Admin and Farmer requests, ensuring that the backend functions correctly identify and classify the data associated with creating new "Farming Tips".
4. API Integration Testing (Supertest)
Context: The API acts as the RESTful contract between the Frontend and the Backend. Location: 
backend/
tests
/api/market.test.js
 Tooling: Supertest (HTTP Assertion Library).

4.1 Technical Implementation Strategy
API testing performs Black Box Testing on the server endpoints. We use Supertest to dispatch HTTP requests and validate the responses.

Endpoint Verification: Supertest sends real HTTP verbs (GET, POST) to the Agriwise server routes. This validates availability and routing logic.
Status Code Assertion: We strictly assert standard HTTP status codes—200 OK for successful data retrieval (e.g., fetching Market Prices) and 400 Bad Request for invalid operations.
Response Structure: The JSON response bodies are inspected to ensure they match the API contract expected by the client.
4.2 Agriwise Domain Validation
Market Intelligence: The testing suite verifies the /api/market/prices endpoint. It asserts that querying for "Wheat" returns a JSON success response containing price data.
Historical Data: We validate the /api/market/history endpoint by simulating requests. The tests confirm that the API correctly handles valid crop queries and rejects malformed requests (e.g., missing crop names) with the appropriate error codes.


5. Conclusion
The Agriwise validation strategy is streamlined and effective:

1.Jest is used for Unit Testing logic in 
FarmingTips_admin.test.jsx
 (Frontend) and 
scheme.model.test.js
 (Backend).
2.Supertest is used for Integration Testing in 
market.test.js
 (API).
