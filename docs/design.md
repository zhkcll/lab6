# Проєктування бази даних

## Модель бізнес-об'єктів 
//task1

## ER-модель
```plantuml
@startuml
package ProjectManagment {
    entity ProjectMember {
        ID: UUID
        RoleID: UUID
    }

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

ProjectManagment.Project "1.1" <-- "0." ProjectManagment.ProjectMember
ProjectManagment.Project "1.1" <-- "0." ProjectManagment.Task
ProjectManagment.Project "1.1" <-- "0.*" ProjectManagment.PaymentData
ProjectManagment.Project "1.1" <-- "0.*" ProjectManagment.Review

UserProfile.User "1.1" <-- "0." AccessPolicy.Member

AccessPolicy.Member "1.1" <-- "0.*" ProjectManagment.ProjectMember
@enduml
```

## Реляційна схема
//task3
