# Test Scenarios

## Overview

The Restful Booker Postman API automation suite contains functional, negative, boundary, authentication, and CRUD validation scenarios.

The suite contains **13 documented scenarios** covering the complete booking lifecycle and important error and edge conditions.

## Test Coverage Summary

| Category         | Scenarios |
| ---------------- | --------: |
| Authentication   |         1 |
| Positive CRUD    |         7 |
| Negative Testing |         3 |
| Boundary Testing |         2 |
| **Total**        |    **13** |

---

# 1. Authentication

## TC-01 — Create Token

**Objective**

Validate that valid credentials can be used to generate an authentication token.

**Request**

```text id="fpldxy"
POST {{baseUrl}}/auth
```

**Validation**

* Response status is `200 OK`.
* Response contains a token.
* Token is stored dynamically in the `token` environment variable.

**Purpose**

The generated token is used by authenticated Update and Delete requests.

---

# 2. Positive CRUD Scenarios

## TC-02 — Get All Booking IDs

**Objective**

Validate that the API returns available booking IDs.

**Request**

```text id="1x4psq"
GET {{baseUrl}}/booking
```

**Validation**

* Response status is `200 OK`.
* Response contains booking ID records.
* Response is successfully processed by the test script.

---

## TC-03 — Create Booking

**Objective**

Validate successful creation of a new booking.

**Request**

```text id="6t2axk"
POST {{baseUrl}}/booking
```

**Validation**

* Response status is `200 OK`.
* Response contains a generated `bookingid`.
* Booking object is returned.
* First name matches the configured create data.
* Last name matches the configured create data.
* Price and booking details are validated.
* Generated booking ID is stored dynamically in `bookingid`.

**Correlation**

The generated booking ID is reused by the subsequent CRUD requests.

---

## TC-04 — Get Booking — Verify Creation

**Objective**

Verify that the booking created by the previous request exists and contains the expected data.

**Request**

```text id="9lq4me"
GET {{baseUrl}}/booking/{{bookingid}}
```

**Validation**

* Response status is `200 OK`.
* Response contains the expected booking.
* Created booking data is validated.

---

## TC-05 — Update Booking

**Objective**

Validate that an existing booking can be updated using a valid authentication token.

**Request**

```text id="7a7h1k"
PUT {{baseUrl}}/booking/{{bookingid}}
```

**Authentication**

```text id="7g9rvy"
Cookie: token={{token}}
```

**Validation**

* Response status is `200 OK`.
* Updated booking is returned.
* Updated first name matches `updateFirstname`.
* Other booking data remains valid.

---

## TC-06 — Get Booking — Verify Update

**Objective**

Verify that the changes made by the Update Booking request were persisted.

**Request**

```text id="y8k1d0"
GET {{baseUrl}}/booking/{{bookingid}}
```

**Validation**

* Response status is `200 OK`.
* Updated booking data is returned.
* Updated first name matches the expected value.

**Purpose**

This is a post-condition validation that independently verifies the update operation.

---

## TC-07 — Delete Booking

**Objective**

Validate successful deletion of an existing booking.

**Request**

```text id="xkn6dj"
DELETE {{baseUrl}}/booking/{{bookingid}}
```

**Authentication**

```text id="vlw6jr"
Cookie: token={{token}}
```

**Validation**

* Response status is `201 Created`.
* Response body contains `Created`.

---

## TC-08 — Verify Booking Deleted

**Objective**

Verify that the booking can no longer be retrieved after deletion.

**Request**

```text id="6i8l0g"
GET {{baseUrl}}/booking/{{bookingid}}
```

**Validation**

* Response status is `404 Not Found`.
* Response body contains `Not Found`.

**Purpose**

This provides post-condition validation for the Delete operation.

---

# 3. Negative Testing

## TC-09 — Get Booking — Invalid ID

**Objective**

Validate API behavior when an invalid booking ID is requested.

**Request**

```text id="j0o6q4"
GET {{baseUrl}}/booking/999999999
```

**Validation**

* Response status is `404 Not Found`.
* Response body contains `Not Found`.

**Purpose**

Validates handling of a non-existent resource.

---

## TC-10 — Update Booking — Invalid Token

**Objective**

Validate that an invalid authentication token is rejected.

**Request**

```text id="b5p3i1"
PUT {{baseUrl}}/booking/{{bookingid}}
```

**Authentication**

```text id="6aw0ip"
Cookie: token=invalid_token_12345
```

**Validation**

* Response status is `403 Forbidden`.
* Response body contains `Forbidden`.

**Purpose**

Validates authentication and authorization handling for protected operations.

---

## TC-11 — Create Booking — Invalid Data

**Objective**

Validate API behavior when booking data contains invalid values.

**Test Data**

* Empty first name
* Empty last name
* Negative total price
* Invalid `depositpaid` data type
* Empty check-in date
* Empty check-out date

**Validation**

* Response behavior is captured and validated.
* A booking ID is generated by the demo API.
* Invalid values returned by the API are verified.
* The scenario does not overwrite the main CRUD `bookingid`.

**Observation**

The Restful Booker demo API accepts several invalid values rather than rejecting them with a validation error.

This behavior is documented as an API behavior observation rather than incorrectly treating the response as a test failure.

---

# 4. Boundary Testing

## TC-12 — Create Booking — Zero Price

**Objective**

Validate API behavior when the booking price is set to zero.

**Test Data**

```text id="a5b9jx"
totalprice = 0
depositpaid = false
```

**Validation**

* Response status is `200 OK`.
* A booking ID is generated.
* `totalprice` is accepted as zero.
* The scenario does not overwrite the main CRUD `bookingid`.

**Purpose**

Validates behavior at a lower boundary for the booking price.

---

## TC-13 — Create Booking — Same Day Dates

**Objective**

Validate API behavior when check-in and check-out occur on the same date.

**Test Data**

```text id="t86q5n"
checkin  = 2026-10-10
checkout = 2026-10-10
```

**Validation**

* Response status is `200 OK`.
* A booking ID is generated.
* Same-day check-in and check-out values are accepted.
* The scenario does not overwrite the main CRUD `bookingid`.

**Purpose**

Validates API behavior for an edge case involving booking dates.

---

# End-to-End CRUD Flow

The primary CRUD regression flow is:

```text id="r8x8up"
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

This flow validates the complete resource lifecycle:

```text id="l0nq9d"
Authentication
      ↓
Create
      ↓
Read
      ↓
Update
      ↓
Read / Verify
      ↓
Delete
      ↓
Verify Deletion
```

# Test Design Approach

The suite follows a layered test-design approach:

### Positive Testing

Validates expected API behavior for normal CRUD operations.

### Negative Testing

Validates API behavior when invalid IDs, authentication data, or request data are supplied.

### Boundary Testing

Validates behavior around selected data boundaries and edge cases.

### Dynamic Correlation

Runtime values such as the authentication token and booking ID are extracted from responses and reused in subsequent requests.

### Post-Condition Validation

Update and Delete operations are independently verified through subsequent GET requests.

### Test Data Isolation

Negative and boundary scenarios do not overwrite the primary booking ID used by the CRUD regression flow.

# Coverage Summary

The 13 scenarios provide coverage across:

* Authentication
* Booking discovery
* Booking creation
* Booking retrieval
* Booking update
* Booking deletion
* Post-condition verification
* Invalid resource handling
* Invalid authentication handling
* Invalid data handling
* Zero-value boundary testing
* Same-day date boundary testing
* Dynamic test-data correlation

The focus of the suite is meaningful API coverage and reliable validation rather than maximizing the number of test cases.
