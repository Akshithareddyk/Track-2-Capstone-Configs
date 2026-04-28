# bankxyz Product Configuration (PRDUSXEE1001)

## Overview
bankxyz Product 1001 (`PRDUSXEE1001`) is a **subprime credit card product** with support for both **secured (FD-backed)** and **unsecured** credit programs, plus EMI loan conversion. The configuration follows Zeta Banking Platform standards and is organized by provider/module.

- **Tenant:** bankxyz (`bankxyz`) | Tenant ID: `600326`
- **Product Bundle Code:** `PRDUSXEE1001`
- **Bundle Definition Code:** `BUNUSXEE1001`
- **Template:** `us-credit`

---

## Directory Structure

```
product_PRDUSXEE1001/
├── provider_ruby/                         # Primary revolving credit module
│   ├── account-product.yaml               # Main credit product definition
│   ├── account-product-family.yaml        # Product family & COA metadata
│   ├── fee_promo_programs/
│   │   └── fee-promo-program_annual-fee-waiveoff-spend.yaml
│   ├── rate_specifications/
│   │   ├── rate-specification-secured-credit.yaml
│   │   └── rate-specification-unsecured-risk-levels.yaml
│   └── policies/
│       ├── Interest/
│       │   ├── interest-policy.yaml
│       │   ├── interest-program.yaml          # General fallback program
│       │   ├── interest-term-resolution-policy.yaml
│       │   ├── minimum-interest-fee-policy.yaml
│       │   ├── scra-program.yaml
│       │   ├── secured-credit-program.yaml
│       │   └── unsecured-credit-program.yaml
│       ├── fee_policies/
│       │   ├── annual-fee-policy.yaml
│       │   ├── card-replacement-fee.yaml
│       │   ├── cash-advance-fee.yaml
│       │   ├── forex-advance-fee.yaml
│       │   ├── forex-markup-fee.yaml
│       │   ├── late-fee.yaml
│       │   ├── mad-policy.yaml
│       │   └── spend-limit.yaml
│       ├── statement-policy/
│       │   └── statement-policy.yaml
│       └── t-policy/
│           └── TPolicy.yaml
├── provider_emerald/                      # EMI loan module
│   ├── account-product-family.yaml
│   ├── account-product.yaml
│   └── Policies/
│       ├── account-closure-policy.yaml
│       ├── interest-policy.yaml
│       ├── loan-conversion-policy.yaml
│       ├── loan-conversion-program.yaml
│       └── loan-repayment-policy.yaml
├── provider_acropolis/                    # Card issuance module
│   ├── card-product.yaml
│   └── card-sku.yaml
├── provider_athena/                       # Payment & settlement module
│   ├── form-factor-product.yaml
│   ├── payment-account-product.yaml
│   ├── payment-product.yaml
│   └── resource-product.yaml
├── provider_aether/                       # Product bundling module
│   ├── bundle-defination.yaml
│   └── product-bundle.yaml
└── provider_atropos/                      # Event subscription & lifecycle
    └── atropos-subscription.yaml
```

---

## Core Configuration Files

### 1. **provider_ruby/account-product.yaml**
**Purpose:** Central product definition for the revolving credit account.

**Key Configurations:**
- **Product Code:** `RAPINXYZ1001`
- **Product Family:** `APFINXYZ1001`
- **COA Code:** `COAUSXEE1001`
- **Credit Limits:** Min $200 | Max $5,000
- **Cash Limit:** 30% of credit limit
- **Over-Limit Percent:** 0%
- **Channel Code:** `CHNUSZZ1101`
- **Secured Card Attributes:**
  - `fundingRequired: true` | `fundingType: FD` | `loanType: SECURED`
  - `externalFDProvider: EXTERNAL_FD_PROVIDER`
  - `creditLimitFormula: "externalFDAmount * 0.90"` | `collateralPercentage: 0.90`

**Policies Referenced:**
| Policy Type | Code | Version |
|-------------|------|---------|
| Coupon Accounting | CAPUSXEE1001 | 3 |
| Credit Allocation | CROUSXEE1001 | 2 |
| Charge-Off Decision | CODUSXEE1001 | 5 |
| Account Closure | ACPUSXEE1001 | 2 |
| Account Number Generation | ANGUSXEE1001 | 4 |
| Interest Term Resolution | TRPUSXEE1001 | 3 |
| Delinquency | DLQUSXEE1001 | 7 |
| Dormancy | DORUSXEE1001 | 2 |
| Interest | INTUSXEE1001 | 18 |
| MAD (Minimum Amount Due) | MADUSXEE1001 | 18 |
| Repayment | REPUSXEE1001 | 4 |
| Statement | STMUSXEE1001 | 22 |
| Spend Limit | SPLUSXEE1001 | 2 |

**Fee Policies Referenced:**
| Fee | Code | Version |
|-----|------|---------|
| Dispute Processing | DPPUSXEE1001 | 5 |
| Annual Fee | ANFINXYZ1001 | 5 |
| Joining Fee | JFFUSXEE1001 | 4 |
| Card Replacement | CRFUSXEE1001 | 3 |
| Cash Advance | CAFUSXEE1001 | 34 |
| Forex Markup | FCFUSXEE1001 | 32 |
| Forex Advance | FCCUSXEE1001 | 26 |
| Late Fee | LPFUSXEE1001 | 42 |
| Minimum Interest Fee | MIFUSXEE1001 | 19 |

---

### 2. **provider_ruby/account-product-family.yaml**
**Purpose:** Product family metadata and COA (Chart of Accounts) configuration.

**Key Configurations:**
- **Family Code:** `APFINXYZ1001`
- **COA Code:** `COAUSXEE1001`
- **Parent COA Node:** `COAUSXEE1001_Node_Revolving_Credit`
- **Name:** bankxyz Account Product Family 1001

---

### 3. **provider_ruby/policies/Interest/interest-policy.yaml**
**Purpose:** Master interest configuration for the revolving credit account.

**Key Configurations:**
- **Policy Code:** `INTUSXEE1001`
- **Computation Type:** `DAILYBALANCEWITHMONTHLYCOMPOUNDING`
- **Ratification Strategy:** `IMMEDIATE`
- **Interest Threshold:** $50
- **Day Count Method:** `ACTUAL/ACTUAL`
- **Rounding:** `NEAR` | Precision: 2 decimals
- **Grace Period on Interest:** `false`
- **PayOff Variance:** 0
- **Default Rate Card Code:** `VARPRIMEXEE1001`
- **Linked Interest Program:** `IPGUSXEE100101`
- **Template:** `us-credit`

**Liquidation Models:**
| Balance Type | Unbilled Model | Billed Model |
|---|---|---|
| retail | DeferCurrentIfOpenBalPaidElseBillCurrent | WaiveDeferredAndWaiveCurrentIfOpenBalPaidElseBillDeferredAndBillCurrent |
| cash | BillUnconditional | BillUnconditional |

**Aggregation Strategy:**
- Fixed Variables: ProgramType, BalanceType, RateType, InterestRate, TermsValidityDate
- Variable: ProgramType, BalanceType, RateType, IndexRateSpecCode, Margin, TermsValidityDate

---

### 4. **provider_ruby/policies/Interest/interest-program.yaml** *(General Fallback)*
**Purpose:** General fallback interest program — applies to all accounts without a specific program match.

**Key Configurations:**
- **Program Code:** `IPGUSXEE100101`
- **Type:** `General`
- **Rate Type:** `VARIABLE`
- **Precedence:** `900` (lowest — always last resort)
- **Rate Table:** `VARPRIMEXEE1001`

**Interest Rules:**
| Rule ID | Posting Categories | Margin (APR) | Grace Period |
|---------|-------------------|--------------|--------------|
| RULE01 | RetailPrincipalUnbilled, RetailInterestUnbilled, AnnualFeeUnbilled, LateFeeUnbilled, OverlimitFeeUnbilled | **28.99%** | POLICY |
| RULE02 | CashPrincipalUnbilled, CashInterestUnbilled, CashAdvanceFeeUnbilled | **32.99%** | 0 days (ABSOLUTE) |
| RULE03 | ExcessPayment, PendingAllocation | 0% | — |

---

### 5. **provider_ruby/policies/Interest/secured-credit-program.yaml**
**Purpose:** Interest program for **FD-backed (secured) accounts**.

**Key Configurations:**
- **Program Code:** `SCPUSXEE1001`
- **Type:** `Secured`
- **Rate Type:** `FIXED`
- **Precedence:** `200`
- **Rate Spec:** `SECURECREDIT1001` → **25% APR** (see rate_specifications/)
- **Trigger Tag:** `cardStatusSecured`

**Interest Rules:**
| Rule ID | Description | Posting Categories | Cash Grace |
|---------|-------------|-------------------|------------|
| RULE_SECURED_RETAIL | Retail for FD-backed accounts | RetailPrincipalUnbilled, RetailInterestUnbilled, AnnualFeeUnbilled, LateFeeUnbilled, OverlimitFeeUnbilled | POLICY |
| RULE_SECURED_CASH | Cash advance for FD-backed accounts | CashPrincipalUnbilled, CashInterestUnbilled, CashAdvanceFeeUnbilled | 0 days (ABSOLUTE), margin: +3% |

---

### 6. **provider_ruby/policies/Interest/unsecured-credit-program.yaml**
**Purpose:** Interest program for **unsecured accounts** with risk-based APR tiers.

**Key Configurations:**
- **Program Code:** `UCPUSXEE1001`
- **Type:** `Unsecured`
- **Rate Type:** `RISK_BASED`
- **Precedence:** `300`
- **Trigger Tags:** `riskLevel=1` or `riskLevel:1`, `riskLevel=2`, `riskLevel=3`

**Risk Tier → Rate Table → APR:**
| Risk Level | Tag | Rate Table Code | APR |
|------------|-----|-----------------|-----|
| Level 3 (Low Risk) | `riskLevel=3` | UNSECUREDLOAN_L3_1001 | **20%** |
| Level 2 (Medium Risk) | `riskLevel=2` | UNSECUREDLOAN_L2_1001 | **21%** |
| Level 1 (High Risk) | `riskLevel=1` | UNSECUREDLOAN_L1_1001 | **23%** |

*Rules exist for both retail and cash advance posting categories for each risk tier (6 rules total).*

---

### 7. **provider_ruby/policies/Interest/scra-program.yaml**
**Purpose:** SCRA (Servicemembers Civil Relief Act) compliance — 6% APR cap for military members.

**Key Configurations:**
- **Program Code:** `SCRAUSXEE1001`
- **Type:** `SCRA`
- **Rate Type:** `FIXED`
- **Precedence:** `100` (highest — always evaluated first)
- **APR:** **6%** (legal maximum under 50 U.S.C. § 3953)
- **Rate Table:** `IRTUS000003`
- **Trigger Tag:** `tag://SCRAStatus/LEVEL_1` (prefix match: `tag://SCRAStatus`)
- **Rules:** 2 (RULE01 — retail; RULE02 — cash)

---

### 8. **provider_ruby/policies/Interest/minimum-interest-fee-policy.yaml**
**Purpose:** Sets the minimum interest fee floor per billing cycle.

**Key Configurations:**
- **Policy Code:** `MIFUSXEE1001`
- **Minimum Base Fee:** $1
- **Template:** `us-credit`

---

### 9. **provider_ruby/policies/Interest/interest-term-resolution-policy.yaml**
**Purpose:** Controls repricing (coupon repricing) strategy when account tags change — particularly for SCRA activation and expiry.

**Key Configurations:**
- **Policy Code:** `TRPUSXEE1001`
- **SCRA Activation:** GENERAL / UNSECURED / SECURED balances repriced to SCRA (LOWESTAPR)
- **SCRA Expiry:** Balances repriced back to GENERAL / UNSECURED / SECURED (LOWESTAPR)
- **SCRA itself:** `DONT_REPRICE`

---

### 10. **provider_ruby/policies/fee_policies/annual-fee-policy.yaml**
**Purpose:** Annual fee charged to active accounts.

**Key Configurations:**
- **Policy Code:** `ANFINXYZ1001`
- **Amount:** $50 USD (fixed)
- **Valid:** 2023-01-01 → 2099-12-31

---

### 11. **provider_ruby/policies/fee_policies/cash-advance-fee.yaml**
**Purpose:** Fee charged on cash advance transactions.

**Key Configurations:**
- **Policy Code:** `CAFUSXEE1001`
- **Type:** Variable — **5%** of transaction amount
- **Minimum:** $10

---

### 12. **provider_ruby/policies/fee_policies/late-fee.yaml**
**Purpose:** Late payment fee with tiered first/repeat occurrence amounts.

**Key Configurations:**
- **Policy Code:** `LPFUSXEE1001`
- **Precedence Code:** `ZZ0025`
- **First Occurrence:** Base $41 | Max $35
- **Repeat Occurrence:** Base $30 | Max $25
- **Repeat Window:** 6 billing cycles
- **Excludes:** `LateFeeUnbilled` balance from fee recalculation

---

### 13. **provider_ruby/policies/fee_policies/forex-markup-fee.yaml**
**Purpose:** Foreign exchange markup fee on cross-currency and international transactions.

**Key Configurations:**
- **Policy Code:** `FCFUSXEE1001`
- **Fee Schedule:**

| Domestic | Cross-Currency | Fee % |
|----------|----------------|-------|
| true | false | 0.0% |
| true | true | 2.5% |
| false | true | 3.0% |
| false | false | 1.5% |

---

### 14. **provider_ruby/policies/fee_policies/forex-advance-fee.yaml**
**Purpose:** Foreign exchange fee on cash advance transactions in foreign/cross-currency.

**Key Configurations:**
- **Policy Code:** `FCCUSXEE1001`
- **Fee Schedule:**

| Domestic | Cross-Currency | Fee % |
|----------|----------------|-------|
| true | false | 0.0% |
| true | true | 7.5% |
| false | true | 8.5% |
| false | false | 6.5% |

---

### 15. **provider_ruby/policies/fee_policies/card-replacement-fee.yaml**
**Purpose:** Fee charged when a card is replaced.

**Key Configurations:**
- **Policy Code:** `CRFUSXEE1001`
- **Amount:** $10 USD (fixed)
- **Applies to SKU:** `SKUUSXEE100101`

---

### 16. **provider_ruby/policies/fee_policies/mad-policy.yaml**
**Purpose:** Minimum Amount Due calculation rules.

**Key Configurations:**
- **Policy Code:** `MADUS002001`
- **Minimum MAD Amount:** $25
- **Current Due Threshold:** $25 (rounded UP)
- **MAD Rounding:** NEAR
- **Key Components:**
  - 100% of all fees (annual, overlimit, cash advance, interest, etc.)
  - 2% of RetailPrincipal (billed + unbilled)
  - 2% of CashPrincipal (billed + unbilled)

---

### 17. **provider_ruby/policies/fee_policies/spend-limit.yaml**
**Purpose:** Transaction spend limits per channel for new cards.

**Key Configurations:**
- **Policy Code:** `SPLUSXEE7001`
- **COA Code:** `COAUSXEE7001`
- **Disabled for New Cards:** DOMESTIC_ECOM, DOMESTIC_CONTACTLESS, INTERNATIONAL_POS, INTERNATIONAL_ECOM, INTERNATIONAL_ATM, INTERNATIONAL_CONTACTLESS

---

### 18. **provider_ruby/policies/statement-policy/statement-policy.yaml**
**Purpose:** Statement generation, billing cycles, and due date configuration.

**Key Configurations:**
- **Policy Code:** `STMUSXEE1001`
- **Grace Days:** 3
- **Due Date Calculation:** `FixedDueDate`
- **Holiday Treatment:** `retainHoliday`
- **Late Fee Applied On:** `GRACE_DATE`
- **Value Date of Late Fees:** `DUE_DATE`
- **Billing Cycle Change Cool-off:** 180 days
- **Schumer Box:** Enabled | Months to Repay: 36
- **YTD Summary:** Enabled (starts Jan 1)
- **Allowed Billing Cycles:**

| Cycle Code | Due Date |
|------------|----------|
| CYCUS00100102 | 1st of month |
| CYCUS00100103 | 15th of month |
| CYCUS00100104 | 28th of month |

- **Yearly Cycle:** `CYCUS00100129`
- **Statement Rules:** Max 1 statement per year (yearly cycle)

---

### 19. **provider_ruby/fee_promo_programs/fee-promo-program_annual-fee-waiveoff-spend.yaml**
**Purpose:** Promotional program that waives the annual fee for qualifying accounts.

**Key Configurations:**
- **Program Code:** `AFPP00003XEE`
- **Precedence:** `65`
- **Waived Fee:** $0 (annual fee waived)
- **Trigger Tag:** `tag://accountLifecycleStatus?name=ZEROANNUALFEE`
- **Charge Spec:** `CHSUSZZ0021`

---

### 20. **provider_ruby/rate_specifications/rate-specification-secured-credit.yaml**
**Purpose:** Rate specification for FD-backed secured credit accounts.

**Key Configurations:**
- **Rate Spec Code:** `SECURECREDIT1001`
- **APR:** 25.00% (fixed, all amounts, all tenures)
- **Currency:** USD
- **Valid:** 2023-01-01 → 2099-12-31

---

### 21. **provider_ruby/rate_specifications/rate-specification-unsecured-risk-levels.yaml**
**Purpose:** Risk-based rate specifications for unsecured credit (L1, L2, L3).

**Key Configurations:**

| Level | Code | APR |
|-------|------|-----|
| L1 (High Risk) | UNSECUREDCREDIT_L1_1001 | 23.00% |
| L2 (Medium Risk) | UNSECUREDCREDIT_L2_1001 | 21.00% |
| L3 (Low Risk) | UNSECUREDCREDIT_L3_1001 | 20.00% |

- **Currency:** USD | **Valid:** 2025-01-01 → 2099-12-31

---

### 22. **provider_emerald/account-product.yaml**
**Purpose:** Emerald EMI loan product definition.

**Key Configurations:**
- **Product Code:** `EAPUSXEE1001`
- **Segment:** Subprime
- **VBO ID:** `bankxyz`
- **Loan Amount:** Min $100 | Max $2,000
- **Tenure:** Min 3 months | Max 12 months | Default 3 months

**Policies Referenced:**
| Policy | Code | Version |
|--------|------|---------|
| Account Closure | ACPUSXEE1001 | 1 |
| Account Number Generation | ANGUSXEE1001 | 1 |
| Amortization | AMOUSXEE1001 | 1 |
| Interest | INTUSXEE1001 | 1 |
| Repayment | REPUSXEE1001 | 1 |

---

### 23. **provider_emerald/account-product-family.yaml**
**Purpose:** Emerald product family definition for loan accounts.

**Key Configurations:**
- **Family Code:** `EPFUSXEE7295`
- **Name:** Revolving Credit 7295
- **Description:** Product family for unsecure loans
- **COA Code:** `COACODE` | **Parent COA Node:** `COANODECODE`

---

### 24. **provider_emerald/Policies/interest-policy.yaml**
**Purpose:** Interest accrual rules for EMI loans in the Emerald module.

**Key Configurations:**
- **Policy Code:** `EIPUS008001`
- **Rounding:** NEAR
- **Day Count Methods:**
  - First installment & pre-closure: `ACTUAL/360`
  - Re-amortization & default: `30/360G`
- **Interest Component:** `NORMAL_INTEREST_BASE` (`BillUnconditional`)
- **Liquidation Voucher:** `LOAN_INTEREST_LIQUIDATION`

---

### 25. **provider_emerald/Policies/loan-repayment-policy.yaml**
**Purpose:** Repayment terms and sequencing for EMI loans.

**Key Configurations:**
- **Policy Code:** `ERPUSXEE1001`
- **Repayment Mode:** `AUTO`
- **Repayment Sequence:** Fees → Interest → Principal
- **Partial Pay-Off:** Allowed (min amount $500)
- **Auto-Repayment Retries:** 5
- **Longest Overdue First:** `false`

---

### 26. **provider_emerald/Policies/loan-conversion-policy.yaml**
**Purpose:** Rules for converting revolving credit transactions to EMI loans.

**Key Configurations:**
- **Policy Code:** `ELCUS008005`
- **Account Provider:** `ruby`
- **Allowed Loan Types:** `merchantEMI`, `smartEMI`
- **Auto Pre-closure Threshold:** 89.99%
- **Days Past Due for Auto Pre-closure:** 145
- **Instalment Billing Days:** 1

---

### 27. **provider_emerald/Policies/loan-conversion-program.yaml**
**Purpose:** Defines eligible transaction criteria for smart EMI conversion.

**Key Configurations:**
- **Program Code:** `ELCPROGUS005002`
- **Loan Type:** `smartEMI`
- **Transaction Amount:** $200 – $5,000
- **Conversion Window:** 0–7 days after transaction
- **Excluded MCC:** `1409`

---

### 28. **provider_emerald/Policies/account-closure-policy.yaml**
**Purpose:** EMI loan cancellation and pre-closure rules.

**Key Configurations:**
- **Policy Code:** `ECPUSXEE1001`
- **Allow Cancellation:** Yes | Cooling Period: 20 days
- **Allow Pre-Closure:** Yes | Lock-in: 0 days
- **Catch-Up Interest on Pre-Closure:** Included
- **Auto Pre-Closure Threshold:** 89.99%

---

### 29. **provider_acropolis/card-product.yaml**
**Purpose:** Physical and digital card product specification.

**Key Configurations:**
- **Card Product Code:** `CRPUSXEE100101`
- **BIN:** `601150` | Range: `23`
- **Expiry:** 1 month + 4 years
- **Features:** `MAG_STRIPE`, `EMV`, `NFC`
- **Digital Activation:** `AUTO_ACTIVATION`
- **Physical Activation:** `MANUAL`
- **ACS (3DS):** Disabled
- **Reissuance:** Disabled (overrideAllowed: true)
- **Renewal:** Enabled — `SWAP_PIN_FROM_SOURCE_CARD`
- **Replacement:** Enabled — `TERMINATE_SOURCE_CARD`

---

### 30. **provider_acropolis/card-sku.yaml**
**Purpose:** Card manufacturing SKU (Stock Keeping Unit) and inventory configuration.

**Key Configurations:**
- **SKU Code:** `SKUUSXEE100101`
- **Plastic Code:** `BXEE01`
- **Product Code:** `CRPUSXEE100101`
- **Bank Identifier:** `bankxyz`
- **Vendor ID:** `VENUSXEE1001`
- **Inventory Size:** 30,000 | Refilling Rate: 3,000

---

### 31. **provider_athena/payment-product.yaml**
**Purpose:** Payment product definition — links channel codes to accepted payment method codes.

**Key Configurations:**
- **Payment Product Code:** `PYPUSXEE1001`
- **Resource Product Code:** `RSPUSXEE1001`
- **Channel Code:** `CHNUSZZ1101` (with 25 mapped payment codes)

---

### 32. **provider_athena/payment-account-product.yaml**
**Purpose:** Payment account product for credit card payment processing.

**Key Configurations:**
- **Code:** `PPDUSXEE1001`
- **Type:** `CREDIT_CARD`
- **Base Currency:** `USD`
- **Payment Account Provider Code:** `PAPUSXEE1001`

---

### 33. **provider_athena/form-factor-product.yaml**
**Purpose:** Card form factor product — links physical card to its SKU.

**Key Configurations:**
- **Code:** `FFPUSXEE100102`
- **Type:** `card`
- **Provider:** `CMS`
- **SKU ID:** `SKUUSXEE100101`
- **Issuance Status:** `ACTIVE` | **Payment Status:** `ACTIVE`

---

### 34. **provider_athena/resource-product.yaml**
**Purpose:** Resource product — groups form factor products for authorization routing.

**Key Configurations:**
- **Code:** `RSPUSXEE1001`
- **Form Factor Products:** `FFPUSXEE100101`

---

### 35. **provider_aether/bundle-defination.yaml**
**Purpose:** Product bundle definition — wires together all sub-products.

**Key Configurations:**
- **Bundle Code:** `BUNUSXEE1001`
- **Blueprint Code:** `PBBUSXEE1001`
- **Bundle Status Policy:** `BSPUSXEE1001`
- **Issue Inactive Card:** `true`
- **Apply Spend Limits:** `true`

**Product Mappings:**
| Role | Code | Provider |
|------|------|----------|
| Account Product | RAPINXYZ1001 (family: APFINXYZ1001) | RUBY |
| Payment Product | PYPUSXEE1001 | — |
| Payment Account Product | PPDUSXEE1001 | — |
| Loan Account Product | EAPUSXEE1001 (family: EAPUSXEE1001) | EMERALD |
| Notification Product | NOTIFIUSXEE1001 | LUMINOS |

---

### 36. **provider_aether/product-bundle.yaml**
**Purpose:** Top-level product bundle registration.

**Key Configurations:**
- **Product Code:** `PRDUSXEE1001`
- **Code Suffix:** `1001`
- **Status:** `active`
- **Description:** bankxyz Subprime Credit Card Product

---

### 37. **provider_atropos/atropos-subscription.yaml**
**Purpose:** Event subscription for real-time authorization interception via webhook.

**Key Configurations:**
- **Subscription Type:** `webhook`
- **Subscriber:** `photon-orcus`
- **Topic:** `_tenant_600326_RESOURCE`
- **Allowed Resource Products:** `RSPUSXEE1001`
- **Triggers On:** Declined/failed payment states (`PAYMENT_DECLINED`, `PAYMENT_REQUEST_DECLINED`, `PAYMENT_FAILED`)
- **Blacklisted Payment Codes:** `PMCUSZZ110131101`, `PMCUSZZ110131100`, `PMCUSZZ110131043`, `PMCUSZZ110131042`
- **Blacklisted Request Types:** `CLEARANCE`

---

## Interest Program Selection Logic

All 4 interest programs function via **precedence-based evaluation** (lower number = higher priority):

```
┌───────────────────────────────────────────────────────────────┐
│  Transaction Posted to Account                                │
│  (System evaluates account tags in precedence order)          │
└────────────────────┬──────────────────────────────────────────┘
                     │
        ┌────────────▼──────────────┐
        │ Precedence 100 (HIGHEST)  │
        │ SCRA Program              │
        │ Tag: tag://SCRAStatus/*   │
        └────────┬───────────────────┘
                 │ NO ▼ YES
                 │  └──→ Apply 6% APR (RULE01/RULE02)
                 │       Rate table: IRTUS000003
                 │
        ┌────────▼──────────────┐
        │ Precedence 200        │
        │ Secured Credit Program│
        │ Tag: cardStatusSecured│
        └────────┬───────────────┘
                 │ NO ▼ YES
                 │  └──→ Retail: 25% APR (RULE_SECURED_RETAIL)
                 │       Cash:   25% + 3% margin (RULE_SECURED_CASH)
                 │       Rate table: SECUREDLOAN1001
                 │
        ┌────────▼──────────────┐
        │ Precedence 300        │
        │ Unsecured Credit      │
        │ Tag: riskLevel=1/2/3  │
        └────────┬───────────────┘
                 │ NO ▼ YES
                 │  └──→ riskLevel=3 → 20% (UNSECUREDLOAN_L3_1001)
                 │       riskLevel=2 → 21% (UNSECUREDLOAN_L2_1001)
                 │       riskLevel=1 → 23% (UNSECUREDLOAN_L1_1001)
                 │
        ┌────────▼──────────────┐
        │ Precedence 900        │
        │ General Interest      │
        │ (Always matches)      │
        └────────┬───────────────┘
                 │
                 ▼
        Retail: 28.99% APR (RULE01)
        Cash:   32.99% APR (RULE02)
        Excess: 0% (RULE03)
        Rate table: VARPRIMEXEE1001
```

---

### Program Summary Table

| Precedence | Program Code | Type | APR | Trigger |
|---|---|---|---|---|
| **100** | SCRAUSXEE1001 | SCRA | 6% | `tag://SCRAStatus/*` |
| **200** | SCPUSXEE1001 | Secured | 25% (retail) / 25%+3% (cash) | `cardStatusSecured` |
| **300** | UCPUSXEE1001 | Unsecured | 20% / 21% / 23% | `riskLevel=1/2/3` |
| **900** | IPGUSXEE100101 | General | 28.99% (retail) / 32.99% (cash) | Always (fallback) |

---

### Real-World Scenarios

**Scenario 1: Military Servicemember (SCRA)**
- Account Tags: `tag://SCRAStatus/LEVEL_1`
- Program Selected: SCRA (`SCRAUSXEE1001`, precedence 100)
- APR: **6%** | Compliance: 50 U.S.C. § 3953

**Scenario 2: FD-Backed Secured Account**
- Account Tags: `cardStatusSecured`
- Program Selected: Secured Credit (`SCPUSXEE1001`, precedence 200)
- APR: **25%** (retail) | Credit Limit = FD amount × 90%

**Scenario 3: Unsecured, Low Risk**
- Account Tags: `riskLevel=3`
- Program Selected: Unsecured Credit (`UCPUSXEE1001`, precedence 300)
- APR: **20%** (safest tier)

**Scenario 4: Unsecured, High Risk**
- Account Tags: `riskLevel=1`
- Program Selected: Unsecured Credit (`UCPUSXEE1001`, precedence 300)
- APR: **23%** (highest tier)

**Scenario 5: No Tags (Unclassified)**
- Account Tags: (none)
- Program Selected: General (`IPGUSXEE100101`, precedence 900)
- APR: **28.99%** (retail) / **32.99%** (cash)

---

## Complete Policy Code Reference

### Ruby — Interest & Term Policies
| Code | Name | Notes |
|------|------|-------|
| `INTUSXEE1001` | Master Interest Policy | Daily balance, monthly compounding |
| `IPGUSXEE100101` | General Interest Program | Fallback — 28.99% retail / 32.99% cash |
| `SCRAUSXEE1001` | SCRA Interest Program | 6% APR legal cap |
| `SCPUSXEE1001` | Secured Credit Program | FD-backed, 25% APR |
| `UCPUSXEE1001` | Unsecured Credit Program | Risk-based 20–23% APR |
| `MIFUSXEE1001` | Minimum Interest Fee Policy | Base fee $1 |
| `TRPUSXEE1001` | Interest Term Resolution Policy | SCRA repricing on tag change |

### Ruby — Fee Policies
| Code | Name | Amount |
|------|------|--------|
| `ANFINXYZ1001` | Annual Fee | $50 |
| `CAFUSXEE1001` | Cash Advance Fee | 5% (min $10) |
| `LPFUSXEE1001` | Late Fee | $41/$30 (first/repeat), max $35/$25 |
| `CRFUSXEE1001` | Card Replacement Fee | $10 |
| `FCFUSXEE1001` | Forex Markup Fee | 0–3% depending on domestic/cross-currency |
| `FCCUSXEE1001` | Forex Advance Fee | 0–8.5% depending on domestic/cross-currency |
| `DPPUSXEE1001` | Dispute Processing Fee | — |
| `JFFUSXEE1001` | Joining Fee | — |

### Ruby — Account Policies
| Code | Name |
|------|------|
| `ACPUSXEE1001` | Account Closure Policy |
| `ANGUSXEE1001` | Account Number Generation |
| `CAPUSXEE1001` | Coupon Accounting Policy |
| `CROUSXEE1001` | Credit Allocation (Payment Distribution) |
| `CODUSXEE1001` | Charge-Off Decision |
| `DLQUSXEE1001` | Delinquency Policy |
| `DORUSXEE1001` | Dormancy Policy |
| `STMUSXEE1001` | Statement Policy |
| `SPLUSXEE7001` | Spend Limit Policy |
| `MADUS002001` | Minimum Amount Due Policy |
| `REPUSXEE1001` | Repayment Policy |

### Ruby — Promo Programs
| Code | Name | Notes |
|------|------|-------|
| `AFPP00003XEE` | Annual Fee Waive-off Program | $0 annual fee for `ZEROANNUALFEE` tag |

### Emerald — Loan Policies
| Code | Name |
|------|------|
| `EIPUS008001` | Emerald Interest Policy |
| `ERPUSXEE1001` | Emerald Repayment Policy |
| `ELCUS008005` | Emerald Loan Conversion Policy |
| `ELCPROGUS005002` | Emerald Loan Conversion Program (smartEMI) |
| `ECPUSXEE1001` | Emerald Account Closure Policy |
| `AMOUSXEE1001` | Amortization Policy (referenced) |

### Athena — Payment Products
| Code | Name |
|------|------|
| `PYPUSXEE1001` | Payment Product |
| `PPDUSXEE1001` | Payment Account Product |
| `RSPUSXEE1001` | Resource Product |
| `FFPUSXEE100102` | Form Factor Product (card) |

### Acropolis — Card Products
| Code | Name |
|------|------|
| `CRPUSXEE100101` | Card Product (BIN 601150, range 23) |
| `SKUUSXEE100101` | Card SKU (plastic code BXEE01) |

---

## Key Product Summary

| Aspect | Configuration |
|--------|---------------|
| **Product Code** | PRDUSXEE1001 |
| **Bundle Code** | BUNUSXEE1001 |
| **Ruby Account Product** | RAPINXYZ1001 |
| **Emerald Loan Product** | EAPUSXEE1001 |
| **Credit Limit (Revolving)** | $200 – $5,000 |
| **Secured Credit Limit** | 90% of FD collateral |
| **Loan Amount (EMI)** | $100 – $2,000 |
| **Loan Tenure** | 3 – 12 months (default 3) |
| **Cash Limit** | 30% of credit limit |
| **APR Range** | 6% (SCRA) / 20–23% (unsecured) / 25% (secured) / 28.99–32.99% (general) |
| **Annual Fee** | $50 (waivable via AFPP00003XEE) |
| **Minimum Interest Fee** | $1 |
| **MAD Minimum** | $25 |
| **Grace Days (Statement)** | 3 |
| **Billing Cycles** | Due 1st / 15th / 28th |
| **Card BIN** | 601150 (range 23) |
| **Card Features** | MAG_STRIPE, EMV, NFC |
| **Channel** | CHNUSZZ1101 |
| **Tenant** | bankxyz (ID: 600326) |
