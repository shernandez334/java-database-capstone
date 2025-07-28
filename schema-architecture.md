# Java Database Capstone: Architecture Overview

## Application Structure

This **Spring Boot** application implements a hybrid architecture that combines both **MVC** and **RESTful** patterns:

- **Admin** and **Doctor dashboards** use `Thymeleaf` for server-side rendering of dynamic HTML views—ideal for structured, role-specific user interfaces.
- All other modules—such as **patient registration**, **login**, and **prescription access**—are built with `REST APIs`, supporting front-end frameworks or third-party integrations.

### Database Design

The system connects to **two specialized databases**, each optimized for different types of data:

- 🟦 **MySQL** handles **structured, relational data**:
  - Entities: `Patient`, `Doctor`, `Appointment`, `Admin`
  - Accessed using `JPA` repositories and entity models

- 🟩 **MongoDB** stores **flexible, unstructured data**:
  - Entity: `Prescription`
  - Accessed via `Spring Data MongoDB` and document-based models

Both databases are abstracted behind a common **service layer**, ensuring clean separation between business logic and data access.

---

## Data & Control Flow

The application's request–response lifecycle can be summarized in seven steps:

1. **User Action**  
   A user (admin, doctor, or patient) triggers an action—either by visiting a web page or making an API call.

2. **Routing**  
   The request is routed to the appropriate controller:
   - `Thymeleaf MVC Controller` (for dashboards)
   - `REST Controller` (for API endpoints)

3. **Delegation**  
   The controller forwards the request to the **service layer**.

4. **Business Logic**  
   The service layer processes the request and determines which repository to interact with.

5. **Relational Data Access**  
   If the request involves structured entities, the service uses **JPA repositories** to access **MySQL**.

6. **Document Data Access**  
   If the request involves prescription data, the service uses **MongoDB repositories** to access **MongoDB**.

7. **Response Handling**  
   The application returns either:
   - A rendered HTML page (`Thymeleaf`)
   - Or a JSON response (`REST`)

---

## Summary

This architecture balances clarity, modularity, and flexibility. By splitting responsibilities across well-defined layers (controllers, services, repositories) and using purpose-specific databases, the application is both scalable and easy to maintain.

---
