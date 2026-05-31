# DZ Fashion Hub 🛍️

A production-ready fashion marketplace aggregator platform for Algeria, connecting customers with clothing stores across the country.

## 📋 Project Overview

**DZ Fashion Hub** is a centralized marketplace that aggregates products from multiple Algerian fashion stores. The platform operates on a commission-based model, earning revenue from successful sales transactions.

### Key Features

- 🛒 Multi-store product aggregation
- 🔍 Advanced search with AI capabilities
- 💳 Multiple payment methods (Cash on Delivery, Bank Transfer, Edahabia, CIB)
- 📦 Integrated delivery tracking
- ⭐ Product and store reviews
- 🎯 AI-powered recommendations
- 📱 Mobile-first responsive design
- 🌙 Dark/Light mode support
- 🗣️ Multi-language (Arabic, French, English)

## 🏗️ Tech Stack

### Frontend
- **Next.js 14+** - React framework
- **React 18+** - UI library
- **TypeScript** - Type safety
- **Tailwind CSS** - Styling
- **Framer Motion** - Animations
- **React Query** - Data fetching

### Backend
- **Node.js** - Runtime
- **Express.js** - Framework
- **TypeScript** - Type safety
- **PostgreSQL** - Database
- **JWT** - Authentication
- **Cloudinary** - Media storage

### DevOps
- **Docker** - Containerization
- **Docker Compose** - Orchestration
- **GitHub Actions** - CI/CD

## 📂 Project Structure

```
dz-fashion-hub/
├── frontend/                 # Next.js application
├── backend/                  # Express.js API
├── database/                 # PostgreSQL schemas
├── docs/                     # Documentation
├── docker-compose.yml        # Docker configuration
└── README.md
```

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- PostgreSQL 14+
- Docker & Docker Compose
- npm or yarn

### Installation

```bash
# Clone repository
git clone https://github.com/zerargamouad20-hue/dz-fashion-hub.git
cd dz-fashion-hub

# Install dependencies
npm install

# Setup environment variables
cp .env.example .env.local

# Start development
npm run dev
```

## 📊 Business Model

1. **Store Registration** - Stores register and upload products
2. **Order Processing** - Customers purchase through platform
3. **Commission** - Platform earns commission on each sale
4. **Delivery Coordination** - Platform manages delivery logistics
5. **Growth** - Scale to become largest fashion aggregator in Algeria

## 💰 Target Market

- **Country**: Algeria
- **Currency**: Algerian Dinar (DZD)
- **Languages**: Arabic, French, English
- **Users**: Fashion-conscious shoppers, clothing stores

## 📱 Supported Devices

- Desktop (1920px+)
- Tablet (768px - 1024px)
- Mobile (320px - 767px)

## 🎨 Design System

### Colors
- **Primary**: #0F172A (Navy)
- **Secondary**: #2563EB (Blue)
- **Accent**: #38BDF8 (Sky Blue)
- **Background**: White & Dark Gray

### Design Inspiration
- Amazon
- Zalando
- Temu
- ASOS

## 🔐 Security Features

- Role-based access control (RBAC)
- JWT authentication
- SQL injection protection
- XSS prevention
- CSRF protection
- Rate limiting
- Input validation
- Secure password hashing

## 📚 API Documentation

Complete REST API documentation available at `/docs/api`

### Main Endpoints
- `/api/users` - User management
- `/api/stores` - Store management
- `/api/products` - Product catalog
- `/api/orders` - Order processing
- `/api/reviews` - Reviews & ratings
- `/api/payments` - Payment processing
- `/api/delivery` - Delivery tracking
- `/api/analytics` - Analytics & reports

## 🛠️ Development

### Available Scripts

```bash
# Development
npm run dev

# Build
npm run build

# Production
npm run start

# Tests
npm run test

# Linting
npm run lint

# Database migration
npm run migrate
```

## 📈 Deployment

- Frontend: Vercel
- Backend: AWS EC2 / DigitalOcean
- Database: AWS RDS
- Storage: Cloudinary

See `docs/deployment.md` for detailed instructions.

## 📖 Documentation

- [Architecture](docs/ARCHITECTURE.md)
- [Database Schema](docs/DATABASE.md)
- [API Reference](docs/API.md)
- [Deployment Guide](docs/DEPLOYMENT.md)
- [Contributing](docs/CONTRIBUTING.md)

## 👥 Team

Created by: Zerar Gamouad

## 📝 License

MIT License - See LICENSE file

## 🤝 Contributing

Contributions welcome! Please see CONTRIBUTING.md

## 📞 Support

For issues and questions, please open a GitHub issue.

---

**DZ Fashion Hub** - Empowering Algerian Fashion Commerce 🇩🇿
