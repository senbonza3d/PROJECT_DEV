# Neo4j Integration for SwiftBox Delivery App

## Overview
This project now includes **Neo4j graph database** integration alongside MongoDB for advanced features like:
- 🎯 **Personalized product recommendations**
- 📊 **Purchase pattern analysis**
- 🔥 **Popular products tracking**
- 👥 **User behavior insights**

## Architecture
- **MongoDB**: Primary database for storing products, orders, users (document-based)
- **Neo4j**: Graph database for relationships and recommendations (graph-based)

## Setup Instructions

### 1. Install Neo4j

#### Option A: Neo4j Desktop (Recommended for Development)
1. Download from: https://neo4j.com/download/
2. Install and create a new project
3. Create a new database with:
   - Database name: `swiftbox`
   - Password: `neo4jpassword` (or update `.env`)
4. Start the database

#### Option B: Docker
```bash
docker run \
    --name neo4j \
    -p 7474:7474 -p 7687:7687 \
    -e NEO4J_AUTH=neo4j/neo4jpassword \
    neo4j:latest
```

### 2. Install Python Dependencies
```bash
./venv/bin/pip install -r requirements.txt
```

### 3. Configure Environment Variables
Update `.env` file with Neo4j credentials:
```env
NEO4J_URI=bolt://localhost:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=neo4jpassword
```

### 4. Sync Data to Neo4j
Run the sync script to populate Neo4j with your existing data:
```bash
./venv/bin/python sync_to_neo4j.py
```

This will:
- Create nodes for all users, products, and categories
- Create relationships for all purchases
- Build the graph structure for recommendations

## API Endpoints

### 1. Get Personalized Recommendations
**Endpoint**: `GET /api/recommendations/`  
**Authentication**: Required  
**Description**: Get product recommendations based on user's purchase history and similar users

**Example Request**:
```bash
curl -H "Authorization: Token YOUR_TOKEN" \
     http://localhost:8000/api/recommendations/
```

**Example Response**:
```json
{
  "count": 5,
  "algorithm": "collaborative_filtering_and_category_based",
  "recommendations": [
    {
      "id": "...",
      "name": "Wireless Headphones",
      "price": "199.99",
      "category": "Electronics",
      "image": "..."
    }
  ]
}
```

### 2. Get Popular Products
**Endpoint**: `GET /api/popular/`  
**Authentication**: Not required  
**Description**: Get most popular products based on purchase count

**Example Request**:
```bash
curl http://localhost:8000/api/popular/
```

### 3. Get User Purchase History (from Neo4j)
**Endpoint**: `GET /api/my-purchases/`  
**Authentication**: Required  
**Description**: Get user's purchase history from the graph database

**Example Request**:
```bash
curl -H "Authorization: Token YOUR_TOKEN" \
     http://localhost:8000/api/my-purchases/
```

## Graph Database Schema

### Nodes
- **User**: `{id, username, email}`
- **Product**: `{id, name, category, price}`
- **Category**: `{name}`

### Relationships
- `(User)-[:PURCHASED {quantity, price, timestamp}]->(Product)`
- `(User)-[:VIEWED {count, last_viewed}]->(Product)`
- `(Product)-[:BELONGS_TO]->(Category)`

## Recommendation Algorithm

The system uses a hybrid recommendation approach:

1. **Collaborative Filtering**:
   - Find users who purchased similar products
   - Recommend products those users bought that current user hasn't

2. **Category-Based**:
   - Identify user's favorite categories
   - Recommend popular products from those categories

3. **Popularity-Based**:
   - Track most purchased products across all users
   - Use as fallback recommendations

## Neo4j Browser Queries

Access Neo4j Browser at: http://localhost:7474

### View All Data
```cypher
MATCH (n) RETURN n LIMIT 100
```

### Find User's Purchases
```cypher
MATCH (u:User {username: "testuser1001"})-[r:PURCHASED]->(p:Product)
RETURN u, r, p
```

### Get Product Recommendations for a User
```cypher
MATCH (u:User {username: "testuser1001"})-[:PURCHASED]->(p:Product)<-[:PURCHASED]-(other:User)
WITH other, COUNT(p) as similarity
ORDER BY similarity DESC
LIMIT 10
MATCH (other)-[:PURCHASED]->(rec:Product)
WHERE NOT (u)-[:PURCHASED]->(rec)
RETURN DISTINCT rec.name, COUNT(*) as score
ORDER BY score DESC
LIMIT 5
```

### Most Popular Products
```cypher
MATCH (p:Product)<-[r:PURCHASED]-()
RETURN p.name, COUNT(r) as purchases, SUM(r.quantity) as total_quantity
ORDER BY purchases DESC
LIMIT 10
```

### Products by Category
```cypher
MATCH (p:Product)-[:BELONGS_TO]->(c:Category)
RETURN c.name as category, COUNT(p) as product_count
ORDER BY product_count DESC
```

## Automatic Sync

To keep Neo4j in sync with MongoDB, you can:

1. **Manual Sync**: Run `./venv/bin/python sync_to_neo4j.py` periodically

2. **Automatic Sync** (Future Enhancement): 
   - Add signals in Django models to update Neo4j on create/update
   - Use Celery for background sync tasks

## Performance Tips

1. **Indexes**: Neo4j automatically creates indexes for node IDs
2. **Batch Operations**: The sync script uses batch operations for efficiency
3. **Connection Pooling**: The driver manages connection pooling automatically

## Troubleshooting

### Neo4j Connection Failed
- Ensure Neo4j is running: Check http://localhost:7474
- Verify credentials in `.env` file
- Check firewall settings for port 7687

### No Recommendations Returned
- Run sync script to populate data: `./venv/bin/python sync_to_neo4j.py`
- Ensure user has made purchases
- Check Neo4j Browser for data

### Slow Queries
- Ensure Neo4j has sufficient memory
- Check query complexity
- Consider adding custom indexes

## Future Enhancements

- [ ] Real-time sync using Django signals
- [ ] Product similarity based on attributes
- [ ] User-to-user friend recommendations
- [ ] Trending products detection
- [ ] Category affinity scoring
- [ ] A/B testing for recommendation algorithms

## Resources

- Neo4j Documentation: https://neo4j.com/docs/
- Cypher Query Language: https://neo4j.com/docs/cypher-manual/
- Python Driver: https://neo4j.com/docs/python-manual/
