# Project Management System 

Project Management System is a tool that helps working with multiple project management methodologies. 


# Methodologies

The main inspiration for project management systems is project management methodologies.

## Agile Project Management

### Project Planing 
The end goal will be determined in the project description. But I think this shouldn't be plain text. We can use a md file for write a description. This description will include main project goals. And it can also include diagrams charts and images about the project. We save this description in CMS servise. Because description field works like wordpress website. For this reason we have a CMS service for pages and diagrams. Also in this stage, subjects(teams that working on specific field of the project) will be created. And empleeyes will be assigned for this subjects.  	  			
- Every subject has an team leader. Team leader can only assign empoyees to a subject.
-  For assigning employees for a subject, employee   
must have been added by the organization. 
- If a user is directly connected to an organization and working for them, a user account will connect to the organization. User account and organization including many2many connections, due to a user can be working at one company and for its organization. 
- But at the same time user can have a side project with other people. For managing this project easily than can create another organization account. 
- From the project setting, they can make their project read-only for all users. If they want, they can give issue create permissions for external issues. These issues can be bugs or needing extra features. With this platform, the organization can easily manage user feedback. (Agile manifesto 3-4.  principle)
- Or you can create guest organization accounts only for customers. And customers can directly watch project development phases. And they can explain, if some pieces of application not fully understood. (Agile manifesto 3-4.  principle)
- Under organization with five users, it's free. But if it's more than that, a small price is needed monthly.
- Projects and subjects have their own chat channels. (Agile manifesto 1.  principle)

#### Diagram of User Service Database (postgresql):
```mermaid
erDiagram  

 TOKEN {
 uint64 id
 string token
 uint64 user_id
 } 
 USER ||--o{ TOKEN : "" 
 USER ||--o{  ORGANIZATION_ACCOUNT  : "can join multiple organization" 
 USER {  
 uint64 id 
 string username
 string name
 string lastname
 string email
 string password
 string avatar_link
 time created_at
 time updated_at
 time deleted_at
 }
```
settings example: Work in progress limit, 

#### Diagram of Organization Service Database (postgresql):

```mermaid
erDiagram 
ORGANIZATION_ACCOUNT 

ORGANIZATION_ACCOUNT ||--o{ TEAM_MEMBERS  : ""
TEAM ||--o{ TEAM_MEMBERS : ""

TEAM_MEMBERS {
uint64 organization_account_id
uint64 subject_id
uint32 salary
byte permissions
}

TEAM {
uint64 team_id
byte permissions
uint64 project_id
}

PROJECT ||--o{ TEAM : ""
TEAM ||--|| CHAT_SERVICE_TEAM_CHANNEL: ""

TEAM_MEMBER_ROLE {
string role_name
uint64 team_member_id
}

TEAM_MEMBERS ||--o{ TEAM_MEMBER_ROLE : ""
TEAM_MEMBERS ||--o{ ISSUES : ""

ORGANIZATION_ROLES ||--o{ TEAM_MEMBER_ROLE : ""
ORGANIZATION  ||--o{  ORGANIZATION_ACCOUNT  : "has employees or contribitors"  
 ORGANIZATION  {  
 uint64 id
 string organization_name  
 string name 
 string sector  
 uint64 ceo
 uint16 employee_count
 byte settings
 byte default_user_permissions
 time last_payment
 time created_at
 time updated_at
 time deleted_at
 string created_by
 string updated_by
 string deleted_by
 
 }  
  ORGANIZATION ||--o{  ORGANIZATION_ROLES  : "contains custom rules" 
ORGANIZATION_ROLES {
uint64 id
uint64 organization_id
string role_id
string hex_color
byte user_permissions
time created_at
time updated_at
time deleted_at

}
 ORGANIZATION_ACCOUNT  {  
 uint64_PK user_id  
 uint64_PK organization_id  
 byte permissions
 }  
 ORGANIZATION ||--o{  PROJECT  : "contains projects"  
 ORGANIZATION_ACCOUNT ||--o{  PROJECT  : "has project responseible"  
 USER ||--o{  ORGANIZATION_ACCOUNT  : "can join multiple organization" 
 CHAT_SERVICE_PROJECT_CHANNEL ||--||  PROJECT  : "has" 
```

### Project Roadmap

Project roadmap will started created from project defination. Than Project Manager started to creating sprints, subjects and issues. After that project parts starts to create. Parts are project sub features can be belonging to subject and sprint. And this parts have isseues. Isseues can be added to parts after that parts are created.

Users will have personal workload feature

#### Project: Organization Projects
- Name : Name of the project  [Min:1 Max:36 Required]
- Description: Summary of the project [Min:1,Max:255 Required]
- Documentation File: Detailed documentation of the project. It will be store project details with a basic CMS service. With using CMS service, user can create complex navigation structure around multiple files and contents. CMS service will create a folder for every project and create pages inside that.
- Project Settings: Settings of the project. It will store as binary format.
- Organization: Organization to which the project belongs
- Project Responsible : Super user of project. Has all create update and get permissions
- Start Date: Project planned start date
- Due Date: Project planned end date

#### Sprint: Project Sprints
- Name: Defined name of sprint [Min:1 Max:36 Required]
- Project: Project to whichthe project belongs.
- Start: Sprint start time.
- End: Sprint end time
- Next Spirint
- Previous Sprint

#### Subject: Substract part

Issue Dependecy Types : child of, blocker of,new version of, new feature of , Finish to start (FS), Finish to finish (FF), Start to start (SS), Start to finish (SF)
Subject Dependecy Types : child of, new version of, next stage of, Finish to start (FS), Finish to finish (FF), Start to start (SS), Start to finish (SF)

#### Diagram of Project Service Database (postgresql):
```mermaid
erDiagram  
 PROJECT {
	uint64 id
	string name
	sting description
	uint64 documentation_file_id
	byte settings
	uint64 organization_id
	uint64 project_responsible_id
	string scope_rules
	money budget
	time start_date
	time due_date
	time created_at
	time updated_at
	tiem deleted_at
 }
ORGANIZATOIN ||--o{  PROJECT: "has"
PROJECT ||--o{  SPRINT  : "has" 
ORGANIZATION_ACCOUNT }|--o|  PROJECT: "project responsible" 
CONTENT_SERVICE||--||  PROJECT: "documentation file" 
HISTORY_SERVICE||--||  PROJECT: "project change history" 

PROJECT||--o{  ADDTIONAL_CUSTOMER_REQUEST : "project can have multiple user request" 

ADDTIONAL_CUSTOMER_REQUEST {
uint64 id
string title
string description
time created_at
time updated_at
time deleted_at
uint64 created_by
}

SPRINT {
uint64 id
uint64 name
uint64 project_id
money budget
uint8 status
uint8 sprint_queue_number
time start
time end
}

PROJECT ||--o{  SUBJECT : "has"

SUBJECT |o--o{ ISSUES:"has"
SPRINT |o--o{ ISSUES:"has"


DEPENDENT_SUBJECTS{
uint64 issue
uint64 dependentIssue
uint8 dependencyType
}

DEPENDENT_SUBJECTS ||--o{  SUBJECT : "has issues"
DEPENDENT_SUBJECTS ||--o{  SUBJECT : "has dependent issues"


PROJECT ||--o{  ISSUES : "has child issues"

ISSUES ||--o{  NOTES: ""
ISSUES ||--o{  NOTES: ""
ISSUES ||--||  ISSUE_CHAT_SERVICE: ""
NOTES {
 uint64 id
 uint64 issue_id
 string comment
}

ISSUES {
ID uint64  
Title string  
Description string  
IssueForeignId string  
TargetTime uint32  
SpendingTime uint32  
Progress uint8  
Impact uint8
Label uint8
Status uint8
PartID uint64  
Subject Subject 
ProjectID uint64  
StatusID uint8 
ChildIssues Issue 
DependentIssues Issue 
ReporterID uint64 
Reporter User 
AssignieID uint64  
Assignie User 
DueDate time
CreatedAt timeTime 
UpdatedAt timeime 
DeletedAt gormDeletedAt 
}

ISSUES ||--o{  ISSUE_DEPENDECY : "has issues"
ISSUES ||--o{  ISSUE_DEPENDECY : "has dependent issues"
ISSUE_DEPENDECY {
uint64_PK issue
uint64_PK dependentIssue
uint8_ENUM_PK dependencyType
}

SUBJECT {
ID uint64 
Title string 
Description string
money budget
RepoID string  
ProjectID uint64  
Project Project 
Issues Issue 

CreatedAt timeTime 
UpdatedAt timeTime 
DeletedAt gormDeletedAt 
}

TEAM_MEMBERS ||--o{ ISSUES : ""

```

### Technical Notes
#### User Permission System
When user logged in, user will get organization permissions from ORGANIZATION_ACCOUNT table and team permissions from TEAM_MEMBER table ( which effects issue creation ) to token. Also user will get defaut role permissions from TEAM_MEMBER_ROLE table

#### Posgres scaling
We will scale project database by project based. Looks like this example https://www.notion.so/blog/sharding-postgres-at-notion

#### Chat Service Specs
- Every project and team has a chat group.
- Inside this chat groups members create sub channels for subjects or issue. Exam



### Content Service

Content Serivice is 
