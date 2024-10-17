# Проєктування бази даних

## Модель бізнес-об'єктів

```plantuml
@startuml

entity User #cyan
entity User.id #lightCyan
entity User.name #lightCyan
entity User.email #lightCyan
entity User.password #lightCyan

User.id -r-* User
User.name --* User
User.email --* User
User.password --* User

entity Role #yellow
entity Role.name #lightYellow
entity Role.description #lightYellow
entity Role.id #lightYellow

Role.name --* Role
Role.description --* Role
Role.id --* Role

entity MediaContent #purple
entity MediaContent.id #plum
entity MediaContent.title #plum
entity MediaContent.type #plum
entity MediaContent.description #plum

MediaContent.id -r-* MediaContent
MediaContent.title --* MediaContent
MediaContent.type --* MediaContent
MediaContent.description --* MediaContent

entity AnalysisTask #green
entity AnalysisTask.id #lightGreen
entity AnalysisTask.name #lightGreen
entity AnalysisTask.status #lightGreen
entity AnalysisTask.created_at #lightGreen

AnalysisTask.id --* AnalysisTask
AnalysisTask.name -l-* AnalysisTask
AnalysisTask.status -u-* AnalysisTask
AnalysisTask.created_at -r-* AnalysisTask

entity Report #orange
entity Report.id #peachPuff
entity Report.name #peachPuff
entity Report.content #peachPuff
entity Report.created_at #peachPuff

Report.id -u-* Report
Report.name -u-* Report
Report.content -u-* Report
Report.created_at -u-* Report

User "0.*" -u- "1.1" Role
User "1.1" -- "0.*" AnalysisTask
MediaContent "0.*" -- "1.1" User
AnalysisTask "1.1" -- "0.*" Report

entity Project #black
entity Project.id #gray
entity Project.name #gray
entity Project.description #gray
entity Project.createdAt #gray

Project.id -u-* Project
Project.name -u-* Project
Project.description -u-* Project
Project.createdAt -u-* Project

entity TaskContent #red
User "1,1" -- "0,*" Project : owner
MediaContent "1,1" -- "0,*" TaskContent
AnalysisTask "1,1" -- "0,*" TaskContent

AnalysisTask "0,*" -- "1,1" Project

@enduml
```

## ER-модель
//task2

## Реляційна схема

![](https://github.com/rmerezha/test_db/blob/main/db.png)
