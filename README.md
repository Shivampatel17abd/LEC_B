# 🌍 Localink – Backend (Microservices)

> Hyperlocal community platform backend — modular, scalable, and beginner-friendly microservices architecture.

---

## 🏗️ Architecture Overview

Localink backend follows a **Microservices Architecture** where each feature domain is an independent service with its own routes, logic, and database connection.

```
                        ┌─────────────────┐
                        │   API Gateway   │  ← Single entry point (port 8000)
                        │   (port 8000)   │
                        └────────┬────────┘
                                 │
          ┌──────────────────────┼──────────────────────┐
          │              │       │        │              │
    ┌─────▼─────┐  ┌─────▼──┐  ┌▼──────┐ ┌▼──────────┐ ┌▼──────────────┐
    │   Auth    │  │Listing │  │ Shop  │ │ Community │ │  Notification  │
    │ Service   │  │Service │  │Service│ │  Service  │ │    Service     │
    │ :3001     │  │ :3002  │  │ :3003 │ │  :3004    │ │    :3005       │
    └───────────┘  └────────┘  └───────┘ └───────────┘ └───────────────┘

    ┌──────────────┐  ┌───────────────┐  ┌───────────────┐
    │  Emergency   │  │  Jobs/Events  │  │    Search     │
    │   Service    │  │    Service    │  │    Service    │
    │    :3006     │  │    :3007      │  │    :3008      │
    └──────────────┘  └───────────────┘  └───────────────┘

                        ┌─────────────────┐
                        │    MongoDB      │
                        │  (shared DB or  │
                        │  per-service)   │
                        └─────────────────┘
```

---

## 📁 Project Structure

```
localink-backend/
│
├── api-gateway/                  # Routes all requests to correct service
│   ├── src/
│   │   ├── index.js              # Entry point
│   │   └── proxy.js              # http-proxy-middleware config
│   ├── .env
│   └── package.json
│
├── services/
│   │
│   ├── auth-service/             # Register, Login, JWT, Firebase Auth
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── db.js
│   │   │   │   └── firebase.js
│   │   │   ├── controllers/
│   │   │   │   └── authController.js
│   │   │   ├── middlewares/
│   │   │   │   └── authMiddleware.js
│   │   │   ├── models/
│   │   │   │   └── User.js
│   │   │   ├── routes/
│   │   │   │   └── authRoutes.js
│   │   │   └── index.js
│   │   ├── .env
│   │   └── package.json
│   │
│   ├── listing-service/          # Buy / Sell / Exchange / Free / Borrow listings
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── db.js
│   │   │   │   └── cloudinary.js
│   │   │   ├── controllers/
│   │   │   │   └── listingController.js
│   │   │   ├── middlewares/
│   │   │   │   ├── authMiddleware.js
│   │   │   │   └── uploadMiddleware.js
│   │   │   ├── models/
│   │   │   │   └── Listing.js
│   │   │   ├── routes/
│   │   │   │   └── listingRoutes.js
│   │   │   └── index.js
│   │   ├── .env
│   │   └── package.json
│   │
│   ├── shop-service/             # Shop profiles + live inventory
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── db.js
│   │   │   │   └── cloudinary.js
│   │   │   ├── controllers/
│   │   │   │   └── shopController.js
│   │   │   ├── middlewares/
│   │   │   │   ├── authMiddleware.js
│   │   │   │   └── uploadMiddleware.js
│   │   │   ├── models/
│   │   │   │   ├── Shop.js
│   │   │   │   └── Inventory.js
│   │   │   ├── routes/
│   │   │   │   └── shopRoutes.js
│   │   │   └── index.js
│   │   ├── .env
│   │   └── package.json
│   │
│   ├── community-service/        # Groups, Polls, Lost & Found
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   └── db.js
│   │   │   ├── controllers/
│   │   │   │   ├── groupController.js
│   │   │   │   ├── pollController.js
│   │   │   │   └── lostFoundController.js
│   │   │   ├── middlewares/
│   │   │   │   └── authMiddleware.js
│   │   │   ├── models/
│   │   │   │   ├── Group.js
│   │   │   │   ├── Poll.js
│   │   │   │   └── LostFound.js
│   │   │   ├── routes/
│   │   │   │   ├── groupRoutes.js
│   │   │   │   ├── pollRoutes.js
│   │   │   │   └── lostFoundRoutes.js
│   │   │   └── index.js
│   │   ├── .env
│   │   └── package.json
│   │
│   ├── emergency-service/        # SOS / Emergency requests
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   └── db.js
│   │   │   ├── controllers/
│   │   │   │   └── emergencyController.js
│   │   │   ├── middlewares/
│   │   │   │   └── authMiddleware.js
│   │   │   ├── models/
│   │   │   │   └── Emergency.js
│   │   │   ├── routes/
│   │   │   │   └── emergencyRoutes.js
│   │   │   └── index.js
│   │   ├── .env
│   │   └── package.json
│   │
│   ├── jobs-events-service/      # Local jobs + community events
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   └── db.js
│   │   │   ├── controllers/
│   │   │   │   ├── jobController.js
│   │   │   │   └── eventController.js
│   │   │   ├── middlewares/
│   │   │   │   └── authMiddleware.js
│   │   │   ├── models/
│   │   │   │   ├── Job.js
│   │   │   │   └── Event.js
│   │   │   ├── routes/
│   │   │   │   ├── jobRoutes.js
│   │   │   │   └── eventRoutes.js
│   │   │   └── index.js
│   │   ├── .env
│   │   └── package.json
│   │
│   ├── notification-service/     # In-app notifications, WhatsApp triggers
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   └── db.js
│   │   │   ├── controllers/
│   │   │   │   └── notificationController.js
│   │   │   ├── middlewares/
│   │   │   │   └── authMiddleware.js
│   │   │   ├── models/
│   │   │   │   └── Notification.js
│   │   │   ├── routes/
│   │   │   │   └── notificationRoutes.js
│   │   │   └── index.js
│   │   ├── .env
│   │   └── package.json
│   │
│   └── search-service/           # Cross-service search (listings + shops)
│       ├── src/
│       │   ├── config/
│       │   │   └── db.js
│       │   ├── controllers/
│       │   │   └── searchController.js
│       │   ├── middlewares/
│       │   │   └── authMiddleware.js
│       │   ├── routes/
│       │   │   └── searchRoutes.js
│       │   └── index.js
│       ├── .env
│       └── package.json
│
├── .gitignore
└── README.md                     # This file
```

---

## 🔌 Service Port Map

| Service              | Port |
|----------------------|------|
| API Gateway          | 8000 |
| auth-service         | 3001 |
| listing-service      | 3002 |
| shop-service         | 3003 |
| community-service    | 3004 |
| emergency-service    | 3005 |
| jobs-events-service  | 3006 |
| notification-service | 3007 |
| search-service       | 3008 |

---

## 🔧 Tech Stack

| Layer        | Technology                        |
|--------------|-----------------------------------|
| Runtime      | Node.js 18+                       |
| Framework    | Express.js                        |
| Database     | MongoDB + Mongoose                |
| Auth         | Firebase Auth + JWT               |
| File Storage | Cloudinary                        |
| Gateway      | http-proxy-middleware             |

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- MongoDB (local or Atlas)
- npm

### Setup Each Service

```bash
# Clone the repo
git clone https://github.com/your-org/localink-backend.git
cd localink-backend

# Install dependencies for all services
for dir in api-gateway services/*/; do
  echo "Installing: $dir"
  (cd "$dir" && npm install)
done

# Start a specific service (example)
cd services/auth-service
npm run dev
```

### Environment Variables

Each service has its own `.env` file. Copy `.env.example` and fill in values:

```env
PORT=3001
MONGO_URI=mongodb://localhost:27017/localink
JWT_SECRET=your_secret_here
FIREBASE_PROJECT_ID=your_project_id
```

---

## 📡 API Gateway Routes

All frontend requests go through `http://localhost:8000`

| Prefix             | Forwards To          |
|--------------------|----------------------|
| `/api/auth`        | auth-service:3001    |
| `/api/listings`    | listing-service:3002 |
| `/api/shops`       | shop-service:3003    |
| `/api/community`   | community-service:3004 |
| `/api/emergency`   | emergency-service:3005 |
| `/api/jobs-events` | jobs-events-service:3006 |
| `/api/notifications` | notification-service:3007 |
| `/api/search`      | search-service:3008  |

---


*Built for communities. Made with ❤️ for small towns of India.*
