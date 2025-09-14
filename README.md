# ShopifyIngestion

# 🛍️ Shopify Data Ingestion & Insights Service

A multi-tenant SaaS application that integrates with Shopify stores to ingest and analyze customer data, providing powerful insights through interactive dashboards.

## 🚀 Features

### Core Functionality
- **Multi-tenant Architecture**: Complete isolation between different Shopify stores
- **Shopify Integration**: Real-time data sync via APIs and webhooks
- **Analytics Dashboard**: Interactive charts and metrics
- **Authentication**: Secure JWT-based auth system
- **Data Visualization**: Beautiful charts using Recharts

### Data Sources
- ✅ **Customers**: Profile data, purchase history, spending patterns
- ✅ **Orders**: Transaction details, trends, revenue analysis
- ✅ **Products**: Performance metrics, sales data
- ✅ **Events**: Cart abandonment, checkout events (bonus feature)

### Dashboard Insights
- 📊 **Overview Metrics**: Total customers, orders, revenue, products
- 📈 **Trend Analysis**: Orders/revenue over time with date filtering
- 👑 **Top Customers**: Ranked by total spend
- 🏆 **Product Performance**: Best-selling products
- 📱 **Responsive Design**: Works on desktop and mobile

## 🏗️ Architecture

### High-Level Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   React.js      │    │   Express.js    │    │   PostgreSQL    │
│   Dashboard     │◄──►│   Backend API   │◄──►│   Database      │
│   (Frontend)    │    │   + Prisma ORM  │    │   (Multi-tenant)│
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                │
                                ▼
                       ┌─────────────────┐
                       │   Shopify APIs  │
                       │   + Webhooks    │
                       └─────────────────┘
```

### Tech Stack
**Backend:**
- Node.js + Express.js
- Prisma ORM
- PostgreSQL
- JWT Authentication
- Axios for API calls
- Cron jobs for data sync

**Frontend:**
- React.js
- Tailwind CSS
- Recharts for visualizations
- Axios for API calls
- React Router for navigation

**Infrastructure:**
- Docker & Docker Compose
- Railway/Heroku deployment ready
- Automated database migrations

## 🚀 Quick Start

### Prerequisites
- Node.js 18+
- PostgreSQL 13+
- Shopify store with Admin API access

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/shopify-insights.git
cd shopify-insights
```

### 2. Backend Setup
```bash
# Install dependencies
npm install

# Setup environment variables
cp .env.example .env
# Edit .env with your database URL and JWT secret

# Setup database
npm run prisma:migrate
npm run prisma:generate

# Start development server
npm run dev
```

### 3. Frontend Setup
```bash
cd frontend
npm install
npm start
```

### 4. Docker Setup (Alternative)
```bash
# Start all services
docker-compose up -d

# Run database migrations
docker-compose exec backend npx prisma migrate deploy
```

## 🔧 Configuration

### Environment Variables

**Backend (.env):**
```env
DATABASE_URL="postgresql://username:password@localhost:5432/shopify_insights"
JWT_SECRET="your-super-secure-jwt-secret"
PORT=3001
NODE_ENV=development
SHOPIFY_WEBHOOK_SECRET="your-webhook-secret"
```

**Frontend (.env):**
```env
REACT_APP_API_URL=http://localhost:3001/api
```

### Shopify Setup

1. **Create a Custom App:**
   - Go to Shopify Admin → Apps → App and sales channel settings
   - Click "Develop apps" → "Create an app"

2. **Configure API Scopes:**
   ```
   read_customers
   read_orders
   read_products
   read_checkouts (for cart abandonment)
   ```

3. **Get Access Token:**
   - Install the app
   - Copy the Admin API access token

4. **Setup Webhooks (Optional):**
   ```
   Order creation: [YOUR_DOMAIN]/api/webhooks/orders/create
   Customer creation: [YOUR_DOMAIN]/api/webhooks/customers/create
   Checkout creation: [YOUR_DOMAIN]/api/webhooks/checkouts/create
   ```

## 📊 API Endpoints

### Authentication
```
POST   /api/auth/register     Register new tenant
POST   /api/auth/login        Login tenant
GET    /api/auth/me          Get current tenant info
```

### Data Sync
```
POST   /api/sync/manual       Trigger manual sync
GET    /api/sync/status       Get sync status
```

### Analytics
```
GET    /api/analytics/overview              Dashboard overview
GET    /api/analytics/orders-by-date        Orders timeline
GET    /api/analytics/top-customers         Top customers by spend
GET    /api/analytics/revenue-trends        Revenue trends
GET    /api/analytics/product-performance   Product sales data
```

### Webhooks
```
POST   /api/webhooks/orders/create       Order created webhook
POST   /api/webhooks/customers/create    Customer created webhook
POST   /api/webhooks/checkouts/create    Checkout created webhook
```

## 🗄️ Database Schema

### Core Tables
- **tenants**: Store information and Shopify credentials
- **customers**: Customer profiles and spending data  
- **orders**: Transaction records and order details
- **products**: Product catalog and metadata
- **order_items**: Line items for each order
- **events**: Custom events (cart abandonment, etc.)

### Multi-tenancy
All tables include `tenantId` for complete data isolation between stores.

## 📈 Key Features Implemented

### 1. Multi-tenant Data Isolation
```javascript
// All queries automatically include tenant filtering
const customers = await prisma.customer.findMany({
  where: { tenantId: req.user.tenantId }
});
```

### 2. Automated Data Sync
```javascript
// Scheduled sync every hour
cron.schedule('0 * * * *', async () => {
  await syncAllTenants();
});
```

### 3. Real-time Webhooks
```javascript
// Process Shopify webhooks instantly
router.post('/webhooks/orders/create', verifyWebhook, async (req, res) => {
  // Update database with new order
});
```

### 4. Interactive Dashboard
- Real-time metrics and KPIs
- Date range filtering
- Responsive charts and tables
- Loading states and error handling



## 🧪 Testing

### Manual Testing Checklist
- [ ] User registration and login
- [ ] Shopify store connection
- [ ] Data synchronization  
- [ ] Dashboard metrics display
- [ ] Charts and visualizations
- [ ] Date range filtering
- [ ] Multi-tenant isolation
- [ ] Webhook processing

### API Testing
```bash
# Test endpoints with curl
curl -X POST http://localhost:3001/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"test123","storeName":"Test Store","shopifyDomain":"test-store","shopifyAccessToken":"shpat_xxx"}'
```

## 🔍 Troubleshooting

### Common Issues

1. **Database Connection Error**
   ```bash
   # Check PostgreSQL is running
   sudo service postgresql status
   
   # Verify connection string in .env
   ```

2. **Shopify API Rate Limits**
   - Implement exponential backoff
   - Current implementation handles 429 responses
   - Reduce sync frequency if needed

3. **CORS Issues**
   ```javascript
   // Update CORS configuration
   app.use(cors({
     origin: process.env.FRONTEND_URL,
     credentials: true
   }));
   ```

4. **Prisma Schema Changes**
   ```bash
   # Reset database (development only)
   npx prisma migrate reset
   npx prisma migrate dev
   ```

## 📝 Next Steps for Production

### Performance Optimization
- [ ] Implement Redis caching
- [ ] Add database indexing
- [ ] Optimize API queries
- [ ] Add request rate limiting

### Enhanced Features  
- [ ] Advanced analytics (cohort analysis, LTV)
- [ ] Email notifications for key events
- [ ] Export data functionality
- [ ] Mobile app support

### Security Improvements
- [ ] API key rotation
- [ ] Enhanced input validation
- [ ] Security headers
- [ ] Audit logging

### Monitoring & Observability
- [ ] Application monitoring (DataDog/NewRelic)
- [ ] Error tracking (Sentry)
- [ ] Performance monitoring
- [ ] Health check endpoints

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request


## 👨‍💻 Developer

Built by Navdeep Kaur for the Xeno FDE Internship Assignment 2025.

**Demo:** [Live Demo URL]
**Repository:** [GitHub Repository URL]

---

*This project demonstrates full-stack development skills, system architecture design, and the ability to integrate with third-party APIs in a production-ready application.*
