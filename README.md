# SwiftBox Delivery Platform 🚀

A modern full-stack delivery platform with **hybrid database architecture** (MongoDB + Neo4j) built with Django and React.

## 🏗️ Architecture

This project uses a **hybrid database approach**:
- **MongoDB**: Primary database for products, users, orders (Django ORM)
- **Neo4j**: Graph database for recommendations and analytics
- **Automatic Sync**: Real-time synchronization between databases

See [HYBRID_ARCHITECTURE.md](HYBRID_ARCHITECTURE.md) for detailed architecture documentation.

---

## 📁 Project Structure

```
├── backend/              # Django REST API
├── frontend/             # React TypeScript application
├── delivery_app/         # Main Django app
│   ├── models.py        # MongoDB models
│   ├── neo4j_db.py      # Neo4j connection handler
│   ├── signals.py       # Auto-sync between databases
│   └── views.py         # API endpoints
├── sync_to_neo4j.py     # Manual sync script
└── requirements.txt     # Python dependencies
```

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- Node.js 14+
- Docker (for databases)

### 1. Start Databases

**MongoDB**:
```bash
docker run -d --name mongodb-django -p 27017:27017 mongo:latest
```

**Neo4j**:
```bash
docker run -d --name neo4j \
  -p 7474:7474 -p 7687:7687 \
  -e NEO4J_AUTH=neo4j/neo4jpassword \
  neo4j:latest
```

### 2. Backend Setup

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run migrations
python manage.py migrate

# Sync data to Neo4j
./venv/bin/python sync_to_neo4j.py

# Start Django server
python manage.py runserver
```

### 3. Frontend Setup

```bash
cd frontend
npm install
npm start
```

### 4. Access the Application

- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:8000/api/
- **Django Admin**: http://localhost:8000/admin/
- **Neo4j Browser**: http://localhost:7474 (neo4j/neo4jpassword)

---

## ✨ Features

### Core Features
- ✅ User authentication & authorization
- ✅ Product catalog with categories
- ✅ Shopping cart functionality
- ✅ Order management & tracking
- ✅ User reviews & ratings
- ✅ Admin dashboard
- ✅ Order history

### AI-Powered Features (Neo4j)
- 🎯 **Personalized Recommendations**: Based on purchase history
- 📊 **Popular Products**: Real-time trending items
- 👥 **Similar Users**: Find users with similar tastes
- 📈 **Purchase Analytics**: Pattern detection
- 🔗 **Category Insights**: Smart product grouping

---

## 🗄️ Database Architecture

### MongoDB (Primary)
Stores core application data:
- Users & Authentication
- Products & Categories
- Orders & Order Items
- Comments & Ratings

### Neo4j (Graph)
Handles relationships:
- User → Product (PURCHASED)
- Product → Category (BELONGS_TO)
- User → Product (VIEWED)

**Automatic Sync**: Django signals keep both databases synchronized in real-time!

---

## 🌐 API Endpoints

### MongoDB-Backed
```
GET    /api/products/              # List products
POST   /api/products/              # Create product
GET    /api/products/{id}/         # Product details
POST   /api/register/              # User signup
POST   /api/login/                 # User login
POST   /api/orders/                # Create order
GET    /api/admin/orders/          # List orders (admin)
```

### Neo4j-Powered
```
GET    /api/recommendations/       # Personalized recommendations
GET    /api/popular/               # Popular products
GET    /api/my-purchases/          # Purchase history (graph)
```

---

## 🔧 Configuration

### Environment Variables (`.env`)
```env
# MongoDB
DB_NAME=delivery_app
DB_USER=admin
DB_PASSWORD=admin123

# Neo4j
NEO4J_URI=bolt://localhost:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=neo4jpassword
```

---

## 📊 Data Synchronization

### Automatic Sync
Data automatically syncs from MongoDB to Neo4j when:
- New user signs up
- Product is created/updated
- Order is placed
- User/Product is deleted

### Manual Sync
If needed, manually sync all data:
```bash
./venv/bin/python sync_to_neo4j.py
```

---

## 🛠️ Development

### Run Tests
```bash
python manage.py test
```

### Create Admin User
```bash
python manage.py createsuperuser
```

### Add Sample Products
```bash
./venv/bin/python add_more_products.py
```

### Update Product Images
```bash
./venv/bin/python fix_all_images.py
```

---

## 📚 Documentation

- [HYBRID_ARCHITECTURE.md](HYBRID_ARCHITECTURE.md) - Detailed architecture guide
- [NEO4J_README.md](NEO4J_README.md) - Neo4j setup and usage
- [NEO4J_STATUS.md](NEO4J_STATUS.md) - Current Neo4j status
- [NEO4J_INTEGRATION_SUMMARY.md](NEO4J_INTEGRATION_SUMMARY.md) - Integration overview

---

## 🎯 Tech Stack

### Backend
- **Django 6.0** - Web framework
- **Django REST Framework** - API
- **MongoDB** - Primary database
- **Neo4j** - Graph database
- **Python 3.13** - Programming language

### Frontend
- **React 18** - UI framework
- **TypeScript** - Type safety
- **React Router** - Navigation
- **Axios** - HTTP client

### Infrastructure
- **Docker** - Containerization
- **MongoDB** - Document database
- **Neo4j** - Graph database

---

## 🚀 Deployment

### Production Checklist
- [ ] Set `DEBUG = False` in settings.py
- [ ] Configure proper SECRET_KEY
- [ ] Set up production database credentials
- [ ] Configure CORS for production domain
- [ ] Set up SSL/HTTPS
- [ ] Configure static file serving
- [ ] Set up monitoring and logging

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

---

## 📝 License

This project is licensed under the MIT License.

---

## 🆘 Troubleshooting

### MongoDB Connection Issues
```bash
# Check if MongoDB is running
docker ps | grep mongodb

# Restart MongoDB
docker restart mongodb-django
```

### Neo4j Connection Issues
```bash
# Check if Neo4j is running
docker ps | grep neo4j

# Restart Neo4j
docker restart neo4j

# Wait for Neo4j to be ready
sleep 15
```

### Sync Issues
```bash
# Re-sync all data
./venv/bin/python sync_to_neo4j.py
```

---

## 📞 Support

For issues and questions:
- Check documentation files
- Review Neo4j Browser: http://localhost:7474
- Check Django logs for sync messages

---

**Built with ❤️ using Django, React, MongoDB, and Neo4j**

**Status**: ✅ Production Ready | 🚀 Fully Operational