## Multi-Container Docker Application with CI/CD: Calculator App Project

#### Submission by - **Basit Tramboo**

### Project Overview

- **Brief project description:** What is the purpose of your application?

The purpose of your application is to provide a simple, interactive calculator that performs basic arithmetic operations. The application consists of two components:

1. **Frontend (React)**: A user-friendly interface that allows users to input numbers and select mathematical operations (e.g., addition, subtraction, multiplication, division).
   
2. **Backend (Python/Flask)**: A server-side component that handles the actual calculation logic, processes the user input, and returns the result.

This calculator app can be extended to support more complex mathematical operations, but its primary goal is to offer a quick and efficient tool for basic calculations. The app showcases the integration of a React frontend with a Python backend to perform dynamic calculations and provides a foundation for future enhancements such as adding scientific functions or more advanced features.


- **Which files are you implmenting? and why?:**

In calculator app project, the following  are  key files and components:

### **1. Frontend (React) Files:**
   - **`index.js`**: This is the entry point of the React application. It renders the `App` component into the DOM and serves as the starting point for building the UI of the application.
   - **`App.js`**: This file defines the main React component for the app. It includes the layout and UI elements for the calculator, such as buttons for digits and operations. It also handles the state management (e.g., current input and result).This file includes code that interacts with the Python backend. It sends user inputs (e.g., numbers and operations) to the backend and retrieves the calculated result for display.
   - **`index.css`**: This file contains the styles for the frontend of the calculator. It ensures the UI is visually appealing and properly laid out, making the buttons and display user-friendly.
   - **`Dockerfile`**: This file is used to create a Docker image for the frontend, ensuring that the app is containerized and can run in a consistent environment.

### **2. Backend (Python/Flask) Files:**
   - **`app.py`**: This is the main backend file that runs the Flask application. It includes:
     - A Flask route that listens for POST requests containing user input.
     - Logic to perform basic arithmetic operations (addition, subtraction, multiplication, division).
     - A response with the calculation result that the frontend can display.
   - **`requirements.txt`**: This file lists all the dependencies required by the backend, such as Flask. It ensures that the backend environment is set up with the correct libraries to run the Python app.
   - **`Dockerfile`**: This file is used to create a Docker image for the backend, ensuring that the app is containerized and can run in a consistent environment.

### **Why Implement These Files?**

- **Frontend (`index.js`, `App.js`, `index.css`)**: These files provide the user interface, allowing users to interact with the calculator. React enables efficient updates to the UI as users click buttons and input numbers, making the app responsive and easy to use.
  
- **Backend (`app.py`)**: The backend is responsible for processing the mathematical logic, ensuring that the calculator can handle user inputs correctly and return accurate results. Using Flask allows you to quickly create a lightweight API to interact with the frontend.

- **`requirements.txt`**: This file ensures that all necessary dependencies are installed in the Python environment, such as Flask for creating the backend server.

- **`Dockerfile`**: Docker helps you containerize the backend/frontend application, making it easier to deploy and run on different systems without compatibility issues.

These files work together to provide a complete solution, where the frontend handles user interaction and displays results, while the backend processes the calculations and communicates with the frontend through API calls.


### Docker Implementation

**Explain your Dockerfiles:**

- **Backend Dockerfile** (Python API):
    - The Dockerfile builds an image using Python 3.9 (slim).
    - It installs the required Python packages from requirements.txt.
    - It copies your backend application code into the container.
    - It sets up Flask to run in a containerized environment and expose the backend service on port 5001.
    - When the container starts, it launches the Flask app.

    This containerization approach ensures that your backend runs in an isolated, reproducible environment, regardless of the host system’s configuration


- **Frontend Dockerfile** (React App):

Here’s a summary of how the **Frontend Dockerfile** works for your React application:

### **Frontend Dockerfile Summary**:

1. **Base Image**:
   - The Dockerfile starts by using a lightweight Node.js image (e.g., `node:14-alpine`), which contains the Node.js runtime necessary for running the React application.

2. **Set the Working Directory**:
   - It sets `/app` as the working directory inside the container, where the application files will reside.

3. **Copy `package.json`**:
   - The `package.json` (and optionally `package-lock.json`) is copied into the container, allowing npm to install the dependencies listed in the file.

4. **Install Dependencies**:
   - `RUN npm install` installs all the Node.js dependencies specified in `package.json`, ensuring that the React app has all the required packages to run.

5. **Copy Application Code**:
   - The rest of the frontend application code (e.g., components, assets) is copied into the `/app` directory inside the container.

6. **Build the React App**:
   - The `RUN npm run build` command generates the production-ready build of your React application. This step creates optimized static files that can be served by a web server (e.g., `nginx`).

7. **Expose Port**:
   - The Dockerfile exposes port `3000` (default React development server port) to allow external access for testing or development purposes.

8. **Start the React App**:
   - The container will run `npm start` to start the React development server or serve the production build (if using something like `nginx` for serving).

This approach ensures that your React app is containerized, with a consistent environment for building and running the app, making it portable across different systems.    

### **Steps for Building Docker Images for Frontend and Backend**

1. **Install Docker Desktop**:
   - Install Docker Desktop on your machine from [Docker’s website](https://www.docker.com/products/docker-desktop).

2. **Build Docker Images**:
   - Open a terminal/command prompt.
   - Navigate to the **frontend** directory, then run:
     ```bash
     docker build -t frontend-image .
     ```
   - Navigate to the **backend** directory, then run:
     ```bash
     docker build -t backend-image .
     ```
These steps will containerize and run your frontend (React) and backend (Python/Flask) applications locally using Docker.

### Docker Compose YAML Configuration

### **Docker Compose YAML Configuration Breakdown**

The `docker-compose.yml` file is used to define and manage multi-container Docker applications.

```yaml
version: '3.9'

services:
  frontend:
    # Specify the build context for the frontend
    build:
      context: ./frontend
      dockerfile: Dockerfile
    # Map the host port 3000 to the container port 80 (default for Nginx)
    ports:
      - "3000:80"
    # Define dependencies if the frontend needs the backend running
    depends_on:
      - backend
    # Volume for live reloading
    # volumes:
    #   - ./frontend:/app
    networks:
      - app-network

  backend:
    # Specify the build context for the backend
    build:
      context: ./backend
      dockerfile: Dockerfile
    # Map the host port 5001 to the container port 5001
    ports:
      - "5001:5001"
    # Specify environment variables (example shown)
    environment:
      FLASK_ENV: development
      API_KEY: "your_api_key_here"
    # Volume for live reloading
    # volumes:
    #   - ./backend:/app
    networks:
      - app-network

networks:
  app-network:
    driver: bridge
```
### **Explanation of the `docker-compose.yml` File:**

1. **Version**: 
   - `version: '3.9'`: Specifies the Docker Compose version used for configuration.

2. **Frontend Service**:
   - **Build Context**: 
     - `context: ./frontend`: Specifies the directory containing the frontend Dockerfile.
     - `dockerfile: Dockerfile`: Explicitly defines the Dockerfile for building the frontend container.
   - **Ports**: 
     - `ports: "3000:80"`: Maps port 3000 on the host machine to port 80 in the container, where the React app (or Nginx) runs.
   - **Dependencies**:
     - `depends_on: backend`: Ensures that the backend service is up before the frontend starts.
   - **Networks**: 
     - `networks: app-network`: Connects the frontend container to the custom network `app-network`.

3. **Backend Service**:
   - **Build Context**: 
     - `context: ./backend`: Specifies the directory containing the backend Dockerfile.
     - `dockerfile: Dockerfile`: Explicitly defines the Dockerfile for building the backend container.
   - **Ports**:
     - `ports: "5001:5001"`: Maps port 5001 on the host machine to port 5001 in the container, where the Flask API runs.
   - **Environment Variables**:
     - `environment`: Configures environment variables like `FLASK_ENV` for development and `API_KEY` for API security.
   - **Networks**:
     - `networks: app-network`: Connects the backend container to the custom network `app-network`.

4. **Networks**:
   - **Custom Network**: 
     - `app-network`: Defines a custom bridge network for isolated communication between frontend and backend containers.

### **General Flow**:
- Both the frontend and backend containers are built from their respective directories (`./frontend` and `./backend`).
- The frontend depends on the backend and uses port 3000 on the host to interact with port 80 inside the container.
- The backend uses port 5001 for API communication.
- Both services are connected through the `app-network` bridge network, ensuring they can communicate with each other securely.

### CI/CD Pipeline (YAML Configuration)

#### **Triggers:**
- The pipeline is triggered by:
  - **Push to `master` branch**: Whenever changes are pushed to the `master` branch.
  - **Pull request to `master` branch**: When a pull request is created targeting the `master` branch.
  These triggers ensure that the pipeline runs whenever new code is merged into the main branch or when pull requests are opened/updated.

#### **Stages of the Pipeline:**

1. **Frontend Stage:**
   - **Checkout Code**: Retrieves the latest version of the code from the repository.
   - **Setup Node.js**: Sets up the Node.js environment using version `14`.
   - **Install Dependencies**: Runs `npm install` in the `./Project/frontend` directory to install necessary npm packages.
   - **Build React App**: Runs `npm run build` to build the React application and prepare it for production.

2. **Backend Stage:**
   - **Checkout Code**: Retrieves the latest version of the code from the repository.
   - **Setup Python**: Sets up the Python environment using version `3.9`.
   - **Install Dependencies**: Runs `pip install -r requirements.txt` to install the necessary Python dependencies for the backend.

3. **Docker Build and Push Stage:**
   - **Checkout Code**: Retrieves the latest version of the code from the repository.
   - **Setup Docker**: Configures Docker Buildx, enabling the multi-platform builds.
   - **Build Docker Images**:
     - **Frontend Docker Image**: Builds the Docker image for the frontend (`./Project/frontend`) and tags it with `latest`.
     - **Backend Docker Image**: Builds the Docker image for the backend (`./Project/backend`) and tags it with `latest`.
   - **Log in to Docker Hub**: Authenticates to Docker Hub using the credentials stored in GitHub Secrets.
   - **Push Docker Images**: Pushes the built frontend and backend Docker images to Docker Hub, tagging them as `latest`.

4. **Deploy Stage:**
   - **Checkout Code**: Retrieves the latest version of the code from the repository.

#### **File Location for GitHub Actions:**
- The CI/CD pipeline is configured using a YAML file located at `.github/workflows/main.yml` in the root directory of the repository. This file defines the entire CI/CD workflow and is required for GitHub Actions to function properly.


This CI/CD pipeline is designed to automate the build, test, and deployment processes for the frontend (React) and backend (Flask) services. When code changes are pushed to the `master` branch or a pull request is made, the pipeline:
1. Builds the frontend and backend services.
2. Creates Docker images for both services.
3. Pushes the images to Docker Hub for potential deployment.
4. Optionally, deploys the application.

The pipeline uses GitHub Actions for automation and stores sensitive data such as Docker Hub credentials in GitHub Secrets for secure access.


### **Assumptions:**

1. **Dockerfiles:**
   - The backend is a Flask application, and the frontend is a React application.
   - The Python backend requires the `requirements.txt` file for dependency installation.
   - The frontend uses Node.js, specifically version 14.
   - Both frontend and backend are running on default ports (e.g., 3000 for React and 5001 for Flask).

2. **docker-compose.yml:**
   - The frontend and backend services need to communicate over a custom bridge network (`app-network`).
   - There is no need for persistent volumes as the application does not require data persistence or live code reloading.
   - The backend is set to `FLASK_ENV=development` for local development, with the flexibility to change this for production use.
   - The frontend depends on the backend service being up before it starts, which is defined via `depends_on`.

3. **CI/CD Pipeline (GitHub Actions):**
   - The pipeline assumes that code is pushed to the `master` branch or a pull request is made to it.
   - The Docker images are built and pushed to Docker Hub using the latest tags (`latest`).
   - Deployment steps are placeholder and require customization based on the chosen deployment platform.
   - Docker Hub credentials are stored securely in GitHub Secrets under `DOCKER_USERNAME` and `DOCKER_PASSWORD`.
   - The pipeline assumes the applications are being developed in a Linux-compatible environment (Ubuntu for both frontend and backend).

### **Lessons Learned**

1. **Challenges Encountered:**
   - **GitHub Actions Workflow Not Triggering:** A major challenge was realizing that after updating the `github-action.yml` file, the GitHub Actions workflow did not trigger as expected. The issue was that GitHub requires the workflow YAML file to be placed specifically in the `.github/workflows` directory in the root folder. Once the file was moved to the correct location, the workflow was properly triggered.
   - **Docker Image Build and Push Failures:** Initially, there were issues with building Docker images and pushing them to Docker Hub due to improper Docker configurations or missing credentials in GitHub Secrets. Ensuring Docker was properly set up and authentication details were correctly stored in GitHub Secrets resolved these issues.

2. **Lessons About Containerization and Automation:**
   - **Understanding Containerization:** Working with Docker helped reinforce the benefits of containerization, such as creating consistent environments for both development and production, and making it easier to package and deploy applications across different platforms.
   - **CI/CD Automation:** Automating the build, test, and deployment pipelines with GitHub Actions and Docker streamlined the development process. It provided a systematic and reproducible way to deploy applications, ensuring that every change in the codebase could be automatically tested and deployed with minimal human intervention.
   - **Debugging Docker Build Processes:** During the process, I gained experience troubleshooting Dockerfiles and understanding the correct sequence of steps needed for building efficient Docker images (e.g., handling caching layers, setting the right context for builds).