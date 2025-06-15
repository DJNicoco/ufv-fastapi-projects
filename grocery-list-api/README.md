# Assignment 3

## Project description and Theme

Task Tracker is a simple and efficient task management web application built using FastAPI. It allows users to create, manage, and track their daily tasks with authentication and authorization features.

This project uses FastAPI, PostgreSQL, SQLAchemy, JWT authentification, and Docker.

## Description of the integration implemented

My application integrates the Google Calendar API to provide users with a smoother way to track task deadlines. <br>
This integration allows users to:
 - Automatically sync task due dates to their Google Calendar.
 - Receive reminders & notifications from Google Calendar.
 - Access their tasks from any device with Google Calendar installed.
 - Manage deadlines more efficiently without manually adding events.

By implementing this feature, it enhances time management and overall productivity, making the app more useful for users.

## Setup and installation instructions

Clone the repository: <br>
- git clone https://gitlab.com/your-gitlab-repo-link <br>
- cd project

Create and activate a virtual environment: <br>
- python -m venv venv
- source venv/bin/activate

Install dependencies: <br>
- pip install -r requirements.txt

Set up the database: <br>
1. Apply migrations: alembic upgrade head
2. I used PostgreSQL so I included a .env file. <br>

.env:
DATABASE_URL=postgresql://username:password@localhost/project_db
SECRET_KEY=your_secret_key_here <br>
ALGORITHM=HS256 <br>
ACCESS_TOKEN_EXPIRE_MINUTES=30

Running the application (This has all the updated functions): <br>
 -  uvicorn main:app --reload --host 0.0.0.0 --port 8000

Running the application with dockerfile: <br>
 1. Build the docker image: docker build -t my-fastapi-app .
 2. Run the container: docker run -p 8000:8000 my-fastapi-app

Running the application with docker compose: <br>
1. Created a docker-compose.yml
2. Updated .env to use DATABASE_URL=postgresql://myuser:mypassword@db:5432/tasktracker 
3. Confirm docker compose is running and then build and run the container: <br>
 - docker ps
 - docker-compose up --build

### If problems arise, here are the solutions:

If running my application gives an error "no module named "fastapi", activate the venv again entering "source venv/bin/activate".
 - (venv) (venv) nicole@DESKTOP-UMEP1K3:~/project-1$ python -m uvicorn main:app --host 0.0.0.0 --port 8000 --reload

If running docker container doesn't work, enter docker ps and find the container id that is running to stop it and then build docker again and run it.

- docker ps
- docker stop <container_id>
- docker build -t my-fastapi-app .
- docker run -d -p 8000:8000 my-fastapi-app

#### The database schema can be found in ProjectPlan.md and the ERD Diagram-1.png file in this repository.

## Features implemented

User Authentication:
 - User Signup & Login using JWT authentication
 - Passwords securely hashed with bycrypt

Role-Based Access Control:
 - Regular Users can only manage their tasks.
 - Admins can access and manage all tasks.

Task Management:
 - Create, Read, Update, Delete (CRUD) tasks.
 - Assign priority levels (Low, Medium, High).
 - Set task due dates.

Task Filtering & Pagination:
 - Search tasks by title.
 - Filter by status.
 - Paginate results for efficiency.

## API documentation

Authorization not required: <br>
User Signup: POST /api/auth/signup <br>
User Login: POST /api/auth/login <br>

Authorization required (Bearer Token needed): <br>
Get logged-in user: GET /api/users/me <br>
Create a new user: POST /api/tasks <br>
Get all tasks: GET /api/tasks <br>
Get a specific task: GET /api/tasks/{id} <br>
Update a task: PUT /api/tasks/{id} <br>
Delete a task: DELETE /api/tasks/{id}

## New API Endpoints 

#### Assign Task to User

Endpoint: POST /api/tasks/{id}/assign <br>

Request Body (JSON): 
```
{ 
    "user_id": "123e4567-e89b-12d3-a456-426614174000" 
}
```

Response (Success 200): 
```
{ 
    "message": "Task 456 assigned to User 123"
}
```
Response (Error 403 - Unauthorized): 
```
{ 
    "detail": "You do not have permission to assign this task" <br>
}
```

Endpoint: GET /api/notifications <br>

Request Example: 
```
curl -X 'GET' \
  'http://localhost:8000/api/notifications' \
  -H 'Authorization: Bearer <access_token>'
```

Response Example (returns a list of notifications): 

```json
{
    "notifications": [
        {
            "id": "b914d22d-819d-45a9-b9d1-dfbd283d8298",
            "user_id": "f97b564b-8961-4b42-a072-72cc018baed3",
            "message": "You have a new task assigned to you.",
            "timestamp": "2025-03-29T10:30:00+00:00",
            "read": false
        },
        {
            "id": "a23c5fb5-04d0-4741-9209-1e3f0c50c728",
            "user_id": "f97b564b-8961-4b42-a072-72cc018baed3",
            "message": "Task deadline approaching in 2 days.",
            "timestamp": "2025-03-28T08:00:00+00:00",
            "read": false
        }
    ]
}
```

## Screenshots of key interfaces & improvements

### Home Page:

![alt text](image-1.png)<br>


### Log-In Page:

![alt text](image.png) <br>

### Reset Password Page:

![alt text](image-2.png) <br>

### Sign-Up Page:

![alt text](image-3.png) <br>
![alt text](image-4.png) <br>

### Deleting account if the user can't log in:

![alt text](image-5.png) <br>

### Dashboard Page:

### This is the dashboard after logging in:
![alt text](image-6.png) <br>

### This is the dashboard after adding task:
![alt text](image-7.png) <br>

## References:
Tiangolo. (n.d.). Templates (Jinja2) - FastAPI. FastAPI. Retrieved March 21, 2025, from https://fastapi.tiangolo.com/advanced/templates/

Tiangolo. (n.d.). Simple OAuth2 with Password and Bearer - FastAPI. FastAPI. Retrieved March 21, 2025, from https://fastapi.tiangolo.com/tutorial/security/simple-oauth2/

Tiangolo. (n.d.). SQL (Relational) Databases with FastAPI. FastAPI. Retrieved March 21, 2025, from https://fastapi.tiangolo.com/tutorial/sql-databases/

Tiangolo. (n.d.). Deploying FastAPI with Docker. FastAPI. Retrieved March 21, 2025, from https://fastapi.tiangolo.com/deployment/docker/

Tiangolo. (n.d.). Separate OpenAPI schemas in FastAPI. FastAPI. Retrieved March 21, 2025, from https://fastapi.tiangolo.com/how-to/separate-openapi-schemas/

Tiangolo. (n.d.). FastAPI features. FastAPI. Retrieved March 21, 2025, from https://fastapi.tiangolo.com/features/

Ramírez, S. (n.d.). OAuth2 with password (and hashing) and JWT tokens. FastAPI. Retrieved April 10, 2025, from https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/ 

Ramírez, S. (n.d.). OAuth2 scopes. FastAPI. Retrieved April 10, 2025, from https://fastapi.tiangolo.com/advanced/security/oauth2-scopes/
