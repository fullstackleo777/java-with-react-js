Here’s a **GitHub-ready boilerplate** for a **Spring Boot (Java) + React (Vite) full-stack project** with Docker support.

---

### 🏗 **Step 1: Create Project Structure**
Run this command to set up the folders:
```sh
mkdir my-java-react-app && cd my-java-react-app
mkdir backend frontend
```

---

## 🚀 **Backend (Spring Boot - Java)**
### 📌 **Step 2: Initialize Spring Boot**
Use [Spring Initializr](https://start.spring.io/) or run:
```sh
curl https://start.spring.io/starter.zip -d dependencies=web,jpa,mysql,security -o backend.zip && unzip backend.zip -d backend
```

### 📌 **Step 3: Backend Code**
#### `backend/src/main/java/com/example/controller/UserController.java`
```java
package com.example.controller;

import com.example.model.User;
import com.example.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
@CrossOrigin(origins = "http://localhost:3000") // Enable CORS for frontend
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        return ResponseEntity.ok(userService.createUser(user));
    }
}
```

#### `backend/src/main/java/com/example/model/User.java`
```java
package com.example.model;

import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    
    public User() {}

    public User(String name) {
        this.name = name;
    }

    public Long getId() { return id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

#### `backend/src/main/java/com/example/service/UserService.java`
```java
package com.example.service;

import com.example.model.User;
import com.example.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    public User createUser(User user) {
        return userRepository.save(user);
    }
}
```

#### `backend/src/main/java/com/example/repository/UserRepository.java`
```java
package com.example.repository;

import com.example.model.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {}
```

### 📌 **Step 4: Configure Database**
#### `backend/src/main/resources/application.properties`
```properties
server.port=8080

spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=root

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

## ⚛️ **Frontend (React + Vite)**
### 📌 **Step 5: Initialize React Project**
```sh
cd ../frontend
npm create vite@latest . --template react
npm install axios react-router-dom
```

### 📌 **Step 6: Frontend Code**
#### `frontend/src/services/UserService.js`
```javascript
import axios from "axios";

const API_URL = "http://localhost:8080/api/users";

export const fetchUsers = async () => {
    const response = await axios.get(API_URL);
    return response.data;
};

export const createUser = async (name) => {
    const response = await axios.post(API_URL, { name });
    return response.data;
};
```

#### `frontend/src/components/UserList.jsx`
```javascript
import { useEffect, useState } from "react";
import { fetchUsers } from "../services/UserService";

const UserList = () => {
    const [users, setUsers] = useState([]);

    useEffect(() => {
        fetchUsers().then(setUsers);
    }, []);

    return (
        <div>
            <h1>User List</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
};

export default UserList;
```

#### `frontend/src/App.jsx`
```javascript
import UserList from "./components/UserList";

function App() {
    return (
        <div>
            <h1>React + Spring Boot App</h1>
            <UserList />
        </div>
    );
}

export default App;
```

#### `frontend/src/main.jsx`
```javascript
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(
    <React.StrictMode>
        <App />
    </React.StrictMode>
);
```

---

## 🐳 **Docker Setup**
### 📌 **Step 7: Backend `Dockerfile`**
#### `backend/Dockerfile`
```dockerfile
FROM openjdk:17
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 📌 **Step 8: Frontend `Dockerfile`**
#### `frontend/Dockerfile`
```dockerfile
FROM node:18
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

### 📌 **Step 9: Docker Compose**
#### `docker-compose.yml`
```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/mydb
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: root
    depends_on:
      - db

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend

  db:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
```

---

## 🎯 **Final Steps**
### ✅ **Step 10: Build and Run**
Run backend:
```sh
cd backend
mvn clean package -DskipTests
docker build -t backend .
docker run -p 8080:8080 backend
```
Run frontend:
```sh
cd frontend
npm install
npm run dev
```
Or use Docker Compose:
```sh
docker-compose up --build
```

---

## 🏆 **Final Thoughts**
This provides:

✔ **Spring Boot Backend (Java, REST API, MySQL, JPA)**  
✔ **React Frontend (Vite, Axios, React Router)**  
✔ **Docker & Docker Compose for Deployment**  