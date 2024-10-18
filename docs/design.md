# Проєктування бази даних

## Модель бізнес-об'єктів 
//task1

## ER-модель
```plantuml
@startuml
package ProjectManagment {
    entity Project {
        ID: UUID
        name: TEXT
        description: TEXT
        createdAt: DATE
    }

    entity AnalysisTask {
        ID: UUID
        name: TEXT
        status: TEXT
        createdAt: DATE
    }

    entity MediaContent {
        ID: UUID
        title: TEXT
        type: TEXT
        description: TEXT
    }

    entity Report {
        ID: UUID
        name: TEXT
        content: TEXT
        createdAt: DATE
    }
}

package AccessPolicy {
    entity Role {
        RoleID: UUID
        name: TEXT
        description: TEXT
    }
}

package UserProfile {
    entity User {
        ID: UUID
        RoleID: UUID
        NAME: TEXT
        EMAIL: TEXT
        PASSWORD: BYTE
    }
}

ProjectManagment.Project "1.1" -- "0." ProjectManagment.AnalysisTask
ProjectManagment.Project "1.1" -- "0.*" ProjectManagment.MediaContent
ProjectManagment.Project "1.1" -- "0.*" ProjectManagment.Report

UserProfile.User "1.1" -- "0." AccessPolicy.Role

AccessPolicy.Role "1.1" -- "0.*" ProjectManagment.Report
@enduml
```

## Реляційна схема
//task3
