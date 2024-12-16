## Предисловие (для проверяющих)
По закону подлости у меня стал лагать Ubuntu: не отображалась загруженная версия Gradle, со всеми вытекающими конфликтами плагинов Kotlin и сбое при загрузки проекта через gradle -v.
Тем не менее я опишу свои тесты и логику последовательности выполнения. 
Все файлы открылись без проблем. 
Информация из файла task.md дает чёткое представление о задаче.


# Todo-app (testing on Kotlin)

Automated tests for a TODO manager application. The application provides basic CRUD operations and WebSocket support for real-time updates.

## Table of Contents
- [About the Project](#about-the-project)
- [Technologies Used](#technologies-used)
- [Features](#features)
- [Setup](#setup)
- [Performance Testing](#performance-testing)
- [Project Structure](#project-structure)

## About the Project

This project implements automated testing for a TODO manager API. This is a simple TODO management application with CRUD (create, read, update, and delete) operations.
It also includes WebSocket functionality for real-time updates.

The project focuses on:

1 Functional testing of API endpoints.
2 Performance testing of the POST /todos endpoint.
3 Validation of WebSocket functionality.

## Technologies Used

- Kotlin 1.9.0
- Gradle with Kotlin DSL
- JUnit 5 for testing
- Ktor Client for API interactions
- Kotlinx Coroutines for concurrent testing
- Docker for running the TODO application

  ## Features

### Required to test endpoints:

- GET /todos`** - Getting a TODO list with pagination support
- POST /todos`** - Create a new TODO.
- PUT /todos/:id`** - Update an existing TODO.
- DELETE /todos/:id`** - Delete a TODO with authentication.
- `/ws`** - Using WebSocket for TODO updates

### Main task

1. Write tests to check the functionality of the application (5 tests for each route, additional tests - as a checklist).
2. Check the performance of the POST /todos endpoint (no need to plot graphs, measurements and a short report are enough).

### Key Highlights:

- Functional and data consistency tests.
- Edge case tests using ParameterizedTest.
- Performance testing for parallel requests using coroutines.
- WebSocket connectivity and real-time message verification.

## Setup


### Prerequisites

1. [Docker](https://www.docker.com/) installed on your machine.
2. [Kotlin](https://kotlinlang.org/) 1.9.0 or higher (JVM target 17).
3. [Gradle](https://gradle.org/) installed.

The emphasis is on the use of Kotlin, and Java is mentioned indirectly via the JVM Target. Let me know if you'd like additional refinements

 ### Running Tests
 - Clone the repository
 - Navigate to the project directory
 - Start the TODO application with Docker
 - Run the tests
 
 ## Performance Testing

 For performance testing, you can use a library that will simulate a large number of requests, such as Gatling or Apache JMeter. But if the task is to do this directly in Kotlin, you can use JMH (Java Microbenchmarking Harness).
 The PostTodoPerformanceTest benchmark evaluates the performance of the POST /todos endpoint under parallel load. The benchmark uses coroutines for asynchronous requests. The benchmark measures the following parameters:

- Average request time.
- Max and min request time.
- Total time spent on all requests.

### Key Metrics:

- Coroutines (threads): 5
*Simulates multiple clients using the API at the same time.*
- Requests per coroutine: 50
*Represents the request rate per client.*
- Total requests: 250
*Adequate volume for performance testing without overloading.*

## Project Structure

The project is organized as follows:
## Project Documentation

[Project Structure](https://github.com/KurbakovskiyYuriy/todo-app-tests-/blob/main/Project%20Structure) file.




 



