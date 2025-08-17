E-commerce Website (Spring Boot + React/Vite)

Monorepo containing a Spring Boot backend and a React (Vite) frontend.

📦 Structure
E-commerce-website/
├─ ecom-backend/               # Spring Boot app (Maven/Gradle)
│  ├─ src/main/java/...
│  ├─ src/main/resources/application.properties
│  └─ pom.xml
├─ ecom-frontend/              # React + Vite app (normal folder)
│  ├─ index.html
│  ├─ src/
│  ├─ package.json
│  └─ vite.config.(ts|js)
├─ .gitignore
└─ README.md


If Git ever shows modified: ecom-frontend (modified content in submodules), it means the frontend accidentally became a submodule. See Troubleshooting.

✅ Prerequisites

Java 17+

Node.js 18+ and npm (or pnpm/yarn)

Maven (if using Maven build)

🚀 Getting Started
1) Backend (Spring Boot)
cd ecom-backend
mvn spring-boot:run
# App running at http://localhost:8080


Example application.properties (dev):

# Server
server.port=8080

# H2 dev database
spring.datasource.url=jdbc:h2:file:~/ecomdb;AUTO_SERVER=TRUE;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=update   # dev: update/create
spring.jpa.show-sql=true

# Actuator (for debugging mappings)
management.endpoints.web.exposure.include=health,info,mappings


Open H2 console: http://localhost:8080/h2-console
(Use the same JDBC URL as above.)

2) Frontend (React + Vite)
cd ../ecom-frontend
npm install
npm run dev
# App available at http://localhost:5173


Recommended (no CORS during dev): set up a Vite proxy and expose Spring endpoints under /api/....

vite.config.(ts|js):

import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      // forward /api requests to Spring Boot
      '/api': 'http://localhost:8080'
    }
  }
})


Alternative: use a base URL env var (e.g., VITE_API_BASE_URL=http://localhost:8080) and call fetch(\${import.meta.env.VITE_API_BASE_URL}/api/...). The proxy approach is simpler for development.

🧩 Calling the API (examples)

Assuming your controller uses @RequestMapping("/api").

Frontend fetch example:

// src/api/products.ts
export async function fetchProducts() {
  const res = await fetch('/api/products'); // proxied to 8080 in dev
  if (!res.ok) throw new Error('Failed to fetch products');
  return res.json();
}

export async function createProduct(p: any) {
  const res = await fetch('/api/products', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(p),
  });
  if (!res.ok) throw new Error('Failed to create product');
  return res.json?.() ?? null;
}


cURL example (create product):

curl -i -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Item","description":"Nice","brand":"BrandX","price":19.99,"category":"General","releaseDate":"2024-01-15","available":true,"quantity":10}'

🛠️ Development Notes

JPA entities: Use a regular class with a no-args constructor and getters/setters (or Lombok). Avoid Java record for JPA entities.

Avoid SQL reserved words like desc for column names. Prefer description.

Enable Actuator and visit /actuator/mappings to confirm your endpoints are registered.

If calling APIs without the Vite proxy, configure CORS on Spring (@CrossOrigin or a WebMvcConfigurer).

📜 Scripts

Backend (Maven)

mvn spring-boot:run
mvn test
mvn package


Frontend (npm)

npm run dev
npm run build
npm run preview

🧯 Troubleshooting

“modified content in submodules” (Git)

This means ecom-frontend turned into a submodule. Convert back to a normal folder:

Ensure you have the actual frontend files (re-add submodule temporarily if needed to materialize files).

Move them out, remove submodule wiring, move them back, and remove any nested .git inside ecom-frontend.

415 / unsupported media type

Send JSON with Content-Type: application/json and JSON.stringify(body).

CORS errors in browser

Use the Vite proxy (recommended).

Or enable CORS in Spring for your origins/methods.

H2 driver / DB not found

Confirm H2 dependency and JDBC URL matches the H2 console settings.

🗺️ Roadmap (optional)

Swap H2 → PostgreSQL/MySQL

Authentication/authorization

CI/CD (GitHub Actions)

Docker Compose for local dev

📄 License

TBD. Consider adding a LICENSE (e.g., MIT or Apache-2.0) if you plan to open source.
