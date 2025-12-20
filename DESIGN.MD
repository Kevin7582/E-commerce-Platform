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
