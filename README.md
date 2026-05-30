# UrbanMart

Marketplace-style e-commerce platform built with Laravel REST API and React. The application supports multi-role users, payment gateway integration, product search, order management, and automated wallet settlement workflow. (Inspired by ruparupa.com)

## Overview

UrbanMart was built to simulate a real-world marketplace where buyers can purchase products from multiple sellers while the platform manages payment processing and settlement workflows.

Unlike a traditional online store, marketplace systems introduce additional complexity such as payment verification, seller payouts, role-based permissions, and asynchronous event handling.

This project was created to explore those engineering challenges using a modern Laravel + React architecture.

---

## Problem

Building a marketplace is more complex than building a standard e-commerce website.

Several technical challenges must be addressed:

* Managing multiple user roles (Buyer, Seller, Admin)
* Processing payments securely
* Handling payment gateway callbacks
* Preventing immediate seller withdrawals
* Providing fast product search
* Maintaining data consistency across transactions

---

## Solution

UrbanMart uses a separated frontend-backend architecture:

* React for the frontend
* Laravel REST API for backend services
* PostgreSQL for transactional data
* Redis for caching and queue-related operations
* Typesense for search functionality
* Midtrans for payment processing

The system automatically handles payment verification through webhooks and schedules delayed seller payouts after successful order completion.

---

## Project Statistics

- 30+ database tables
- 50+ API endpoints
- 3 user roles
- Payment gateway integration
- Full-text search integration
- Queue-based background processing

---

## Key Features

### Authentication & Authorization

* Buyer accounts
* Seller accounts
* Administrator accounts
* Laravel Sanctum authentication
* Role-based access control

### Product Management

* Product creation
* Product editing
* Product categorization
* Product inventory tracking

### Marketplace Orders

* Shopping cart
* Checkout process
* Order tracking
* Multi-seller transactions

### Payment Integration

* Midtrans payment gateway integration
* Webhook processing
* Payment status synchronization
* Transaction verification

### Seller Settlement

* Delayed seller payout workflow
* Scheduled payout processing
* Automated settlement execution

### Search System

* Typesense-powered product search
* Keyword search
* Filtering
* Sorting

---

## System Architecture

React Frontend

↓
        
Laravel REST API

   ├── PostgreSQL
   
   ├── Redis
   
   ├── Typesense
   
   └── Midtrans

---

## Database Design

The database was designed around marketplace transaction workflows rather than a traditional online store.

### Core Domains

#### User Management

* Users
* Wallets
* Wallet Transactions
* Addresses

Supports multiple user roles:

* Buyer
* Partner (Seller)
* Admin

Each user can manage multiple shipping addresses and maintain a wallet balance for settlement transactions.

---

#### Product Catalog

* Products
* Product Images
* Categories

Features:

* Hierarchical categories
* Product ownership by partner
* Inventory management
* Product search indexing

---

#### Checkout System

* Carts
* Cart Items
* Checkout Sessions
* Checkout Items

A dedicated checkout session layer was introduced to preserve checkout state and support both cart-based and buy-now purchase flows.

---

#### Order Management

* Orders
* Order Items
* Order Shipments

Orders are separated from shipments to support marketplace scenarios where products may belong to different partners.

This design enables:

* Multi-seller orders
* Independent shipment tracking
* Per-partner fulfillment workflows

---

#### Payment Processing

* Order Payments

Stores:

* Midtrans transaction references
* Payment status
* Payment method
* Webhook payloads
* Payment expiration data

---

#### Marketplace Settlement

* Order Payouts
* Wallets
* Wallet Transactions

Instead of immediately crediting seller balances after payment, the platform temporarily holds transaction funds.

Funds are released to the seller's internal wallet when:

The buyer confirms the order has been received, or
Three days have passed after delivery without any dispute.

This settlement workflow simulates how marketplace platforms delay seller settlements to provide buyer protection and dispute handling periods.

Workflow:

Buyer Payment

↓

Platform Holding Balance

↓

Order Delivered

↓

Confirmation Period

↓

Scheduled Settlement

↓

Partner Wallet

---

### Internal Wallet Settlement

The platform includes an internal wallet system for sellers.

After a successful order:

1. Customer payment is received by the platform.
2. Funds are temporarily held.
3. The order enters a confirmation period.
4. Settlement is processed automatically.
5. Seller balance is credited to their internal wallet.
6. Wallet transactions are recorded for auditing purposes.

This workflow was implemented to simulate marketplace-style settlement processes while maintaining transaction traceability through wallet ledgers.
This implementation simulates marketplace settlement behavior and is not intended to serve as a regulated escrow service.

---

#### Shipping & Logistics

* Shipping Services
* Provinces
* Cities
* Districts
* Urban Villages

Supports shipping cost calculation and Indonesian regional address management.

---

## Payment Flow

1. Buyer places an order
2. System creates Midtrans transaction
3. Buyer completes payment
4. Midtrans sends webhook notification
5. Backend verifies payment status
6. Order status is updated
7. Seller payout is scheduled
8. Scheduler processes payout after settlement period

---

## Technical Challenges

### Webhook Reliability

Challenge:
Payment notifications arrive asynchronously.

Solution:
Webhook validation and idempotent processing were implemented to avoid duplicate updates.

### Search Performance

Challenge:
Database search becomes slower as product volume grows.

Solution:
Integrated Typesense search engine for faster indexing and filtering.

### Marketplace Settlement Logic

Challenge:
Sellers should not receive funds immediately.

Solution:
Implemented delayed payout scheduling using Laravel Scheduler.

---

## Engineering Decisions

### Why PostgreSQL?

PostgreSQL was chosen because it provides strong relational modeling capabilities, advanced indexing features, and better support for complex marketplace transactions.

### Why Typesense Instead of Database Search?

As product volume grows, SQL LIKE queries become inefficient. Typesense provides dedicated full-text search capabilities with filtering and sorting support.

### Why Separate Checkout Sessions From Orders?

Checkout sessions allow temporary purchase states to exist before an order is officially created, reducing inconsistencies during payment processing.

### Why Internal Wallets?

Instead of directly crediting sellers after payment, an internal wallet system was introduced to simulate marketplace settlement workflows and maintain transaction history through wallet ledgers.

---

## Lessons Learned

Through this project I gained practical experience with:

* REST API architecture
* Payment gateway integration
* Webhook handling
* Search engine integration
* Marketplace workflows
* Queue and scheduling concepts
* Database design
* Dockerized development environments

---

## Technology Stack

### Backend

* Laravel
* PHP

### Frontend

* React
* TypeScript

### Database

* PostgreSQL
* Redis

### Search

* Typesense

### Infrastructure

* Docker

### Payments

* Midtrans

---

## Screenshots

(Add screenshots here)

---

## Local Development Setup

### Prerequisites

* Docker Desktop
* Ngrok
* Git

### Installation

```bash
git clone <repository-url>
cd urbanmart

docker compose build
docker compose up
```

Application:

Frontend:
http://localhost:5173

Backend:
http://127.0.0.1
