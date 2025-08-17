<h1 align="center">E-commerce Website</h1>
<p align="center">Spring Boot backend • React + Vite frontend</p>

<p align="center">
  <a href="#-project-structure">Structure</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-configuration">Configuration</a> •
  <a href="#-api-examples">API</a> •
  <a href="#-scripts">Scripts</a> •
  <a href="#-troubleshooting">Troubleshooting</a>
</p>

---

## 📦 Project Structure

E-commerce-website/
├─ ecom-backend/ # Spring Boot app (Maven/Gradle)
│ ├─ src/main/java/...
│ ├─ src/main/resources/application.properties
│ └─ pom.xml
├─ ecom-frontend/ # React + Vite app (normal folder)
│ ├─ index.html
│ ├─ src/
│ ├─ package.json
│ └─ vite.config.(ts|js)
├─ .gitignore
└─ README.md

yaml
Copy
Edit

> If Git ever shows `modified: ecom-frontend (modified content in submodules)`, the frontend became a **submodule** by mistake—see **Troubleshooting**.

---

## 🚀 Quick Start

### 1) Backend (Spring Boot)

```bash
cd ecom-backend
mvn spring-boot:run
# http://localhost:8080
2) Frontend (React + Vite)
bash
Copy
Edit
cd ../ecom-frontend
npm install
npm run dev
# http://localhost:5173
⚙️ Configuration
application.properties (dev example)
properties
Copy
Edit
# Server
server.port=8080

# H2 (dev)
spring.datasource.url=jdbc:h2:file:~/ecomdb;AUTO_SERVER=TRUE;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# Actuator (debug endpoints/mappings)
management.endpoints.web.exposure.include=health,info,mappings
Open H2 console: http://localhost:8080/h2-console
Use the same JDBC URL as above.

Vite dev proxy (avoid CORS during dev)
ecom-frontend/vite.config.(ts|js):

ts
Copy
Edit
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': 'http://localhost:8080'
    }
  }
})
Expose Spring endpoints under /api/... (e.g., @RequestMapping("/api")) so the proxy applies cleanly.

🔗 API Examples
Assuming controllers are under /api.

Create product (cURL):

bash
Copy
Edit
curl -i -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Item","description":"Nice","brand":"BrandX","price":19.99,"category":"General","releaseDate":"2024-01-15","available":true,"quantity":10}'
Frontend calls (fetch):

ts
Copy
Edit
export async function fetchProducts() {
  const res = await fetch('/api/products');  // proxied to 8080 in dev
  if (!res.ok) throw new Error('Failed to fetch products');
  return res.json();
}

export async function createProduct(p: unknown) {
  const res = await fetch('/api/products', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(p),
  });
  if (!res.ok) throw new Error('Failed to create product');
  return res.json?.() ?? null;
}
🧰 Scripts
Backend (Maven):

bash
Copy
Edit
mvn spring-boot:run
mvn test
mvn package
Frontend (npm):

bash
Copy
Edit
npm run dev
npm run build
npm run preview
🧯 Troubleshooting
Frontend shows as “modified content in submodules”
Your ecom-frontend turned into a submodule. Convert back to a normal folder:

Ensure the real files exist (re-add submodule temporarily if needed to materialize files).

Move files out, remove submodule wiring, move files back, remove any nested .git in ecom-frontend.

git add ecom-frontend && git commit -m "Make frontend a normal folder".

415 / Unsupported Media Type
Send JSON: Content-Type: application/json and JSON.stringify(body) in the frontend.

CORS errors
Use the Vite proxy above. If calling directly, add @CrossOrigin or a global CORS config in Spring.

JPA entity issues
Use a regular class with a no-args constructor; avoid Java record for JPA entities. Don’t name columns with SQL keywords like desc—use description.

🗺️ Roadmap
Switch H2 → PostgreSQL/MySQL

Authentication/authorization

CI/CD (GitHub Actions)

Docker Compose for local dev

📄 License
TBD. Consider adding a LICENSE (MIT/Apache-2.0) if you plan to open source.

yaml
Copy
Edit

---

### Why your alignment looked off
- Using `printf` or pasting with mismatched quotes can break line breaks and code fences.
- Mixing tabs/spaces inside code blocks can look jagged—this version uses spaces and consistent blank lines so GitHub renders cleanly.

If you want, I can commit this directly as a PR-ready patch (just paste it here if you need small tweaks like your exact endpoints).
::contentReference[oaicite:0]{index=0}
