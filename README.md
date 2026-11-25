# Smart Supermarket Inventory and Checkout System

A unified supermarket management platform concept for coordinating inventory, store operations, checkout, online orders, deliveries, and reporting across one or more branches. The system is designed to track products throughout their lifecycle—from receiving and storage to shelf replenishment, sale, return, or transfer—using QR codes and shared, up-to-date operational records.

Customers can scan products for self-checkout or shop with a cashier. Managers and staff use role-appropriate tools to monitor stock, process orders, coordinate deliveries, and understand sales activity.

> **Project status:** This repository currently contains the product description and intended scope. Application code, technology choices, deployment instructions, and implemented feature status have not yet been added. The capabilities below describe the proposed system, not a claim that they are already available.

## Contents

- [Goals](#goals)
- [Users and roles](#users-and-roles)
- [Functional areas](#functional-areas)
- [Core workflows](#core-workflows)
- [Operational principles](#operational-principles)
- [Reports and decision support](#reports-and-decision-support)
- [Multi-branch operation](#multi-branch-operation)
- [Security and accountability](#security-and-accountability)
- [Getting started](#getting-started)
- [Planned implementation documentation](#planned-implementation-documentation)

## Goals

The proposed system aims to:

- Keep a reliable view of stock across sales floors, storerooms, and branches.
- Connect purchasing, receiving, internal stock movement, checkout, and online fulfilment.
- Reduce manual data entry and make product handling easier to trace with QR codes.
- Give staff timely information about low stock, upcoming expiry, and work awaiting action.
- Offer customers convenient checkout choices, including self-checkout, cashier checkout, pickup, and delivery.
- Give managers a consolidated view of operations and business performance.

## Users and roles

Role-based access is intended to give each person the tools needed for their work while limiting access to unrelated administrative functions. An administrator can configure accounts and permissions; managers oversee operations; operational staff complete assigned tasks.

| Role | Typical responsibilities |
| --- | --- |
| **Administrator** | Manage user accounts, roles, system-wide settings, and branch access. |
| **Store manager** | Oversee branch operations, inventory, purchasing, orders, staff activity, and reports. |
| **Stock keeper** | Receive and count goods, label or scan products, move stock, replenish shelves, and review stock alerts. |
| **Cashier** | Scan products at the point of sale, review a customer's basket, accept supported payment outcomes, and complete checkout. |
| **Delivery staff** | View assigned deliveries, update fulfilment progress, and record delivery completion or an exception. |
| **Customer** | Browse products, scan items for self-checkout, place online orders, and choose pickup or delivery where available. |

Permissions should be configurable according to role and, where applicable, the user's assigned branch. Sensitive actions—such as changing stock adjustments, cancelling transactions, or managing users—should be limited to authorized roles and recorded for review.

## Functional areas

### Product catalog and QR tracking

Each product has a catalog record containing identifying and operational information such as its name, SKU or barcode, category, selling price, and applicable stock or expiry details. A QR code can identify the product or a specific tracked unit or batch, depending on the eventual implementation.

Scanning is intended to support receiving, stock counts, shelf replenishment, transfers, checkout, and other authorized stock movements. Recording each movement with its source, destination, quantity, time, and responsible user makes it easier to reconcile inventory and investigate discrepancies. QR codes supplement—not replace—appropriate controls for validating prices, quantities, and transaction status.

### Inventory and stock locations

Stock is tracked by branch and location, including storerooms and shelves. Staff should be able to inspect available quantities, record receipts and adjustments, and transfer goods between locations. Stock changes associated with a sale or fulfilment should be reflected in the relevant inventory records so staff can make decisions using current information.

### Low-stock and expiry alerts

Managers can set or review replenishment thresholds and receive alerts when a product is running low. Where expiry dates or batch information are captured, the system can highlight stock approaching its expiry date so staff can inspect it and take appropriate action. Alerts should identify the product, affected branch or location, relevant quantity or date, and any follow-up needed.

### Suppliers and purchasing

Supplier records provide a place to maintain supplier contact and purchasing information. Staff can create purchase orders, track their status, and compare expected goods with incoming deliveries. On receipt, the quantity accepted, discrepancies, and relevant product or batch details can be recorded, updating stock and preserving a link to the purchase order.

### Checkout and sales

The system supports two checkout paths:

- **Self-checkout:** Customers scan products into a digital basket, review items and totals, and complete payment through an enabled digital payment method.
- **Cashier point of sale (POS):** A cashier scans products for a customer, checks the basket, and completes the transaction using an enabled payment option.

A completed sale should create a transaction record with its items, quantities, prices, payment outcome, timestamp, and branch. The system should clearly distinguish a completed/paid transaction from a basket or payment that is still pending or has failed.

### Payment integrations

The intended payment options include Telebirr, Chapa, and other locally supported providers, subject to provider availability and integration. Payment confirmation should come from a trusted provider response or an authorized cashier workflow; a customer-facing success screen alone should not be treated as proof of payment. Payment credentials and secrets must be handled through secure configuration rather than committed to source control.

### Exit-gate receipt verification

After a successful purchase, the customer receives a QR-coded receipt or transaction token. At the exit, an authorized gate attendant or verification device can scan it and check that it corresponds to a valid, paid transaction. Verification should report whether the receipt is valid and already used or expired, where such controls are implemented, and should avoid exposing unnecessary payment or personal data.

### Online orders, picking, and fulfilment

Customers can place orders for available products and choose an eligible fulfilment option:

- **Store pickup:** staff prepare the order for collection at the selected branch.
- **Home delivery:** the order is prepared for delivery to the customer's provided destination, subject to service coverage.

Confirmed orders feed an order-preparation queue with picking lists for staff. Staff can record picked quantities and substitutions or unavailable items according to store policy. Order statuses should make progress understandable—for example, placed, confirmed, preparing, ready for pickup, out for delivery, completed, or cancelled—and stock allocation must be reconciled with the selected order workflow.

### Delivery management

Managers or dispatch staff can assign delivery orders to delivery staff. Assigned staff can view the information needed to complete each delivery and update its progress. Failed delivery attempts, customer unavailability, and other exceptions should be recorded rather than silently marking an order as complete.

### Demand prediction and replenishment support

Sales history can be used to estimate demand and suggest reorder quantities. These suggestions are decision support: managers should be able to review them alongside current stock, supplier lead times, upcoming expiry, and local conditions before creating or approving a purchase order. Predictions should not silently place orders or be presented as guaranteed outcomes.

## Core workflows

### 1. Receiving a supplier delivery

1. A staff member opens the relevant purchase order and checks the incoming delivery.
2. Products and quantities are scanned or entered; discrepancies are recorded for review.
3. Accepted stock is associated with its branch and storage location, with batch or expiry information captured where applicable.
4. Inventory levels are updated and the purchase order's receiving progress is recorded.

### 2. Moving stock and replenishing shelves

1. An authorized staff member selects the source and destination locations.
2. The staff member scans the relevant product or tracked stock and records the quantity moved.
3. The movement is recorded against both locations so stock remains traceable.
4. Updated quantities and any resulting low-stock alerts are made available to staff.

### 3. Completing a customer purchase

1. Products are scanned into a self-checkout basket or by a cashier at the POS.
2. The customer or cashier reviews the items, quantities, and total before confirming.
3. Payment is processed through an enabled method and its outcome is recorded.
4. On successful payment, the sale is completed, stock is updated, and a receipt with a verification QR code is issued.
5. At the exit, the receipt QR code can be checked against the paid transaction.

### 4. Preparing and fulfilling an online order

1. A customer submits an order and selects an available pickup or delivery option.
2. The order appears in the preparation queue with a picking list for the responsible branch.
3. Staff pick and confirm items, recording any shortages or approved substitutions.
4. A pickup order is marked ready for collection, or a delivery order is assigned and tracked through delivery.
5. Completion or exceptions are recorded so customers and staff can see the order's final status.

### 5. Responding to stock and expiry alerts

1. The system highlights an item below its configured stock threshold or nearing its recorded expiry date.
2. The responsible staff member checks the affected branch and location.
3. The team replenishes, transfers, counts, or otherwise handles the stock according to store policy.
4. The resulting stock movement or action is recorded, keeping the alert and inventory view actionable.

## Operational principles

The intended design emphasizes the following:

- **Consistent stock records:** Every relevant receipt, sale, transfer, adjustment, and fulfilment should have a clear effect on inventory.
- **Traceable actions:** Important operations should identify who performed them, when they occurred, and which branch or location they affected.
- **Explicit transaction states:** Pending, failed, cancelled, and completed actions should not be confused with one another.
- **Human review for decisions:** Reorder suggestions, substitutions, and exceptions should follow configurable business rules and staff approval where appropriate.
- **Operational visibility:** Staff should be able to find alerts, unfulfilled orders, and outstanding receiving or delivery work without relying on informal messages.

## Reports and decision support

The planned reporting area can help managers review:

- Daily sales totals and transaction activity.
- Best-selling products and sales trends.
- Slow-moving products and stock on hand.
- Low-stock items and upcoming expiry dates.
- Purchasing and incoming-delivery progress.
- Online order preparation, pickup, and delivery status.
- Profit-related summaries, when reliable cost and pricing data is available.

Reports should be filterable by date range and branch where appropriate. Financial and profit figures depend on accurate product costs, transaction records, and business rules; they should not be inferred where those inputs are missing.

## Multi-branch operation

The system is intended to support multiple supermarket branches from a shared management view. Each branch maintains its own locations, stock movements, sales, orders, and staff assignments, while authorized managers can compare or review information across branches. Transfers between branches should be recorded as linked outgoing and incoming movements so that goods remain accounted for while in transit.

## Security and accountability

A production implementation should use authenticated accounts, role- and branch-aware authorization, secure handling of payment-provider integrations, and audit records for important operational actions. Customer, employee, and payment-related data should be limited to what is needed for the workflow and protected according to applicable laws and organizational policies. QR codes should be validated against server-side transaction or inventory records rather than trusted solely because they can be scanned.

## Getting started

There is no application to install or run in this repository yet. No language, framework, dependency manager, database, or deployment process has been selected or documented. Setup instructions will be added when the implementation and its tooling are established.

## Planned implementation documentation

As development progresses, this README or linked documentation should be updated with:

- The selected technology stack and system architecture.
- Prerequisites, installation, environment configuration, and local development commands.
- Database setup, migrations, and any seed or demo data.
- Payment-provider and QR-code integration configuration, without publishing secrets.
- Tests, deployment steps, and operational monitoring guidance.
- A feature-status checklist that distinguishes implemented capabilities from planned work.
