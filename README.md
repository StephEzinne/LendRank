

# LendRank - On-Chain Credit Scoring & Under-Collateralized Loan System

A Clarity smart contract for the Stacks blockchain implementing decentralized credit scoring and under-collateralized loans, based on on-chain reputation.

---

## 🚀 Features

* **Decentralized Credit Scoring**
  Each user has an evolving on-chain credit score that increases with successful repayments and decreases on defaults.

* **Under-Collateralized Loans**
  Users with high enough credit scores can borrow more than they collateralize.

* **Dynamic Interest Rates & Collateral Requirements**
  Based on the borrower’s score, the loan conditions (interest rate and required collateral) adjust automatically.

* **Loan Management**
  Users can:

  * Initialize their credit profile
  * Request a loan
  * Repay a loan
  * View active loans and scores
    Admins can:
  * Mark loans as defaulted

* **Reputation-Based Lending**
  The system rewards good behavior and penalizes defaults, promoting responsible borrowing.

---

## 📚 Data Structures

### 🔹 `UserScores`

Tracks credit information for each user:

```clojure
{
  score: uint,               ;; Between 50 and 100
  total-borrowed: uint,
  total-repaid: uint,
  loans-taken: uint,
  loans-repaid: uint,
  last-update: uint          ;; Block height of last update
}
```

### 🔹 `Loans`

Tracks each loan using a unique `loan-id`:

```clojure
{
  borrower: principal,
  amount: uint,
  collateral: uint,
  due-height: uint,
  interest-rate: uint,
  is-active: bool,
  is-defaulted: bool,
  repaid-amount: uint
}
```

### 🔹 `UserLoans`

Stores a list of up to 20 active loan IDs per user.

---

## ⚙️ Functions

### 🔐 Admin-Only

* `mark-loan-defaulted (loan-id)`
  Marks a loan as defaulted if it has passed its due block.

---

### 👤 Public User Functions

* `initialize-score`
  Sets up a user's credit profile (score starts at 50).

* `request-loan (amount, collateral, duration)`
  Requests a loan. Requires:

  * Credit score ≥ 70
  * Max 5 active loans
  * Collateral ≥ required minimum

* `repay-loan (loan-id, amount)`
  Repays a loan. If full repayment is made:

  * Collateral is returned
  * Credit score is increased

---

### 📖 Read-Only Functions

* `get-user-score (user)`
  Returns a user's credit profile.

* `get-loan (loan-id)`
  Returns details about a specific loan.

* `get-user-active-loans (user)`
  Returns a user's current active loans.

---

## 🧮 Internal Logic

### 📌 Credit Score Rules

* Starts at **50**
* Max score: **100**
* Loan eligibility: **≥ 70**
* Repayment increases score by **2**
* Default reduces score by **10**

### 📌 Collateral & Interest Calculations

* Required collateral = `amount * (100 - (score * 0.5)) / 100`
* Interest rate = `10 - (score * 0.05)%`

---

## 🧑‍💼 Roles

* **Contract Owner**: Can mark loans as defaulted
* **Borrowers**: Can request and repay loans based on their credit profile

---

## 🔒 Errors

| Error Code | Meaning                   |
| ---------- | ------------------------- |
| u1         | Unauthorized              |
| u2         | Insufficient balance      |
| u3         | Invalid amount            |
| u4         | Loan not found            |
| u5         | Loan already defaulted    |
| u6         | Insufficient credit score |
| u7         | Too many active loans     |
| u8         | Loan not yet due          |

---

## 🔄 State Variables

* `next-loan-id`: Incremental ID for loans
* `total-stx-locked`: Tracks total STX held as collateral

---
