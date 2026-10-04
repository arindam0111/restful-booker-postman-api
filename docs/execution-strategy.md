# Collection Execution Strategy

## Overview

The Restful Booker Postman API automation suite is organized to support focused execution as well as complete regression testing.

The execution strategy separates dependent CRUD workflows from independent negative and boundary scenarios.

## Execution Categories

The collection supports the following execution categories:

1. Smoke Testing
2. CRUD Regression
3. Negative Testing
4. Boundary Testing
5. Full Regression
6. Multiple-Iteration Execution

---

## 1. Smoke Testing

The smoke flow validates that the API is available and the basic authentication and booking creation functionality are working.

### Recommended Flow

```text id="j9sqoe"
Create Token
      ↓
Create Booking
```

### Purpose

Smoke testing provides a quick validation of the core API functionality before executing the complete regression suite.

### Key Validations

* Authentication succeeds.
* A valid token is generated.
* Booking creation succeeds.
* A valid booking ID is generated.

---

## 2. CRUD Regression

The CRUD regression flow validates the complete lifecycle of a booking.

### Recommended Execution Order

```text id="u6h29k"
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

### Purpose

This flow validates the complete resource lifecycle:

```text id="zqf5n2"
Create → Read → Update → Delete → Verify
```

### Dependency

The CRUD flow is dependent on runtime data:

* `token`
* `bookingid`

Therefore, the requests should be executed in the defined order.

---

## 3. Negative Testing

Negative scenarios validate how the API behaves when invalid inputs or authentication data are provided.

### Included Scenarios

| Scenario                       | Expected Result                                   |
| ------------------------------ | ------------------------------------------------- |
| Get Booking — Invalid ID       | `404 Not Found`                                   |
| Update Booking — Invalid Token | `403 Forbidden`                                   |
| Create Booking — Invalid Data  | API response validates handling of invalid values |

### Purpose

Negative testing verifies that the automation suite can validate unsuccessful or unexpected input conditions rather than testing only successful business flows.

### Isolation

Negative scenarios should not overwrite the primary `bookingid` used by the CRUD regression flow.

---

## 4. Boundary Testing

Boundary scenarios validate behavior around specific data limits or edge conditions.

### Included Scenarios

| Scenario                        | Expected Result                   |
| ------------------------------- | --------------------------------- |
| Create Booking — Zero Price     | `200 OK` and generated booking ID |
| Create Booking — Same Day Dates | `200 OK` and generated booking ID |

### Purpose

Boundary testing validates API behavior using edge-case data that may expose validation or business-rule issues.

### Isolation

Boundary scenarios use independent booking data and must not overwrite the primary CRUD `bookingid`.

---

## 5. Full Regression

Full regression executes all applicable positive, negative, and boundary scenarios.

### Coverage

```text id="m16kdi"
Authentication
     ↓
Positive CRUD
     ↓
Negative Scenarios
     ↓
Boundary Scenarios
```

The full regression suite is useful after collection changes or before publishing a new version of the test suite.

---

## 6. Multiple-Iteration Execution

The main CRUD regression flow can be executed for multiple iterations using the Postman Collection Runner.

### Recommended Configuration

For CRUD regression:

```text id="s4q5l1"
Iterations: 3
```

Each iteration should:

1. Generate a new authentication token.
2. Create a new booking.
3. Capture the generated booking ID.
4. Verify the created booking.
5. Update the booking.
6. Verify the updated booking.
7. Delete the booking.
8. Verify that the booking has been deleted.

### Runtime Data

The following values are generated during each execution:

```text id="v6p7iz"
token
bookingid
```

This prevents the test from depending on hard-coded booking IDs.

---

## Execution Dependencies

The following dependencies must be respected:

| Request                       | Dependency             |
| ----------------------------- | ---------------------- |
| Create Token                  | `username`, `password` |
| Create Booking                | `baseUrl`              |
| Get Booking — Verify Creation | `bookingid`            |
| Update Booking                | `bookingid`, `token`   |
| Get Booking — Verify Update   | `bookingid`            |
| Delete Booking                | `bookingid`, `token`   |
| Verify Booking Deleted        | `bookingid`            |

The CRUD workflow should therefore be executed sequentially.

---

## Independent Scenarios

The following scenarios are designed to remain independent from the primary CRUD booking:

### Negative

* Get Booking — Invalid ID
* Update Booking — Invalid Token
* Create Booking — Invalid Data

### Boundary

* Create Booking — Zero Price
* Create Booking — Same Day Dates

These scenarios should not modify the main `bookingid` environment variable.

---

## Execution Matrix

| Execution Type      | Scenarios                    | Recommended Use                |
| ------------------- | ---------------------------- | ------------------------------ |
| Smoke               | Create Token, Create Booking | Quick health check             |
| CRUD Regression     | Complete booking lifecycle   | Functional regression          |
| Negative            | 3 negative scenarios         | Error handling validation      |
| Boundary            | 2 boundary scenarios         | Edge-case validation           |
| Full Regression     | All applicable scenarios     | Complete validation            |
| Multiple Iterations | CRUD regression × 3          | Data correlation and stability |

---

## Best Practices

* Select the correct Postman environment before execution.
* Execute dependent CRUD requests in order.
* Use dynamic token and booking ID values.
* Keep negative and boundary scenarios independent.
* Avoid hard-coded booking IDs.
* Run multiple iterations to validate runtime data correlation.
* Clear runtime values before exporting the environment.
* Re-import exported collection and environment files to validate portability.
* Review test results after collection changes.
* Avoid committing runtime tokens or stale booking IDs.

---

## Regression Validation

The collection has been validated through repeated execution of the main CRUD workflow.

The exported collection and environment were also re-imported and executed successfully.

This validates:

* Request organization
* Variable resolution
* Authentication
* Dynamic data correlation
* CRUD workflow
* Negative scenarios
* Boundary scenarios
* Collection Runner compatibility
* Export/re-import portability

## Execution Goal

The overall execution strategy is designed to provide reliable API validation while keeping test data reusable, runtime values dynamic, and dependent workflows predictable.
