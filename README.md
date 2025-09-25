

# STX-Veridocs - Document Verification Smart Contract (Clarity)

This smart contract provides a **secure, decentralized, and auditable** mechanism for submitting, modifying, verifying, and retrieving documents on the **Stacks blockchain**. It emphasizes **input validation**, **permissioned verification**, and **version control**, making it suitable for use cases such as legal document notarization, educational certificate validation, and other tamper-resistant digital records.

---

## 🚀 Features

* ✅ **Document Submission & Modification**

  * Only document owners can submit or modify documents.
  * Modification is disallowed after verification is complete.
  * Automatic version incrementing on modification.

* 🔍 **Verification Workflow**

  * Only authorized users with verification permission can verify documents.
  * Prevents duplicate verification or unauthorized verifications.
  * Captures verifying authority and marks the document as `VERIFIED`.

* 🔐 **Permission Control**

  * Fine-grained permission settings for each document:

    * `document-viewing-permission`
    * `document-verification-permission`

* 📚 **Validation & Safety**

  * Strict validation of inputs (hashes, metadata, principals).
  * Consistent use of error codes.
  * `safe-get` patterns prevent unsafe unwrapping of maps.

* 🧾 **Version Control & Auditability**

  * Each modification increments the document version.
  * Stores original submission and modification timestamps.

---

## 🏗️ Contract Architecture

### 📦 Data Structures

#### `document-records` (Map)

Stores each document’s complete metadata.

```clojure
{ document-hash-id: (buff 32) } => {
  document-owner: principal,
  document-content-hash: (buff 32),
  submission-timestamp: uint,
  verification-status: (string-ascii 20),
  verification-authority: (optional principal),
  document-metadata: (string-utf8 256),
  document-version: uint,
  verification-complete: bool
}
```

#### `document-permissions` (Map)

Manages user-specific permissions for a document.

```clojure
{ document-hash-id: (buff 32), authorized-user: principal } => {
  document-viewing-permission: bool,
  document-verification-permission: bool
}
```

---

## 🛠️ Key Functions

### 🔒 Private Functions

* `validate-and-sanitize-hash`: Validates hash size (must be 32 bytes).
* `validate-and-sanitize-metadata`: Validates metadata UTF-8 string.
* `validate-and-sanitize-user`: Ensures a user is not the document owner.
* `safe-get-document`: Wraps a safe retrieval from the `document-records` map.

---

### 📖 Read-Only Functions

#### `get-document-details (document-hash-id)`

Returns all details of a validated document.

#### `get-user-permissions (document-hash-id, authorized-user)`

Returns the permission levels for a specific user on a document.

---

### 🔓 Public Functions

#### `modify-existing-document`

Updates a document’s hash and metadata.
**Only allowed by the document owner before verification.**

Parameters:

* `document-hash-id`: (buff 32)
* `updated-content-hash`: (buff 32)
* `updated-metadata`: (string-utf8 256)

#### `perform-document-verification`

Verifies the document as an authorized user.
Updates status, verification authority, and flags the document as verified.

Parameters:

* `document-hash-id`: (buff 32)

---

## ⚠️ Error Codes

| Code       | Meaning                   |
| ---------- | ------------------------- |
| `err u100` | Unauthorized access       |
| `err u101` | Duplicate document        |
| `err u102` | Document missing          |
| `err u103` | Document already verified |
| `err u104` | Invalid document hash ID  |
| `err u105` | Invalid content hash      |
| `err u106` | Invalid metadata          |
| `err u107` | Invalid authorized user   |
| `err u108` | Invalid input             |
| `err u109` | Permission denied         |

---

## 🔐 Verification Status Constants

* `"PENDING"`: Document submitted, awaiting verification.
* `"VERIFIED"`: Verified by an authorized user.

---
