## What is DTO?(payload)

- Data Transfer Object. Class that specifies what fields should be in the request body, and some rules for those fields.

## Controller Layer

- Extracts data from the request
- Validate the data
- Forwards the data to Service Layer
- Issues a response

## What does SERVICE LAYER do?

- Turns the data from the user (DTO) into an object ready to be save in the database(ENTITY) (add some fields, clean up the data)
- Save the Entity in the db through repository layer.

## REPOSITORY LAYER (DAO - Data Access Object)

- Methods fo CRUD operations that will under the hood query the database.
- Returns some data.

## Entity

- Class that represents a record in the database.

## What is Maven?

- Maven is an open-source build automation and project management tool widely used for Java applications. As a build automation tool, it automates the source code compilation and dependency management, assembles binary codes into packages, and executes test scripts. Maven translates and packages your source code so that it becomes an executable application.

## What is Spring Framework?

- It's and open source java application development framework which supports building of all types of java applications like web application, DB Driven application, Enterprise applications etc.
