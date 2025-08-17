# 🛒 E-commerce Website

A full-stack e-commerce application built with **Spring Boot** backend and **React + Vite** frontend.

## 🏗️ Architecture

- **Backend**: Spring Boot with Maven, H2 Database, JPA/Hibernate
- **Frontend**: React with Vite, TypeScript support
- **Development**: Hot reload, proxy configuration, H2 console

## 📁 Project Structure

```
E-commerce-website/
├─ ecom-backend/              # Spring Boot application
│  ├─ src/main/java/          # Java source code
│  ├─ src/main/resources/     # Configuration files
│  │  └─ application.properties
│  ├─ pom.xml                 # Maven dependencies
│  └─ target/                 # Build output
├─ ecom-frontend/             # React + Vite application  
│  ├─ src/                    # React source code
│  ├─ index.html              # Entry HTML file
│  ├─ package.json            # npm dependencies
│  ├─ vite.config.js          # Vite configuration
│  └─ dist/                   # Build output
├─ .gitignore
└─ README.md
```

## 🚀 Quick Start

### Prerequisites

- **Java 17+** (for Spring Boot)
- **Node.js 18+** (for React/Vite)
- **Maven** (for backend build)

### 1. Clone the Repository

```bash
git clone https://github.com/prateek2468/E-commerce-website.git
cd E-commerce-website
```

### 2. Backend Setup (Spring Boot)

```bash
cd ecom-backend
mvn spring-boot:run
```

The backend will start at: `http://localhost:8080`

### 3. Frontend Setup (React + Vite)

```bash
cd ../ecom-frontend
npm install
npm run dev
```

The frontend will start at: `http://localhost:5173`

## ⚙️ Configuration

### Backend Configuration

Edit `ecom-backend/src/main/resources/application.properties`:

```properties
# Server Configuration
server.port=8080

# H2 Database (Development)
spring.datasource.url=jdbc:h2:file:~/ecomdb;AUTO_SERVER=TRUE;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# H2 Console (Development)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# JPA/Hibernate Configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect

# Actuator Endpoints
management.endpoints.web.exposure.include=health,info,mappings
```

**H2 Database Console**: Access at `http://localhost:8080/h2-console`
- JDBC URL: `jdbc:h2:file:~/ecomdb`
- Username: `sa`
- Password: (leave empty)

### Frontend Configuration

Configure Vite proxy in `ecom-frontend/vite.config.js` to avoid CORS issues:

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        secure: false
      }
    }
  }
})
```

## 🔗 API Documentation

### Base URL
- Development: `http://localhost:8080/api`
- All endpoints should be prefixed with `/api`

### Product Endpoints

#### Create Product
```bash
curl -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Sample Product",
    "description": "Product description",
    "brand": "Brand Name",
    "price": 29.99,
    "category": "Electronics",
    "releaseDate": "2024-01-15",
    "available": true,
    "quantity": 50
  }'
```

#### Get All Products
```bash
curl -X GET http://localhost:8080/api/products
```

### Frontend API Integration

Example service functions:

```typescript
// Product Service
export const productService = {
  // Get all products
  async fetchProducts() {
    const response = await fetch('/api/products');
    if (!response.ok) throw new Error('Failed to fetch products');
    return response.json();
  },

  // Create new product
  async createProduct(product: Product) {
    const response = await fetch('/api/products', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(product),
    });
    if (!response.ok) throw new Error('Failed to create product');
    return response.json();
  },

  // Update product
  async updateProduct(id: number, product: Product) {
    const response = await fetch(`/api/products/${id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(product),
    });
    if (!response.ok) throw new Error('Failed to update product');
    return response.json();
  },

  // Delete product
  async deleteProduct(id: number) {
    const response = await fetch(`/api/products/${id}`, {
      method: 'DELETE',
    });
    if (!response.ok) throw new Error('Failed to delete product');
  }
};
```

## 🛠️ Development Scripts

### Backend (Maven)
```bash
# Run application
mvn spring-boot:run

# Run tests
mvn test

# Build package
mvn package

# Clean build
mvn clean package
```

### Frontend (npm)
```bash
# Development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Run tests (if configured)
npm run test

# Lint code (if configured)
npm run lint
```

## 🐛 Troubleshooting

### Common Issues

#### 1. Frontend appears as "modified content in submodules"
This happens when the frontend folder becomes a Git submodule accidentally.

**Solution**:
1. Backup your frontend files
2. Remove the submodule reference
3. Re-add as a regular folder
4. Commit changes

```bash
git submodule deinit ecom-frontend
rm -rf .git/modules/ecom-frontend
git rm --cached ecom-frontend
# Re-add files normally
git add ecom-frontend
git commit -m "Fix: Convert frontend from submodule to regular folder"
```

#### 2. HTTP 415 - Unsupported Media Type
**Cause**: Missing or incorrect Content-Type header

**Solution**: Ensure you're sending JSON with proper headers:
```javascript
fetch('/api/products', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(data)
})
```

#### 3. CORS Errors
**Solution**: Use the Vite proxy configuration shown above, or add CORS configuration in Spring Boot:

```java
@CrossOrigin(origins = "http://localhost:5173")
@RestController
@RequestMapping("/api")
public class ProductController {
    // Controller methods
}
```

#### 4. JPA Entity Issues
- Use regular classes with no-args constructors (not records) for JPA entities
- Avoid SQL reserved keywords as column names (use `description` instead of `desc`)
- Ensure proper JPA annotations (`@Entity`, `@Id`, `@GeneratedValue`, etc.)

#### 5. Port Already in Use
```bash
# Find process using port 8080
lsof -i :8080
# Kill process
kill -9 <PID>

# Or change port in application.properties
server.port=8081
```

## 🚀 Deployment

### Production Build

#### Backend
```bash
cd ecom-backend
mvn clean package
java -jar target/ecom-backend-*.jar
```

#### Frontend
```bash
cd ecom-frontend
npm run build
# Serve dist/ folder with web server
```

### Environment Variables
Create `application-prod.properties` for production:

```properties
server.port=${PORT:8080}
spring.datasource.url=${DATABASE_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=validate
spring.h2.console.enabled=false
```

## 🗺️ Roadmap

- [ ] **Database Migration**: Switch from H2 to PostgreSQL/MySQL
- [ ] **Authentication**: Implement JWT-based auth system
- [ ] **Shopping Cart**: Add cart functionality
- [ ] **Payment Integration**: Stripe/PayPal integration
- [ ] **User Management**: Registration, profiles, order history
- [ ] **Admin Panel**: Product management, order tracking
- [ ] **Testing**: Unit tests, integration tests
- [ ] **CI/CD**: GitHub Actions workflow
- [ ] **Containerization**: Docker and Docker Compose setup
- [ ] **Monitoring**: Logging, metrics, health checks

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Submit a pull request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Prateek** - [@prateek2468](https://github.com/prateek2468)

---

### 📞 Support

If you have any questions or run into issues, please:
1. Check the [Troubleshooting](#-troubleshooting) section
2. Search existing [Issues](https://github.com/prateek2468/E-commerce-website/issues)
3. Create a new issue with detailed information

---

⭐ **Star this repo** if you find it helpful!
