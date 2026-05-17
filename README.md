# FarmLink

> **"Your Partner in Farm-to-Market Success"**

FarmLink is a full-stack PHP web platform that connects **farmers**, **buyers**, **agricultural consultants**, and **delivery persons** in a single marketplace. Built as a University of Colombo School of Computing (UCSC) second-year group project, it covers the entire farm-to-doorstep workflow: product listing, cart & checkout, distance-based delivery fee calculation, real-time notifications, consultant booking, a Q&A forum, and a multi-panel admin dashboard.

---

## Project Overview

| Aspect | Detail |
|---|---|
| Type | Multi-role web application |
| Language | PHP (custom MVC) |
| Database | MySQL |
| Payment | PayHere |
| Maps | Google Maps |
| Email | PHPMailer + SMTP |
| Auth | PHP Sessions + `password_hash` / `password_verify` |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | PHP |
| Database | MySQL |
| ORM / DB Layer | Custom PDO wrapper (`Database.php`) |
| Frontend | HTML5, CSS3, JavaScript (vanilla) |
| Email | PHPMailer  |
| Payment Gateway | PayHere  |
| Distance API | Google Maps  |

---

## Architecture

FarmLink uses a **custom PHP MVC framework built entirely from scratch** 

```
public/
  index.php          ← Single entry point, boots Core
  .htaccess          ← Rewrites all requests to index.php

app/
  bootstrap.php      ← Loads config, helpers, autoloader
  config/
    config.php       ← DB constants, APPROOT, URLROOT (not in repo)
    .env             ← SMTP credentials (not in repo)
  libraries/
    Core.php         ← URL router: /controller/method/params
    Controller.php   ← Base controller: loads models & views
    Database.php     ← PDO wrapper (query/bind/execute/single/resultSet)
  controllers/       ← One class per user role / feature
  models/            ← One class per database entity
  views/             ← PHP templates per role
  helpers/
    session_helper.php      ← isLoggedIn(), isAdmin(), redirect()
    notification_helper.php ← NotificationHelper class, getUserType()
    flash_message.php       ← flash() helper
    url_helper.php          ← URL utilities
    mailer_config.php       ← PHPMailer SMTP setup
```


## User Roles

| Role | Registration | Status Flow | Key Capabilities |
|---|---|---|---|
| **Farmer** | Self-register (ID card required) | `pending` → `approved` (by admin) | List products, manage stock, view orders & sales, book consultants, forum Q&A |
| **Buyer** | Self-register | `approved` immediately | Browse/filter products, cart, wishlist, PayHere checkout, order tracking, reviews, complaints |
| **Consultant** | Self-register (verification doc required) | `pending` → `approved` | Set availability, manage appointments, post forum answers, receive ratings |
| **Delivery Person** | Self-register (vehicle + license) | `pending` → `approved` | Claim orders by area, upload pickup/dropoff photos, track deliveries, view revenue |
| **Admin** | Pre-seeded | — | Full dashboard, user management, order oversight, complaint resolution, revenue reports |

---

## Setup Instructions

### Prerequisites

- PHP 7.4+
- MySQL 5.7+ / MariaDB 10+
- Apache with `mod_rewrite` enabled
- Composer

### 1. Clone the repository

```bash
git clone https://github.com/your-username/farmlink.git
cd farmlink
```

### 2. Install PHP dependencies

```bash
composer install
```

### 3. Create the database configuration file

Create `app/config/config.php` (this file is git-ignored):

```php
<?php
define('DB_HOST', 'localhost');
define('DB_USER', 'your_db_user');
define('DB_PASS', 'your_db_password');
define('DB_NAME', 'farmlink');

define('APPROOT', dirname(dirname(__FILE__)));
define('URLROOT', 'http://localhost/farmlink');
define('SITENAME', 'FarmLink');
```

### 4. Create the environment file

Create `app/config/.env` (this file is git-ignored):

```env
MAIL_HOST=smtp.gmail.com
MAIL_USERNAME=your_email@gmail.com
MAIL_PASSWORD=your_app_password
MAIL_PORT=587
```

### 5. Import the database

Import the SQL schema into your MySQL server:

```bash
mysql -u root -p farmlink < database/farmlink.sql
```

### 6. Configure Apache virtual host

```apache
<VirtualHost *:80>
    DocumentRoot "/path/to/farmlink/public"
    ServerName localhost
    <Directory "/path/to/farmlink/public">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Or place the project in your XAMPP/WAMP `htdocs` folder and access via `http://localhost/farmlink`.

### 7. Create upload directories

```bash
mkdir -p public/uploads/farmer/profile
mkdir -p public/uploads/farmer/products
mkdir -p public/uploads/farmer/id_cards
mkdir -p public/uploads/consultants
mkdir -p public/uploads/consultants/docs
mkdir -p public/uploads/consultants/verifications
mkdir -p public/d_uploads
```

### 8. Access the application

Open `http://localhost/farmlink` in your browser.

---

## Environment Variables

### `app/config/config.php` (PHP constants)

| Constant | Description | Example |
|---|---|---|
| `DB_HOST` | MySQL host | `localhost` |
| `DB_USER` | MySQL username | `root` |
| `DB_PASS` | MySQL password | `secret` |
| `DB_NAME` | Database name | `farmlink` |
| `APPROOT` | Absolute path to `app/` directory | auto-computed |
| `URLROOT` | Base URL of the application | `http://localhost/farmlink` |
| `SITENAME` | Application display name | `FarmLink` |

### `app/config/.env` (SMTP / Email)

| Variable | Description | Example |
|---|---|---|
| `MAIL_HOST` | SMTP server hostname | `smtp.gmail.com` |
| `MAIL_USERNAME` | SMTP login email | `you@gmail.com` |
| `MAIL_PASSWORD` | SMTP password or app password | `abcd1234` |
| `MAIL_PORT` | SMTP port | `587` |

> **Note:** Both files are excluded from version control via `.gitignore`.

---

## Key Features

### For Farmers
- Self-registration with NIC/ID card upload for identity verification
- Product/stock management with expiry tracking (stocks auto-archive when expired)
- Dashboard with total sales, monthly trend, pending orders, expiring stock alerts
- Wishlist-based buyer notifications — when a new stock matching a wishlisted product is added, relevant buyers are automatically notified
- Consultant booking and Q&A forum access
- Sales analytics with month-over-month percentage change

### For Buyers
- Product browsing with real-time search, category filter, and price/stock/expiry sorting
- Single-item cart with stock validation
- Address-based delivery fee calculation using **Google Maps Distance Matrix API** (base fee LKR 200 + LKR 10/km)
- **PayHere** payment gateway integration (LKR)
- Wishlist with availability notifications
- Post-purchase star ratings and photo reviews
- Order complaint submission

### For Consultants
- Profile page with posts (text + file attachments), star ratings, and public availability calendar
- Calendar-based availability management
- Appointment accept / decline / cancel with automatic notifications
- Forum answer management (create, edit, delete)

### For Delivery Persons
- Area-based order claiming — only see orders in their registered delivery zone
- Photo proof upload for pickup and dropoff
- Live tracking view showing pickup/dropoff addresses and delivery fee
- Revenue dashboard with monthly/yearly earnings charts and filterable transaction history

### For Admins
- Unified user management across all 5 roles
- Order management with status and date-range filtering
- Complaint resolution with fault assignment (farmer / delivery person)
- Revenue reports showing farmer fees and delivery fees per user
- Sales analytics: by location (city), by product category, top-selling products

### Notifications System
- Role-specific notification tables (`notify_farmer`, `notify_buyer`, `notify_dperson`, `notify_consultant`, `notify_admin`)
- Triggered on: new order, order ready, order picked up, order delivered, new complaint, appointment booked/accepted/declined/cancelled, new stock of wishlisted product, forum answer
- Frontend polling via JavaScript + JSON API (`notifications/getNotifications`)
- Mark as read / dismiss per notification or all at once
