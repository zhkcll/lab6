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
