# Реалізація інформаційного та програмного забезпечення

## SQL-скрипт для створення на початкового наповнення бази даних

```sql

CREATE SCHEMA IF NOT EXISTS db;

CREATE TABLE IF NOT EXISTS db.role (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(64) NOT NULL UNIQUE,
    description TEXT NOT NULL
);

CREATE TABLE IF NOT EXISTS db.user (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(64) NOT NULL,
    email VARCHAR(64) NOT NULL UNIQUE,
    password VARCHAR(128) NOT NULL,
    role_id INT NOT NULL REFERENCES db.role (id) ON DELETE NO ACTION
);

CREATE TABLE IF NOT EXISTS db.media_content (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(128) NOT NULL,
    description TEXT NOT NULL,
    type VARCHAR(32) NOT NULL,
    file_path VARCHAR(128) NOT NULL UNIQUE,
    user_id INT NOT NULL REFERENCES db.user (id) ON DELETE NO ACTION
);

CREATE TABLE IF NOT EXISTS db.project (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(64) NOT NULL,
    description TEXT NOT NULL,
    created_at DATETIME NOT NULL DEFAULT NOW(),
    user_id INT NOT NULL REFERENCES db.user (id) ON DELETE NO ACTION
);

CREATE TABLE IF NOT EXISTS db.analysis_task (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(64) NOT NULL,
    status VARCHAR(64) NOT NULL,
    created_at DATETIME NOT NULL DEFAULT NOW(),
    user_id INT NOT NULL REFERENCES db.user (id) ON DELETE NO ACTION,
    project_id INT NOT NULL REFERENCES db.project (id) ON DELETE NO ACTION
);

CREATE TABLE IF NOT EXISTS db.task_content (
    media_content_id INT NOT NULL REFERENCES db.media_content (id) ON DELETE NO ACTION,
    analysis_task_id INT NOT NULL REFERENCES db.analysis_task (id) ON DELETE NO ACTION,
    PRIMARY KEY (media_content_id, analysis_task_id)
);

CREATE TABLE IF NOT EXISTS db.report (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(64) NOT NULL,
    content TEXT NOT NULL,
    created_at DATETIME NOT NULL DEFAULT NOW(),
    analysis_task_id INT NOT NULL REFERENCES db.analysis_task (id) ON DELETE NO ACTION
);

INSERT INTO db.role (name, description)
VALUES ('User Role', 'description for User Role'),
       ('Tech Expert Role', 'description for Tech Expert Role'),
       ('Media Content Analyst Role', 'description for Media Content Analyst Role');

INSERT INTO db.user (name, email, password, role_id)
VALUES
    ('Mock user1', 'test1@test.com', '$2a$12$v.HK88e3zeXbJdQptStutOTyFBitIOdSlOxIfNeOcPey/ZKjtaWPm', 1),
    ('Mock user2', 'test2@test.com', '$2a$12$6IpiXsVmylfNzPBD29YbU.bchJr9IztZpYD/A9PrwUuIn4jEFQEd2', 1),
    ('Mock user3', 'test3@test.com', '$2a$12$363l0yY4Cxy3Gj.hr7D85OJmf0qkvd.tc0VIBxn4svkLazvsbBo3S', 1),
    ('Mock user4', 'test4@test.com', '$2a$12$auMQTmDv9D7lISYaCd7ZQO3VSXUbcjQQrcxdiooQO0EJY3q2bY4hW', 1),
    ('Mock user5', 'test5@test.com', '$2a$12$nRJp1Ad6rRHfzD7l5WLKk.c7EZ/FSuADv0MsM1qYnHSE4YxcG4joO', 1);

INSERT INTO db.media_content (title, description, type, file_path, user_id)
VALUES
    ('media content 1', '...', 'jpg', 'path/to/media/content/1', 1),
    ('media content 2', '...', 'png', 'path/to/media/content/2', 2),
    ('media content 3', '...', 'pdf', 'path/to/media/content/3', 3),
    ('media content 4', '...', 'pdf', 'path/to/media/content/4', 4),
    ('media content 5', '...', 'txt', 'path/to/media/content/5', 5);

INSERT INTO db.project (name, description, user_id)
VALUES
    ('Project 1', '...', 1),
    ('Project 2', '...', 2),
    ('Project 3', '...', 3);

INSERT INTO db.analysis_task (name, status, user_id, project_id)
VALUES
    ('analysis task 1', 'pending', 1, 1),
    ('analysis task 2', 'in progress ', 2, 1),
    ('analysis task 3', 'completed', 3, 1),
    ('analysis task 4', 'paused', 1, 2),
    ('analysis task 5', 'cancelled', 1, 3);

INSERT INTO db.task_content (media_content_id, analysis_task_id)
VALUES
    (5,1),
    (2,1),
    (2,2),
    (1,2),
    (1,5);

INSERT INTO db.report (name, content, analysis_task_id)
VALUES
    ('report 1', '...', 1),
    ('report 2', '...', 2),
    ('report 3', '...', 3),
    ('report 4', '...', 4),
    ('report 5', '...', 5);


```

## RESTfull сервіс для управління даними
### Підключення до бази данних

```
import sqlite3
from src.Lab6.src.models.schema import SQL_SCHEMA

DB_NAME = "my_database.db"

def get_db_connection():
    conn = sqlite3.connect(DB_NAME)
    conn.row_factory = sqlite3.Row
    return conn

def create_tables():
    conn = get_db_connection()
    cursor = conn.cursor()
    cursor.executescript(SQL_SCHEMA)
    conn.commit()
    conn.close()

```

### Налаштування сервера

```
from fastapi import FastAPI
from api import routes
from api.middleware import ErrorHandlingMiddleware

app = FastAPI()

app.add_middleware(ErrorHandlingMiddleware)
app.include_router(routes.router)
```
###Роутинг
```
 from fastapi import APIRouter, HTTPException, status
 from api import models
 from src.Lab6.src.utils import database_operations as db_operations
 from api import exceptions
 from datetime import datetime
 from typing import List


 router = APIRouter()


 def create_report_data(report: models.Report):
     """Створює новий запис звіту."""
     created_at = datetime.now().isoformat()
     report_data = {
             'content': report.content,
             'created_at': created_at,
             'analysis_task_id': report.analysis_task_id,
             'name': report.name
         }
     return report_data


 @router.post("/reports/", response_model=models.Report, status_code=status.HTTP_201_CREATED)
 def create_report(report: models.Report):
     report_data = create_report_data(report)
     try:
         new_report_id = db_operations.insert_report(report_data)
         db_report = db_operations.fetch_report_by_id(new_report_id)
         if db_report:
              return models.Report(**db_report)
         raise exceptions.not_found_exception(f"Report with id={new_report_id} not found")
     except Exception as e:
         raise exceptions.create_error_exception(str(e))



 @router.get("/reports/", response_model=List[models.Report])
 def read_all_reports():
     try:
       db_reports = db_operations.fetch_all_reports()
       if db_reports:
           return [models.Report(**db_report) for db_report in db_reports]
       raise exceptions.not_found_exception("Reports not found")
     except Exception as e:
        raise exceptions.get_error_exception(str(e))



 @router.get("/reports/{report_id}", response_model=models.Report)
 def read_report(report_id: int):
      try:
         db_report = db_operations.fetch_report_by_id(report_id)
         if db_report:
               return models.Report(**db_report)
         raise exceptions.not_found_exception(f"Report with id={report_id} not found")
      except Exception as e:
           raise exceptions.get_error_exception(str(e))



 @router.put("/reports/{report_id}", response_model=models.Report)
 def update_report(report_id: int, report: models.Report):
     report_data = create_report_data(report)
     try:
        db_operations.update_report(report_id, report_data)
        updated_report = db_operations.fetch_report_by_id(report_id)
        if updated_report:
             return models.Report(**updated_report)
        raise exceptions.not_found_exception(f"Report with id={report_id} not found")
     except Exception as e:
         raise exceptions.update_error_exception(str(e))


 @router.delete("/reports/{report_id}", response_model=dict)
 def delete_report(report_id: int):
     try:
         db_operations.delete_report(report_id)
         return {"message": "Report deleted"}
     except Exception as e:
         raise exceptions.delete_error_exception(str(e))
```
###Логіка для CRUD операцій
```
from datetime import datetime
import sqlite3
from src.Lab6.src.database import get_db_connection

def insert_report(report_data):
    """Вставляє новий звіт."""
    conn = get_db_connection()
    cursor = conn.cursor()
    
    
    cursor.execute("""
        INSERT INTO Report (content, created_at, analysis_task_id, name)
        VALUES (?, ?, ?, ?)
    """, (report_data['content'], report_data['created_at'], report_data['analysis_task_id'], report_data['name']))

    conn.commit()
    new_id = cursor.lastrowid
    conn.close()
    return new_id


def fetch_all_reports():
    """Отримує всі звіти з бази даних."""
    conn = get_db_connection()
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM Report")
    reports = cursor.fetchall()
    conn.close()
    return reports


def update_report(report_id, report_data):
    """Оновлює звіт за його ID."""
    conn = get_db_connection()
    cursor = conn.cursor()

    cursor.execute("""
        UPDATE Report
        SET content = ?, created_at = ?, analysis_task_id = ?, name = ?
        WHERE id = ?
    """, (report_data['content'], report_data['created_at'], report_data['analysis_task_id'], report_data['name'], report_id))

    conn.commit()
    conn.close()


def delete_report(report_id):
    """Видаляє звіт за його ID."""
    conn = get_db_connection()
    cursor = conn.cursor()

    cursor.execute("DELETE FROM Report WHERE id = ?", (report_id,))
    conn.commit()
    conn.close()


def fetch_report_by_id(report_id):
    """Отримує звіт за його ID."""
    conn = get_db_connection()
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM Report WHERE id = ?", (report_id,))
    report = cursor.fetchone()
    conn.close()
    return report

def create_analysis_task(user_id, project_id, name, status):
    """Створює нове аналітичне завдання."""
    conn = get_db_connection()
    cursor = conn.cursor()
    created_at = datetime.now().isoformat()
    cursor.execute("""
        INSERT INTO AnalysisTask (created_at, user_id, project_id, name, status)
        VALUES (?, ?, ?, ?, ?)
    """, (created_at, user_id, project_id, name, status))
    conn.commit()
    new_id = cursor.lastrowid
    conn.close()
    return new_id


def create_media_content(user_id, title, type, file_path, description):
    """Створює новий медіаконтент."""
    conn = get_db_connection()
    cursor = conn.cursor()
    cursor.execute("""
        INSERT INTO MediaContent (user_id, title, type, file_path, description)
        VALUES (?, ?, ?, ?, ?)
    """, (user_id, title, type, file_path, description))
    conn.commit()
    new_id = cursor.lastrowid
    conn.close()
    return new_id
```
###Відправка відповіді
```
from fastapi import APIRouter, HTTPException, status, Depends
from api import models
from src.Lab6.src.utils import database_operations as db_operations
from datetime import datetime
from typing import List


router = APIRouter()

def create_report_data(report: models.Report):
    """Створює новий запис звіту."""
    created_at = datetime.now().isoformat()
    report_data = {
            'content': report.content,
            'created_at': created_at,
            'analysis_task_id': report.analysis_task_id,
            'name': report.name
        }
    return report_data


@router.post("/reports/", response_model=models.Report, status_code=status.HTTP_201_CREATED)
def create_report(report: models.Report):
    report_data = create_report_data(report)
    new_report_id = db_operations.insert_report(report_data)
    db_report = db_operations.fetch_report_by_id(new_report_id)
    if db_report:
        return models.Report(**db_report)
    raise HTTPException(status_code=404, detail="Report not found")


@router.get("/reports/", response_model=List[models.Report])
def read_all_reports():
    db_reports = db_operations.fetch_all_reports()
    if db_reports:
        return [models.Report(**db_report) for db_report in db_reports]
    raise HTTPException(status_code=404, detail="Reports not found")


@router.get("/reports/{report_id}", response_model=models.Report)
def read_report(report_id: int):
    db_report = db_operations.fetch_report_by_id(report_id)
    if db_report:
         return models.Report(**db_report)
    raise HTTPException(status_code=404, detail="Report not found")


@router.put("/reports/{report_id}", response_model=models.Report)
def update_report(report_id: int, report: models.Report):
    report_data = create_report_data(report)
    db_operations.update_report(report_id, report_data)
    updated_report = db_operations.fetch_report_by_id(report_id)
    if updated_report:
         return models.Report(**updated_report)
    raise HTTPException(status_code=404, detail="Report not found")


@router.delete("/reports/{report_id}", response_model=dict)
def delete_report(report_id: int):
    db_operations.delete_report(report_id)
    return {"message": "Report deleted"}
    

@router.post("/task_contents/", response_model=models.TaskContent, status_code=status.HTTP_201_CREATED)
def create_task_content(task_content: models.TaskContent):
    task_content_data = {
        'media_content_id': task_content.media_content_id,
        'analysis_task_id': task_content.analysis_task_id
    }
    db_operations.insert_task_content(task_content_data)
    db_task_content = db_operations.fetch_task_content_by_ids(task_content.media_content_id, task_content.analysis_task_id)
    if db_task_content:
        return models.TaskContent(**db_task_content)
    raise HTTPException(status_code=404, detail="TaskContent not found")


@router.get("/task_contents/", response_model=List[models.TaskContent])
def read_all_task_content():
    db_task_contents = db_operations.fetch_all_task_content()
    if db_task_contents:
          return [models.TaskContent(**db_task_content) for db_task_content in db_task_contents]
    raise HTTPException(status_code=404, detail="TaskContents not found")

@router.get("/task_contents/{media_content_id}/{analysis_task_id}", response_model=models.TaskContent)
def read_task_content(media_content_id: int, analysis_task_id: int):
    db_task_content = db_operations.fetch_task_content_by_ids(media_content_id, analysis_task_id)
    if db_task_content:
        return models.TaskContent(**db_task_content)
    raise HTTPException(status_code=404, detail="TaskContent not found")


@router.delete("/task_contents/{media_content_id}/{analysis_task_id}", response_model=dict)
def delete_task_content(media_content_id: int, analysis_task_id: int):
        db_operations.delete_task_content(media_content_id, analysis_task_id)
        return {"message": "TaskContent deleted"}
```
###Обробка помилок
```
from fastapi import FastAPI, Request, HTTPException
from fastapi.responses import JSONResponse
from starlette.middleware.base import BaseHTTPMiddleware
from typing import Callable
from http import HTTPStatus


class ErrorHandlingMiddleware(BaseHTTPMiddleware):
    def __init__(self, app: FastAPI):
        super().__init__(app)

    async def dispatch(self, request: Request, call_next: Callable):
        try:
           response = await call_next(request)
           return response
        except HTTPException as http_exception:
           response =  JSONResponse(
                status_code=http_exception.status_code,
                content={'error': http_exception.detail}
            )
           return response
        except Exception as e:
             response = JSONResponse(
                status_code=HTTPStatus.INTERNAL_SERVER_ERROR,
                content={'error': str(e)}
            )
             return response
```
###Фабрика помилок
```
from fastapi import HTTPException, status


def missing_fields_exception():
    """Помилка для відсутніх полів."""
    return HTTPException(
        status_code=status.HTTP_400_BAD_REQUEST,
        detail="Missing fields"
    )

def not_found_exception(detail: str = "Resource not found"):
    """Помилка для не знайденого ресурсу."""
    return HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail=detail
    )


def create_error_exception(detail: str = "Create error"):
    """Помилка при створенні."""
    return HTTPException(
        status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
        detail=detail
    )


def get_error_exception(detail: str = "Get error"):
    """Помилка при отриманні."""
    return HTTPException(
        status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
        detail=detail
    )

def update_error_exception(detail: str = "Update error"):
    """Помилка при оновленні."""
    return HTTPException(
        status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
        detail=detail
    )

def delete_error_exception(detail: str = "Delete error"):
    """Помилка при видаленні."""
    return HTTPException(
        status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
        detail=detail
    )
```
###SQL запити
```
QUERIES = {
    "CREATE_REPORT": """
        INSERT INTO Report (content, created_at, analysis_task_id, name)
        VALUES (?, ?, ?, ?)
    """,
    "GET_ALL_REPORTS": "SELECT * FROM Report",
    "GET_REPORT_BY_ID": "SELECT * FROM Report WHERE id = ?",
    "UPDATE_REPORT": """
        UPDATE Report
        SET content = ?, created_at = ?, analysis_task_id = ?, name = ?
        WHERE id = ?
    """,
    "DELETE_REPORT": "DELETE FROM Report WHERE id = ?"
}
```
###Коди статусів HTTP
```
from fastapi import status
HTTP_STATUS_CODES = {
  "OK": status.HTTP_200_OK,
  "CREATED": status.HTTP_201_CREATED,
  "BAD_REQUEST": status.HTTP_400_BAD_REQUEST,
  "NOT_FOUND": status.HTTP_404_NOT_FOUND,
  "INTERNAL_SERVER_ERROR": status.HTTP_500_INTERNAL_SERVER_ERROR,
}
```



