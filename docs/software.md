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

```

## RESTfull сервіс для управління даними

