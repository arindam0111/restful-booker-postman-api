# Restful Booker Postman API Automation

Postman-based API automation suite for the **Restful Booker** demo API, covering authentication, CRUD operations, positive testing, negative testing, boundary testing, dynamic data correlation, and regression execution.

## Project Overview

This project demonstrates API test automation using **Postman and JavaScript** with a focus on reusable test data, dynamic response correlation, functional validation, negative scenarios, boundary conditions, and complete CRUD lifecycle verification.

The suite validates the booking resource through:

```text
Create → Read → Update → Delete → Verify
```

The project is designed as a practical QA/SDET portfolio project demonstrating API automation and test-design skills.

---

## Technology Stack

| Technology          | Purpose                                     |
| ------------------- | ------------------------------------------- |
| Postman             | API testing and automation                  |
| JavaScript          | Test scripts and response validation        |
| REST API            | API architecture under test                 |
| Postman Environment | Configuration and runtime data              |
| Collection Runner   | Regression and multiple-iteration execution |
| Git                 | Version control                             |
| GitHub              | Source control and portfolio                |

---

## API Under Test

**Restful Booker**

Base URL:

```text
https://restful-booker.herokuapp.com
```

The API provides booking-related endpoints suitable for practicing authentication, CRUD operations, API validation, and automated testing.

---

## Test Coverage

The collection contains **13 documented test scenarios**.

| Category         | Coverage |
| ---------------- | -------: |
| Authentication   |        1 |
| Positive CRUD    |        7 |
| Negative Testing |        3 |
| Boundary Testing |        2 |
| **Total**        |   **13** |

### Authentication

* Create authentication token
* Validate token generation
* Store token dynamically for authenticated requests

### Positive Testing

* Get all booking IDs
* Create booking
* Verify created booking
* Update booking
* Verify updated booking
* Delete booking
* Verify deleted booking

### Negative Testing

* Invalid booking ID
* Invalid authentication token
* Invalid booking data

### Boundary Testing

* Zero booking price
* Same-day check-in and check-out

---

## End-to-End CRUD Flow

The primary regression flow validates the complete booking lifecycle:

```text
Create Token
      ↓
Create Booking
      ↓
Get Booking — Verify Creation
      ↓
Update Booking
      ↓
Get Booking — Verify Update
      ↓
Delete Booking
      ↓
Verify Booking Deleted
```

This provides both operation-level and post-condition validation.

---

## Dynamic Data Correlation

The collection uses runtime values instead of hard-coded IDs and tokens.

### Authentication Token

The `Create Token` request extracts the generated token from the response and stores it in:

```text
{{token}}
```

Authenticated requests then use:

```text
Cookie: token={{token}}
```

### Booking ID

The `Create Booking` request extracts the generated booking ID and stores it in:

```text
{{bookingid}}
```

The generated booking ID is reused throughout the main CRUD workflow.

This allows the collection to remain reusable across multiple executions without depending on hard-coded booking IDs.

---

## Environment Management

The project uses a dedicated Postman environment:

```text
Restful Booker - QA
```

Environment variables are divided into:

### Static Configuration

```text
baseUrl
username
password
createFirstname
createLastname
updateFirstname
```

### Runtime Variables

```text
token
bookingid
```

Runtime variables are initially blank and populated during execution.

Before committing the environment to GitHub, runtime values are cleared to avoid storing stale execution data.

Detailed environment information is available in:

[`docs/environment.md`](docs/environment.md)

---

## Project Structure

```text
restful-booker-postman-api/
│
├── README.md
├── .gitignore
│
├── collections/
│   └── Restful-Booker API Suite.postman_collection.json
│
├── environments/
│   └── Restful-Booker-QA.postman_environment.json
│
└── docs/
    ├── environment.md
    ├── execution-strategy.md
    └── test-scenarios.md
```

---

## How to Run

### 1. Install Postman

Install Postman on your system.

### 2. Clone the Repository

```bash
git clone https://github.com/arindam0111/restful-booker-postman-api.git
```

### 3. Import the Collection

Import:

```text
collections/Restful-Booker API Suite.postman_collection.json
```

### 4. Import the Environment

Import:

```text
environments/Restful-Booker-QA.postman_environment.json
```

### 5. Select the Environment

Select:

```text
Restful Booker - QA
```

### 6. Verify Runtime Variables

Initially:

```text
token = ""
bookingid = ""
```

### 7. Execute the Smoke Flow

Run:

```text
Create Token
Create Booking
```

### 8. Execute CRUD Regression

Run the requests in the following order:

```text
Create Token
Create Booking
Get Booking — Verify Creation
Update Booking
Get Booking — Verify Update
Delete Booking
Verify Booking Deleted
```

### 9. Run Negative and Boundary Scenarios

Execute the independent scenarios separately or as part of the full regression suite.

---

## Execution Strategy

The collection supports multiple execution strategies.

### Smoke

```text
Create Token → Create Booking
```

Used for a quick API health check.

### CRUD Regression

Validates the complete booking lifecycle.

### Negative Testing

Validates invalid resource, authentication, and data scenarios.

### Boundary Testing

Validates selected edge conditions such as zero price and same-day dates.

### Full Regression

Executes the complete set of applicable scenarios.

### Multiple Iterations

The CRUD regression flow can be executed for multiple iterations using the Postman Collection Runner.

Detailed execution guidance is available in:

[`docs/execution-strategy.md`](docs/execution-strategy.md)

---

## Automation Features

### Dynamic Authentication

Authentication tokens are extracted automatically from the Create Token response.

### Dynamic Booking Correlation

Booking IDs are extracted from Create Booking and reused throughout the CRUD workflow.

### Collection-Level Header Handling

A collection-level pre-request script ensures that requests have the standard:

```text
Accept: application/json
```

header when it is not already defined.

### Post-Condition Validation

Update and Delete operations are independently verified using subsequent GET requests.

### Test Data Isolation

Negative and boundary scenarios are designed so they do not overwrite the primary CRUD `bookingid`.

### Reusable Environment

Common configuration and runtime values are managed through Postman environment variables.

### Regression Execution

The collection supports both focused scenario execution and complete regression through the Postman Collection Runner.

---

## Authentication

The project uses the Restful Booker authentication endpoint:

```text
POST /auth
```

Valid credentials generate a runtime authentication token.

Authenticated operations use:

```text
Cookie: token={{token}}
```

The authentication token is generated dynamically during execution rather than being hard-coded.

---

## Test Design Approach

The suite follows a layered API test-design approach:

### Positive Testing

Validates expected behavior for successful API operations.

### Negative Testing

Validates API behavior when invalid IDs, authentication data, or request data are supplied.

### Boundary Testing

Validates API behavior using selected edge-case data.

### Dynamic Correlation

Uses runtime values generated by previous requests.

### Post-Condition Verification

Validates that state changes are actually persisted or removed.

### Test Data Isolation

Keeps independent scenarios separate from the primary CRUD workflow.

---

## Validation Results

The collection has been validated through repeated execution of the main CRUD regression flow.

The final exported collection and environment were also re-imported and successfully executed.

Validation covered:

* Authentication
* Dynamic token generation
* Dynamic booking ID correlation
* Create booking
* Read booking
* Update booking
* Delete booking
* Delete verification
* Negative scenarios
* Boundary scenarios
* Multiple-iteration execution
* Environment variable resolution
* Export/re-import portability

---

## Documentation

Additional project documentation:

* [Environment Configuration](docs/environment.md)
* [Execution Strategy](docs/execution-strategy.md)
* [Test Scenarios](docs/test-scenarios.md)

---

## Skills Demonstrated

This project demonstrates practical experience with:

* API Testing
* Postman
* REST APIs
* JavaScript test scripting
* Authentication testing
* CRUD testing
* Dynamic data correlation
* Environment management
* Positive testing
* Negative testing
* Boundary testing
* Regression testing
* Test data management
* Postman Collection Runner
* Test design
* API response validation
* Git and GitHub
* Technical documentation

---

## Portfolio Context

This project is part of a broader QA/SDET automation portfolio covering:

* UI automation with Playwright and TypeScript
* UI automation with Selenium and Java
* API automation with Postman
* API/performance testing with k6

Each project focuses on a different layer of the software testing and automation stack.

---

## Author

**Arindam Chowdhury**

QA Automation Engineer / SDET

Focus Areas:

* UI Test Automation
* API Testing
* Test Automation Frameworks
* Manual & Functional Testing
* Playwright
* Selenium
* Postman
* k6

---

## Disclaimer

This project uses the publicly available Restful Booker demo API for learning, testing, and portfolio demonstration purposes.

No production systems or confidential application data are used.
