# Task Management API

A simple REST API for managing tasks, built with Python and FastAPI.

This project was created as part of the FlyRank internship task. It demonstrates full CRUD operations using FastAPI with PostgreSQL as the database. The API and database run together using Docker Compose.

## Features

- FastAPI REST API
- PostgreSQL database
- Docker and Docker Compose
- Create, read, update, and delete tasks
- Input validation
- Proper HTTP status codes
- Automatic Swagger UI documentation
- Persistent PostgreSQL data using a Docker volume
- Environment variable configuration

## Requirements

- Docker Desktop
- Docker Compose

## Running the API

The entire application stack can be started with one command:

```bash
docker compose up
```

The API will be available at:

```text
http://127.0.0.1:8000
```

Swagger UI is available at:

```text
http://127.0.0.1:8000/docs
```

To stop the application, press `Ctrl + C`.

The containers can also be stopped and removed with:

```bash
docker compose down
```

The PostgreSQL data is stored in a Docker volume, so task data persists when the containers are stopped and started again.

## Environment Variables

The application uses the `DATABASE_URL` environment variable to connect to PostgreSQL.

A sample environment file is included in the project as:

```text
.env.example
```

Copy the example file to `.env` and configure the database connection:

```powershell
Copy-Item .env.example .env
```

The database URL is:

```text
DATABASE_URL=postgres://postgres:dev@localhost:5432/tasks
```

The `.env` file is ignored by Git and should not be committed to the repository.

When the application runs through Docker Compose, the FastAPI container connects to the PostgreSQL container using the Docker Compose service name `db`.

## Database

The API uses PostgreSQL for persistent task storage.

PostgreSQL runs in its own Docker container using the official PostgreSQL image. The FastAPI application connects to PostgreSQL through the Docker Compose network.

The database configuration is:

```text
Database: tasks
User: postgres
Password: dev
Host: db
Port: 5432
```

The database uses a persistent Docker volume named `taskdata`.

This allows task data to remain available after the containers are stopped and started again.

The `tasks` table is created automatically when the application starts. If the table is empty, three example tasks are inserted automatically.

## Database Verification

The PostgreSQL database can be accessed directly through the running database container:

```powershell
docker compose exec db psql -U postgres -d tasks
```

The available tables can be checked with:

```sql
\dt
```

The stored tasks can be viewed with:

```sql
SELECT * FROM tasks;
```

Example database output:

```text
 id |       title        | done
----+--------------------+------
  1 | Learn PostgreSQL   | f
  2 | Build the database | f
  3 | Test the API       | f
  5 | Stage 4 docker test| f
```

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Returns basic information about the Task API |
| GET | `/health` | Checks whether the API is running |
| GET | `/tasks` | Returns all tasks |
| GET | `/tasks/{id}` | Returns a single task by ID |
| POST | `/tasks` | Creates a new task |
| PUT | `/tasks/{id}` | Updates an existing task |
| DELETE | `/tasks/{id}` | Deletes an existing task |

## Example: Health Check

### Request

```bash
curl -i http://127.0.0.1:8000/health
```

### Response

```text
HTTP/1.1 200 OK
content-type: application/json

{"status":"ok"}
```

## Example: Get Tasks

### Request

```bash
curl -i http://127.0.0.1:8000/tasks
```

### Response

```text
HTTP/1.1 200 OK
content-type: application/json

[{"id":1,"title":"Learn PostgreSQL","done":false},{"id":2,"title":"Build the database","done":false},{"id":3,"title":"Test the API","done":false},{"id":5,"title":"Stage 4 docker test","done":false}]
```

## CRUD Operations

### Create

```text
POST /tasks
```

Example request:

```json
{
  "title": "Go for a walk"
}
```

Returns `201 Created`.

### Read

```text
GET /tasks
GET /tasks/{id}
```

Returns the requested task or tasks.

### Update

```text
PUT /tasks/{id}
```

Example request:

```json
{
  "title": "Finish the task",
  "done": true
}
```

Returns `200 OK`.

### Delete

```text
DELETE /tasks/{id}
```

Returns `204 No Content` when successful.

## Error Handling

The API returns appropriate HTTP status codes for invalid requests.

- `400 Bad Request` — missing or empty task title
- `404 Not Found` — task does not exist
- `201 Created` — task successfully created
- `200 OK` — task successfully retrieved or updated
- `204 No Content` — task successfully deleted

## Swagger UI

The API includes automatically generated interactive documentation using FastAPI's Swagger UI.

Open:

```text
http://127.0.0.1:8000/docs
```

### Swagger Screenshot

![Swagger UI](swagger.png)

## PostgreSQL Database Screenshot

The PostgreSQL database was inspected directly using `psql`.

The screenshot shows the `tasks` table using `\dt` and the stored task data using `SELECT * FROM tasks`.

![PostgreSQL Database](database1.png)

## Persistence Verification

The PostgreSQL persistence was tested by creating a task through the API and then stopping and restarting the Docker Compose stack.

The task remained available after restarting the containers, confirming that PostgreSQL data is persisted using the Docker volume.

## Project Structure

```text
task-management-api/
├── main.py
├── database.py
├── README.md
├── Dockerfile
├── compose.yaml
├── requirements.txt
├── .dockerignore
├── .env.example
├── swagger.png
├── database1.png
└── .gitignore
```

The `.env` file and local database files are not tracked by Git.

## GitHub Repository

Repository: `haneen09/task-management-api`
