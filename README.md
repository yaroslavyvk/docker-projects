# DevOps Docker Module Project

## Introduction

As part of my DevOps course, I have completed a Docker module that focuses on practical applications of Docker in a real-world setting. This project consists of three key tasks:

1. **[Docker Volumes with MySQL Container](#task-1-docker-volumes-with-mysql-container)**
2. **[Docker Compose Setup](#task-2-docker-compose-setup)**
3. **[External Configurations and Environment Variables](#task-3-external-configurations-and-environment-variables)**

Each task builds upon the previous one, showcasing essential Docker skills that are highly valuable in a professional environment. All these tasks were based on and implemented, where possible, according to the principles of the [12-Factor App](https://12factor.net/).

---

## Task 1: Docker Volumes with MySQL Container

### Objective

- **Create a MySQL database container using Docker.**
- **Initialize the database and user using environment variables.**
- **Attach a volume for data persistence.**
- **Build and push the Docker image to Docker Hub.**
- **Connect a Django application to the MySQL database.**

### What I Did

- **Dockerized a MySQL Database:**
  - Created a `Dockerfile` based on the official MySQL image.
  - Used `ENV` variables to initialize the `app_db` database and the `app_user` with password `1234`.
- **Built and Tagged the Image:**
  - Built the image with the tag `mysql-local:1.0.0`.
- **Data Persistence with Volumes:**
  - Ran the MySQL container with a Docker volume attached to `/var/lib/mysql` for persistent storage.
- **Pushed to Docker Hub:**
  - Uploaded the image to my [Docker Hub repository](https://hub.docker.com/repository/docker/yaroslavya27/mysql-local).
- **Connected Django Application:**
  - Updated the Django `settings.py` to connect to the MySQL container.
  - Built the Django app image and pushed it to [Docker Hub](https://hub.docker.com/repository/docker/yaroslavya27/todoapp).

### Alignment with 12-Factor App Principles

- **III. Config:** Stored configuration in the environment, separating it from code.
- **VI. Processes:** The app is executed as one or more stateless processes, with persistent data stored in a stateful backing service (MySQL with volumes).

### Why This Is Useful at Work

- **Containerized Databases:** Simplifies deployment and scaling of database services.
- **Environment Configuration:** Using environment variables for setup promotes flexibility and security.
- **Data Persistence:** Ensures that data remains intact across container restarts, a critical feature for production databases.
- **Inter-Container Networking:** Demonstrates the ability to network multiple containers, a common practice in microservices architectures.

### Instructions

#### Building the MySQL Image

```bash
docker build -t mysql-local:1.0.0 .
```

#### Running the MySQL Container with a Volume Attached

```bash
docker run --name mysql-container -v mysql-volume:/var/lib/mysql -d mysql-local:1.0.0
```

- `--name mysql-container`: Assigns a name to the container for easier reference.
- `-v mysql-volume:/var/lib/mysql`: Attaches a Docker Volume named `mysql-volume` for data persistence.
- `-d`: Runs the container in detached mode.

#### Building the Django Application Image

```bash
docker build -t django-app:1.0 .
```

#### Running the Django Application Container

```bash
docker run -d -p 8001:8000 --name django-server --link mysql-container:mysql django-app:1.0
```

- `-p 8001:8000`: Maps port 8000 in the container to port 8001 on the host machine.
- `--link mysql-container:mysql`: Links the app container to the MySQL container.
- `--name django-server`: Assigns a name to the container.

#### Accessing the Application

- Navigate to **[http://localhost:8001](http://localhost:8001)** in your web browser.

### Docker Hub Repositories

- **MySQL Image:** [mysql-local:1.0.0](https://hub.docker.com/repository/docker/yaroslavya27/mysql-local)
- **Django App Image:** [todoapp:2.0.0](https://hub.docker.com/repository/docker/yaroslavya27/todoapp)

### Link to Solution

- **Repository:** [Task 1 Solution](https://github.com/yaroslavyvk/devops_todolist_docker_core_task_2_volumes.git)

---

## Task 2: Docker Compose Setup

### Objective

- **Create a `docker-compose.yml` file to orchestrate the MySQL and Django containers.**
- **Refactor the application to run migrations at startup.**
- **Ensure data persistence with Docker volumes.**

### What I Did

- **Developed `docker-compose.yml`:**
  - Defined services for both the MySQL database and the Django application.
- **Refactored Application Startup:**
  - Removed `RUN python manage.py migrate` from the Dockerfile.
  - Updated the `ENTRYPOINT` to run migrations and start the server:

    ```dockerfile
    ENTRYPOINT ["sh", "-c", "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"]
    ```

- **Data Persistence:**
  - Configured volumes for both MySQL and the Django application to ensure data is retained.
- **Updated Documentation:**
  - Added instructions on how to run and stop the application using Docker Compose.

### Alignment with 12-Factor App Principles

- **II. Dependencies:** Used a `requirements.txt` file to specify Python dependencies.
- **V. Build, Release, Run:** Separated build and run stages using Docker Compose.
- **VIII. Concurrency:** Enabled scaling by defining multiple services.

### Why This Is Useful at Work

- **Service Orchestration:** Docker Compose simplifies managing multi-container applications.
- **Automated Migrations:** Ensures the database schema is up-to-date every time the application starts.
- **Improved Workflow:** Streamlines development and deployment processes, reducing setup time.
- **Scalability:** Makes it easier to scale services independently.

### Instructions

#### Starting the Application

```bash
docker-compose up
```

- To run in detached mode:

  ```bash
  docker-compose up -d
  ```

#### Stopping the Application

```bash
docker-compose down
```

#### Accessing the Application

- Navigate to **[http://localhost:8000](http://localhost:8000)** in your web browser.

### Link to Solution

- **Repository:** [Task 2 Solution](https://github.com/yaroslavyvk/devops_todolist_docker_core_task_3_docker_compose.git)

---

## Task 3: External Configurations and Environment Variables

### Objective

- **Configure the application to use environment variables for database settings.**
- **Update Docker Compose to pass these variables to the containers.**
- **Implement semantic versioning for Docker images.**

### What I Did

- **Updated `docker-compose.yml`:**
  - Added environment variables for database configuration:

    ```yaml
    environment:
      ENGINE: django.db.backends.mysql
      NAME: app_db
      USER: app_user
      PASSWORD: 1234
      HOST: mysql
      PORT: 3306
    ```

- **Modified `settings.py`:**
  - Updated the `DATABASES` section to read from environment variables:

    ```python
    DATABASES = {
        'default': {
            'ENGINE': os.environ.get('ENGINE'),
            'NAME': os.environ.get('NAME'),
            'USER': os.environ.get('USER'),
            'PASSWORD': os.environ.get('PASSWORD'),
            'HOST': os.environ.get('HOST'),
            'PORT': os.environ.get('PORT'),
        }
    }
    ```

- **Semantic Versioning:**
  - Tagged Docker images with version numbers (e.g., `todoapp:2.1.0`).
- **Ensured Application Functionality:**
  - Tested the application to confirm it works seamlessly with the new configurations.

### Alignment with 12-Factor App Principles

- **III. Config:** Strictly separated config from code, storing it in environment variables.
- **IV. Backing Services:** Treated the database as an attached resource, making it easy to swap out if needed.
- **VII. Port Binding:** The service is self-contained and exports HTTP as a service by binding to a port.

### Why This Is Useful at Work

- **Security and Flexibility:** Using environment variables for configurations enhances security and allows for easy changes without modifying code.
- **Consistency Across Environments:** Simplifies deployment to different environments (development, staging, production).
- **Version Control:** Semantic versioning aids in tracking changes and rolling back if necessary.
- **Best Practices Compliance:** Aligns with industry standards for containerized applications.

### Instructions

#### Running the Application with External Configurations

```bash
docker-compose up
```

- The application will automatically read the environment variables specified in `docker-compose.yml`.

#### Accessing the Application

- Navigate to **[http://localhost:8000](http://localhost:8000)** in your web browser.

### Link to Solution

- **Repository:** [Task 3 Solution](https://github.com/yaroslavyvk/devops_todolist_docker_core_task_4_externalize_configs.git)

---

## Conclusion

Through these tasks, I have demonstrated:

- **Containerization of Services:** Ability to dockerize applications and databases.
- **Data Persistence:** Using Docker volumes to maintain data integrity.
- **Service Orchestration:** Managing multi-container applications with Docker Compose.
- **Configuration Management:** Implementing external configurations for flexibility and security.
- **Version Control:** Using semantic versioning for Docker images.

All these tasks were based on and implemented, where possible, according to the principles of the [12-Factor App](https://12factor.net/). This ensures that the application is scalable, maintainable, and portable across different environments, which is essential for modern cloud-native applications.

---

Feel free to explore each task's repository for detailed code and additional documentation.

- **Task 1 Repository:** [Task 1 Solution](https://github.com/yaroslavyvk/devops_todolist_docker_core_task_2_volumes.git)
- **Task 2 Repository:** [Task 2 Solution](https://github.com/yaroslavyvk/devops_todolist_docker_core_task_3_docker_compose.git)
- **Task 3 Repository:** [Task 3 Solution](https://github.com/yaroslavyvk/devops_todolist_docker_core_task_4_externalize_configs.git)

---
