# Roles & Access Matrix

This document defines all user roles in CatalogHub and the exact features, pages, and API endpoints each role can access.  
It ensures consistent implementation across backend (Spring Security), frontend (Angular route guards), and API design.

---

## User Roles

### 1. Guest (Unauthenticated User)
A non‑logged‑in visitor browsing the application.

### 2. Customer (Authenticated User)
A registered user who can manage their profile and addresses.

### 3. Admin (Authenticated User with elevated privileges)
Responsible for managing products and performing administrative actions.

---

## Access Matrix (Feature-Level)

| Feature / Page                  | Guest | Customer | Admin |
|---------------------------------|:-----:|:--------:|:-----:|
| View product list               | ✅    | ✅       | ✅    |
| View product details            | ✅    | ✅       | ✅    |
| Register                        | ✅    | ❌       | ❌    |
| Login                           | ✅    | ✅       | ✅    |
| View profile                    | ❌    | ✅       | ✅    |
| Manage addresses                | ❌    | ✅       | ✅    |
| View admin product list         | ❌    | ❌       | ✅    |
| Add product                     | ❌    | ❌       | ✅    |
| Edit product                    | ❌    | ❌       | ✅    |
| Delete product                  | ❌    | ❌       | ✅    |

---

## Access Matrix (API-Level)

### **Public APIs (No auth required)**  
| Endpoint                        | Method | Access |
|---------------------------------|--------|--------|
| `/api/auth/register`           | POST   | Guest  |
| `/api/auth/login`              | POST   | Guest  |
| `/api/products`                | GET    | Guest/Customer/Admin |
| `/api/products/{id}`           | GET    | Guest/Customer/Admin |

---

### **Customer APIs (Requires JWT)**  
| Endpoint                                    | Method  | Access |
|---------------------------------------------|---------|--------|
| `/api/users/me`                             | GET     | Customer/Admin |
| `/api/users/me/addresses`                   | GET     | Customer/Admin |
| `/api/users/me/addresses`                   | POST    | Customer/Admin |
| `/api/users/me/addresses/{id}`              | PUT     | Customer/Admin |
| `/api/users/me/addresses/{id}`              | DELETE  | Customer/Admin |

---

### **Admin APIs (Requires JWT + ADMIN role)**  
| Endpoint                          | Method   | Access |
|----------------------------------|----------|--------|
| `/api/admin/products`            | GET      | Admin |
| `/api/admin/products`            | POST     | Admin |
| `/api/admin/products/{id}`       | PUT      | Admin |
| `/api/admin/products/{id}`       | DELETE   | Admin |

---

## Access Rules Summary

### Guest
- Can browse products, view product details.
- Can register & login.
- Cannot access customer/admin features.

### Customer
- Can do everything guest can.
- Can manage profile & addresses.
- Cannot access admin product management.

### Admin
- Full access to all features.
- Can manage products.
- Can also act as a customer for profile/address.

---

## Expected Implementation Mapping

### Backend (Spring Security)
- Roles: `ROLE_GUEST` (implicit), `ROLE_CUSTOMER`, `ROLE_ADMIN`
- JWT required for Customer/Admin endpoints
- Method-level or URL-level access control

### Frontend (Angular)
- Route Guards:
  - `authGuard` → for Customer/Admin pages
  - `adminGuard` → for admin-only pages
- Navbar/menu visibility based on roles

### Database
- `User` ↔ `Role` (many-to-many or many-to-one depending on simplicity)
- Store roles as enums or separate table

---

## Notes
- Admin inherits all customer abilities.
- Only Admin can perform product CRUD.
- Access rules must be enforced on both **frontend and backend**.