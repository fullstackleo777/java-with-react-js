Here's a guide to set up a **GitHub repository** for your **Java + React full-stack project**, including **CI/CD** using **GitHub Actions**.

### 🏗 **Step 1: Set Up Your GitHub Repository**
1. **Create a new repository** on GitHub (e.g., `my-java-react-app`).
2. **Clone** the repository to your local machine:
    ```sh
    git clone https://github.com/your-username/my-java-react-app.git
    cd my-java-react-app
    ```

3. **Add the Project Files**:  
   Copy the project structure we discussed earlier into this repository (i.e., the `backend/`, `frontend/`, and `docker-compose.yml`).

4. **Push the Changes** to GitHub:
    ```sh
    git add .
    git commit -m "Initial commit - Java + React full-stack app"
    git push origin main
    ```

---

## 🚀 **Step 2: Set Up CI/CD Pipeline with GitHub Actions**

GitHub Actions is a powerful tool for automating build, test, and deployment pipelines. Below, we'll set up two pipelines:
1. **Java Backend CI** (Maven build and tests)
2. **React Frontend CI** (npm install and tests)

### 📌 **Step 3: Backend CI Workflow**
1. In the root of your project, create a `.github/workflows/backend.yml` file.
2. Add the following configuration to automatically build and test the backend on every push to `main`.

#### `.github/workflows/backend.yml`
```yaml
name: Java Backend CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Java
        uses: actions/setup-java@v2
        with:
          java-version: '17'

      - name: Cache Maven dependencies
        uses: actions/cache@v2
        with:
          path: ~/.m2/repository
          key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
          restore-keys: |
            ${{ runner.os }}-maven-

      - name: Build with Maven
        run: mvn clean package -DskipTests

      - name: Run unit tests
        run: mvn test
```

---

### 📌 **Step 4: Frontend CI Workflow**
1. Create another workflow for the **React frontend**. In the `.github/workflows` directory, create a file called `frontend.yml`.
2. Add the following configuration to build the frontend with npm and run the tests.

#### `.github/workflows/frontend.yml`
```yaml
name: React Frontend CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'

      - name: Cache Node modules
        uses: actions/cache@v2
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm run test -- --coverage
```

---

## 🎉 **Step 5: Docker CI/CD with GitHub Actions**
If you want to **deploy your app** (e.g., on AWS, DigitalOcean, etc.), you can set up Docker deployment in your GitHub Actions workflow. You can add steps to build and push Docker images to a container registry like **Docker Hub** or **Amazon ECR**.

### 📌 **Example Docker Build and Push to Docker Hub**

1. Create a new workflow `.github/workflows/docker.yml`.
2. Add this configuration to automatically build and push your Docker images:

#### `.github/workflows/docker.yml`
```yaml
name: Docker CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  docker-build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image for backend
        uses: docker/build-push-action@v2
        with:
          context: ./backend
          push: true
          tags: your-docker-username/backend:latest

      - name: Build and push Docker image for frontend
        uses: docker/build-push-action@v2
        with:
          context: ./frontend
          push: true
          tags: your-docker-username/frontend:latest
```

**Make sure to add your Docker Hub credentials as secrets**:
1. In your GitHub repository, go to **Settings > Secrets**.
2. Add secrets for `DOCKER_USERNAME` and `DOCKER_PASSWORD` to authenticate with Docker Hub.

---

## 📦 **Step 6: Push Your Changes**
Once all workflows are in place, push them to GitHub:
```sh
git add .github/
git commit -m "Add CI/CD pipelines for backend, frontend, and Docker"
git push origin main
```

---

## 🎯 **Step 7: Monitor Builds**
- Go to the **Actions** tab in your GitHub repository to see the progress of the CI/CD pipelines.
- If everything is set up correctly, every push to `main` or a pull request will trigger the workflows to build and test both backend and frontend.

---

## 💡 **Final Thoughts**
Now you have a **fully automated CI/CD pipeline** for your **Java + React full-stack app**. Every time you push new changes, the workflows will automatically:

- **Build** the backend and frontend.
- **Run unit tests**.
- **Build and push Docker images** (optional).