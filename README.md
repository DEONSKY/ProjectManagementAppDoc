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

```mermaid
erDiagram  
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
  ORGANIZATION ||--o{  SPECIAL_ROLES  : "contains custom rules" 
SPECIAL_ROLES {
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
 uint64 id
 uint64 user_id  
 uint64 organization_id  
 byte permissions
 }  
 USER ||--o{  ORGANIZATION_ACCOUNT  : "can join multiple organization" 
 USER {  
 uint64 id 
 string username
 string name
 string lastname
 string email
 string password
 time created_at
 time updated_at
 time deleted_at
 }
 ORGANIZATION ||--o{  PROJECT  : "contains projects"  
 ORGANIZATION_ACCOUNT ||--o{  PROJECT  : "has project responseible"  
 PROJECT {
	uint64 id
	string name
	uint64 description_file_id
	byte settings
	uint64 organization_id
	uint64 project_responsible_id
	string scope_rules
	time start_date
	time due_date
	time created_at
	time updated_at
	tiem deleted_at
 }
 CHAT_SERVICE ||--o{  PROJECT  : "has" 
 CHAT_SERVICE 
```

### Project Roadmap

Project Roadmap will created by gantt diagram page. First, sprints will be defined. Than subjects will be created and users organization accounts will add inside of the subject. Than inside the sprints, issues starts to creating. And default status is backlog.

- Subjects are abstract parts about business rules. Example:
- Issues are technical parts of projects

Issue Dependecy Types : child of, blocker of,new version of, new feature of  
Subject Dependecy Types : child of, new version of, next stage of


```mermaid
erDiagram  
PROJECT
PROJECT ||--o{  SPRINT  : "has" 
SPRINT {
uint64 id
uint64 name
uint64 project_id
time start
time end
uint64 next_sprint
uint64 prev_sprint

}
SPRINT ||--o|  SPRINT  : " can has next sprint " 
SPRINT ||--o|  SPRINT  : " can has prev sprint " 
SUBJECT |o--o{  ISSUES  : "has" 
SPRINT |o--o{  SUBJECT : "has"
PROJECT ||--o{  SUBJECT : "has"
TEAM_MEMBERS ||--o{ ORGANIZATION_ACCOUNT : ""
TEAM_MEMBERS ||--o{ TEAM : ""
TEAM_MEMBERS {
uint64 organization_account_id
uint64 subject_id
byte permissions
}
TEAM {
uint64 team_id
byte permissions
uint64 project_id
}
PROJECT ||--o{ TEAM : ""
ROLE {
string nameID
string resposiblelities
byte default_permissions
}
TEAM_MEMBER_ROLE {
string role_name
uint64 team_member_id
}
DEPENDENT_SUBJECTS{
uint64 issue
uint64 dependentIssue
uint8 dependencyType
}
DEPENDENT_SUBJECTS ||--o{  SUBJECT : "has issues"
DEPENDENT_SUBJECTS ||--o{  SUBJECT : "has dependent issues"
TEAM_MEMBERS ||--o{ TEAM_MEMBER_ROLE : ""
TEAM_MEMBERS ||--o{ ISSUES : ""
ROLE ||--o{ TEAM_MEMBER_ROLE : ""
PROJECT ||--o{  ISSUES : "has child issues"
ISSUES {
ID uint64  
Title string  
Description string  
IssueForeignId string  
TargetTime uint32  
SpendingTime uint32  
Progress uint8  
Label uint8
SubjectID uint64  
Subject Subject 
ProjectID uint64  
StatusID uint8 
ChildIssues Issue 
DependentIssues Issue 
ReporterID uint64 
Reporter User 
AssignieID uint64  
Assignie User 
CreatedAt timeTime 
UpdatedAt timeime 
DeletedAt gormDeletedAt 
}
DEPENDENT_ISSUES ||--o{  ISSUES : "has issues"
DEPENDENT_ISSUES ||--o{  ISSUES : "has dependent issues"
DEPENDENT_ISSUES {
uint64 issue
uint64 dependentIssue
uint8 dependencyType
}
ORGANIZATION_ACCOUNT 
SUBJECT {
ID uint64 
Title string 
Description string
RepoID string  
ProjectID uint64  
Project Project 
Issues Issue 

CreatedAt timeTime 
UpdatedAt timeTime 
DeletedAt gormDeletedAt 
}


```

### Technical Notes
#### User Permission System
When user logged in, user will get organization permissions from ORGANIZATION_ACCOUNT table and team permissions from TEAM_MEMBER table ( which effects issue creation ) to token. Also user will get defaut role permissions from TEAM_MEMBER_ROLE table
