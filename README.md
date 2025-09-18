# Student List - Containerized Web Application

A simple PHP/Flask application demonstrating Docker containerization and multi-service orchestration.

<!-- ![project](https://user-images.githubusercontent.com/18481009/84582395-ba230b00-adeb-11ea-9453-22ed1be7e268.jpg) -->
Image

---

## 📌 Overview

Student List is a two-tier web application designed to display student information with ages. The application demonstrates modern containerization practices and microservice architecture.

### Architecture Components:
- **Frontend**: PHP web interface with Apache server
- **Backend**: Flask REST API with JSON data source
- **Infrastructure**: Docker containers with orchestration

**Original source**: [student-list repository](https://github.com/diranetafen/student-list.git)

---

## 🏢 Project Background

This application was developed for **POZOS**, a French IT company specializing in High School software solutions. The project addresses common infrastructure challenges:

- **Scalability**: Moving from single-server to containerized architecture
- **Deployment**: Automating release processes to reduce manual errors
- **Modernization**: Adopting Docker for improved agility and reliability

---

## 🧩 Application Architecture

### System Components:

| **Service** | **Technology** | **Purpose** | **Port** |
|-------------|----------------|-------------|----------|
| **API** | Python/Flask | REST endpoints with basic authentication | 5000 |
| **Web Frontend** | PHP + Apache | User interface for browsing student data | 80 |
| **Data** | JSON File | Student information storage | - |

### Communication Flow:
```
User → Web Interface (PHP) → API (Flask) → JSON Data → Response
```

---

## 📁 Project Structure

```
student-list/
├── 📄 docker-compose.yml    # Service orchestration
├── 📄 Dockerfile           # API container definition  
├── 📄 requirements.txt      # Python dependencies
├── 📊 student_age.json      # Student data source
├── 🐍 student_age.py        # Flask API application
├── 🌐 index.php            # PHP frontend
├── 📁 website/             # Web assets
└── 📄 README.md            # Project documentation
```

---

## 🚀 Quick Start

### Prerequisites:
- Docker installed
- Docker Compose installed
- Basic understanding of containerization

### 1️⃣ Clone the Repository:
```bash
git clone <repository-url>
cd student-list
```

### 2️⃣ Deploy the Application:
```bash
docker-compose up -d
```

### 3️⃣ Access the Application:
- **Web Interface**: http://localhost
- **API Endpoint**: http://localhost:5000

### 4️⃣ Test the API:
```bash
curl -u toto:python -X GET http://localhost:5000/pozos/api/v1.0/get_student_ages
```

---

## 🔧 Detailed Setup

### API Container Configuration

The Flask API container is built using the following specifications:

```dockerfile
FROM python:3.11-slim
MAINTAINER your-name

# Install system dependencies
RUN apt update -y && apt install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y

# Copy application files
COPY . /
COPY requirements.txt /

# Install Python packages
RUN pip3 install -r /requirements.txt

# Create data volume
RUN mkdir /data
VOLUME ["/data"]

# Expose API port
EXPOSE 5000

# Start application
CMD ["python3", "./student_age.py"]
```

### Docker Compose Configuration

The application uses Docker Compose for multi-service orchestration:

```yaml
version: '3.8'

services:
  api:
    build: .
    container_name: student-list-api
    volumes:
      - ./student_age.json:/data/student_age.json
    ports:
      - "5000:5000"
    networks:
      - student-network

  website:
    image: php:apache
    container_name: student-list-web
    environment:
      - USERNAME=toto
      - PASSWORD=python
    volumes:
      - ./website:/var/www/html
    ports:
      - "80:80"
    depends_on:
      - api
    networks:
      - student-network

networks:
  student-network:
    driver: bridge
```

---

## 🔌 API Endpoints

### Authentication
The API uses basic HTTP authentication:
- **Username**: `toto`
- **Password**: `python`

### Available Endpoints

| **Endpoint** | **Method** | **Description** |
|--------------|------------|-----------------|
| `/pozos/api/v1.0/get_student_ages` | GET | Retrieve all students with ages |

### Example Response:
```json
[
  {
    "name": "alice",
    "age": "12"
  },
  {
    "name": "bob", 
    "age": "13"
  }
]
```

---

## 🌐 Web Interface

### Features:
- Clean, simple interface
- "List Students" button to fetch data
- Responsive design
- Error handling for API connectivity

### Configuration:
The PHP frontend connects to the API using:
```php
$url = 'http://api:5000/pozos/api/v1.0/get_student_ages';
```

> **Note**: When running via Docker Compose, use the service name `api` instead of `localhost`

---

## 📊 Data Management

### Student Data Format:
The application reads from `student_age.json`:
```json
{
  "student_age": {
    "alice": "12",
    "bob": "13", 
    "charlie": "14"
  }
}
```

### Volume Mounting:
- JSON file is mounted at `/data/student_age.json` in the API container
- Web files are mounted at `/var/www/html` in the web container

---

## 🛠️ Development

### Local Development Setup:

1. **API Development**:
   ```bash
   cd api/
   pip install -r requirements.txt
   python3 student_age.py
   ```

2. **Frontend Development**:
   ```bash
   php -S localhost:8080 -t website/
   ```

### Building Individual Components:

```bash
# Build API image
docker build -t student-list-api .

# Run API container
docker run -d -p 5000:5000 -v $(pwd)/student_age.json:/data/student_age.json student-list-api

# Run PHP container
docker run -d -p 80:80 -v $(pwd)/website:/var/www/html php:apache
```

---

## 🔍 Troubleshooting

### Common Issues:

| **Problem** | **Solution** |
|-------------|--------------|
| API not responding | Check if port 5000 is available |
| Web interface shows errors | Verify API service is running |
| Authentication fails | Confirm username:password is `toto:python` |
| JSON data not loading | Check volume mount path |

### Debugging Commands:

```bash
# Check running containers
docker-compose ps

# View container logs
docker-compose logs api
docker-compose logs website

# Access container shell
docker-compose exec api bash
docker-compose exec website bash
```

---

## 🚀 Deployment

### Production Considerations:

1. **Environment Variables**:
   ```yaml
   environment:
     - API_USERNAME=${API_USERNAME}
     - API_PASSWORD=${API_PASSWORD}
     - FLASK_ENV=production
   ```

2. **Security**:
   - Change default credentials
   - Use HTTPS in production
   - Implement proper logging

3. **Scaling**:
   ```yaml
   deploy:
     replicas: 3
     restart_policy:
       condition: on-failure
   ```

### Health Checks:
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
  interval: 30s
  timeout: 10s
  retries: 3
```

---

## 📝 Configuration

### Environment Variables:

| **Variable** | **Service** | **Description** | **Default** |
|--------------|-------------|-----------------|-------------|
| `USERNAME` | Website | API authentication username | `toto` |
| `PASSWORD` | Website | API authentication password | `python` |
| `FLASK_ENV` | API | Flask environment mode | `development` |

### Port Configuration:

- **API**: Internal `5000`, External `5000`
- **Web**: Internal `80`, External `80`
- **Registry** (optional): External `5000` & `8080`

---

## 📄 License

This project is provided for educational and demonstration purposes. Please check with original authors for commercial usage rights.

---
