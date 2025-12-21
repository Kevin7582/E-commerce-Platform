# DESIGN.md — E-commerce Platform

## 1. Purpose & Scope

This document describes the architectural and design decisions for the E-commerce Platform project.

The project is a **backend-focused e-commerce application** built to practice and demonstrate core backend engineering concepts such as product management, cart handling, checkout logic, order creation, and basic inventory tracking. The emphasis is on correctness, maintainability, and clear separation of concerns rather than UI or visual polish.

### In scope (v1)
- Product catalog management
- Shopping cart functionality
- Checkout and order creation
- Basic inventory tracking and validation
- REST API design
- Relational database usage with transactions

### Out of scope (v1)
- Frontend/UI development
- Real payment provider integration
- Complex shipping logic
- Multi-warehouse inventory
- Advanced scalability mechanisms (caching, messaging)

---

## 2. High-Level Architecture

The application is implemented as a **layered monolithic backend** using Spring Boot.

**Architecture overview:**

Client (API consumer)  
→ REST Controllers  
→ Service Layer (business logic)  
→ Repository Layer (Spring Data JPA)  
→ PostgreSQL Database  

This structure was chosen to:
- Keep the codebase simple and easy to reason about
- Encourage separation of concerns
- Make business logic testable
- Reflect common enterprise backend architectures

---

## 3. Core Domains

The system is divided into a small number of clearly defined domains.

### Product / Catalog
Responsible for managing products available for purchase.
- Product creation and updates (admin)
- Product listing and retrieval (public)
- Product availability flag (active/inactive)

### Cart
Represents a user’s temporary selection of products prior to checkout.
- Add items to cart
- Update quantities
- Remove items
- Compute cart totals

### Orders
Represents completed purchase attempts.
- Order creation during checkout
- Immutable order history
- Order status tracking

### Inventory
Responsible for tracking available stock for products.
- Validate stock availability during checkout
- Decrement stock when an order is placed
- Prevent negative inventory values

### Payments (Stubbed in v1)
Represents payment records associated with orders.
- In v1, payments are simulated or stubbed
- The goal is to model the flow, not real payment processing

---

## 4. Data Model (Conceptual)

The following entities describe the core data model. Exact schemas may evolve during implementation.

### Product
- id
- name
- description
- price
- active
- createdAt
- updatedAt

### Inventory
- productId
- quantity
- updatedAt

Inventory is intentionally simple in v1 and assumes a single stock source per product.

### Cart
- id
- userId
- createdAt
- updatedAt

### CartItem
- cartId
- productId
- quantity

### Order
- id
- userId
- status
- totalAmount
- createdAt

### OrderItem
- orderId
- productId
- quantity
- unitPriceSnapshot

**Design decision:**  
Prices are snapshotted in `OrderItem` to ensure order history remains correct even if product prices change later.

### Payment (Stub)
- id
- orderId
- status
- amount
- createdAt

---

## 5. Checkout Flow (Core Logic)

Checkout is the most critical workflow in the system.

### Checkout process
1. Validate that the cart exists and is not empty
2. Load cart items and associated products
3. Validate product availability and quantities
4. Check inventory for each product
5. Create an order and order items with snapshotted prices
6. Decrement inventory quantities
7. Create a payment record (stubbed)
8. Finalize order status
9. Clear or close the cart

### Transactional guarantees
Steps involving order creation and inventory updates are executed within a **single database transaction** to prevent partial updates and inconsistent state.

If any validation fails, the checkout operation is rolled back.

---

## 6. Consistency & Concurrency Considerations

Although this is a learning project, basic consistency rules are enforced:

- Inventory quantities must never become negative
- Only one checkout should succeed if multiple users attempt to purchase the last available unit of a product
- Order totals must remain immutable once created

The initial approach relies on database transactions and row-level locking mechanisms provided by PostgreSQL and JPA.

---

## 7. API Design Overview

The backend exposes a REST API.

Examples of core endpoints:
- `GET /products`
- `GET /products/{id}`
- `GET /cart`
- `POST /cart/items`
- `POST /checkout`
- `GET /orders`
- `GET /orders/{id}`

API documentation is generated using OpenAPI / Swagger.

---

## 8. Security (Planned)

Security is not the primary focus of v1.

Planned future work includes:
- User authentication
- Role-based authorization (USER vs ADMIN)
- Protection of admin-only endpoints

---

## 9. Testing Strategy

Testing focuses on correctness of business logic.

- Unit tests for cart totals and order calculations
- Integration tests for checkout and inventory updates
- Validation tests for insufficient stock and invalid input

---

## 10. Future Improvements

Potential enhancements for future versions:
- Real payment provider integration
- Refunds and order cancellation
- Integration testing with Testcontainers
- Caching with Redis
- Asynchronous processing using a message queue
