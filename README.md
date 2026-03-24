# SENG513W25-FinalProject-CodeClash Development Guide

## Frontend Development

If you are working on styling-related frontend development and don't need the backend server, you can run the application separately:

```sh
cd frontend
npm run dev
```

This will start the Vite development server for hot reloading.

## Backend Setup

The backend uses a JAR file to create the Spring Boot application. This JAR file is essentially a packaged version of the entire Spring project.

### Building the JAR File

Before running Docker Compose, you need to build the JAR file. Since the JAR file uses environment variables, ensure that you have an `.env` file inside the project root folder, and since the jar file is being built in the code_clash folder add another env file here as well `/backend/code_clash/`.

#### Example `.env` file

```sh
# Database credentials
DB_USERNAME=myuser
DB_PASSWORD=secret

# PostgreSQL URL
SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/codeClashdb
```

To build the JAR file, make sure you're inside the `/backend/code_clash/` folder, then run:

```sh
mvn clean install -DskipTests
```

Instead of:

```sh
mvn clean install
```

Skipping tests is necessary because the PostgreSQL database won't be available until the JAR file is built, and `clean install` would fail due to a missing database connection.

If you don’t have Maven installed on your system, run:

```sh
./mvnw clean install -DskipTests
```

## Running with Backend

If you need the backend, you'll need to run the Docker containers:

### Building and Running Containers

Below is the first command youre going to run

```sh
docker-compose up --build
```

This builds the containers and starts them. Use Control-C to stop running the docker containers.

```sh
docker-compose up
```

Once the containers are built, the data from those sessions will be stored in Docker volumes. Running this without `--build` will use the existing data.

```sh
docker-compose down -v
```

If you want to start fresh (resetting the database), use this command. It will delete both the containers and their volumes.

```sh
docker-compose down
```

This only removes the containers, keeping the stored data (volumes).

## Accessing the Backend

The backend's base route can be accessed using this environment variable:

```js
import.meta.env.VITE_SERVER_HOSTNAME;
```

This variable is where you'll be making your requests to and has already been set inside `docker-compose.yaml`.

### Example: Register a New User

To register a new user, send a `POST` request to the `/auth/register` endpoint using the base URL from the environment variable.

#### JavaScript Example (Fetch API):

```javascript
const registerUser = async () => {
  try {
    const response = await fetch(
      `${import.meta.env.VITE_SERVER_HOSTNAME}/auth/register`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          username: "exampleUser",
          password: "securePassword123",
        }),
      }
    );

    if (!response.ok) {
      throw new Error("Failed to register user");
    }

    const data = await response.json();
    console.log("Success:", data);
  } catch (error) {
    console.error("Error:", error);
  }
};

// Call the function to register a user
registerUser();
```

---
