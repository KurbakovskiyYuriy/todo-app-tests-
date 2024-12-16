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


# Configure test dependencies in the build.gradle.kts file

```kotlin
dependencies {
    testImplementation("org.junit.jupiter:junit-jupiter-api:5.7.0")
    testImplementation("org.junit.jupiter:junit-jupiter-engine:5.7.0")
    testImplementation("io.rest-assured:rest-assured:4.4.0")
    testImplementation("org.springframework.boot:spring-boot-starter-websocket:2.5.3")
}

#  Basic Test Cases
## Test for GET /todos

import io.restassured.RestAssured.*
import org.junit.jupiter.api.Test
import org.hamcrest.Matchers.*

class TodoApiTests {

    @Test
    fun `should return a list of todos`() {
        given()
            .baseUri("http://localhost:8080")
            .get("/todos")
        .then()
            .statusCode(200)
            .body("size()", greaterThan(0)) // Checking the list size
    }

    @Test
    fun `should return limited todos with offset`() {
        given()
            .baseUri("http://localhost:8080")
            .queryParam("offset", 1)
            .queryParam("limit", 2)
            .get("/todos")
        .then()
            .statusCode(200)
            .body("size()", equalTo(2)) // Check if limit is respected
    }
}

## Test for POST /todos

```kotlin
@Test
fun `should create a new todo`() {
    val todo = mapOf("text" to "New Task", "completed" to false)
    given()
        .baseUri("http://localhost:8080")
        .contentType("application/json")
        .body(todo)
        .post("/todos")
    .then()
        .statusCode(201) // Created status
        .body("text", equalTo("New Task"))
        .body("completed", equalTo(false))
}

# Test for PUT /todos/:id

```kotlin
@Test
fun `should update an existing todo`() {
    val updatedTodo = mapOf("text" to "Updated Task", "completed" to true)

    given()
        .baseUri("http://localhost:8080")
        .contentType("application/json")
        .body(updatedTodo)
        .put("/todos/1") // Assuming 1 is a valid ID
    .then()
        .statusCode(200)
        .body("text", equalTo("Updated Task"))
        .body("completed", equalTo(true))
}


# Test for DELETE /todos/:id

```kotlin
@Test
fun `should delete an existing todo`() {
    given()
        .baseUri("http://localhost:8080")
        .header("Authorization", "Basic YWRtaW46YWRtaW4=") // admin:admin base64 encoded
        .delete("/todos/1") // Assuming 1 is a valid ID
    .then()
        .statusCode(204) // No content status
}

# Performance Test for POST /todos

```kotlin
@Test
fun `should check POST /todos performance`() {
    val todo = mapOf("text" to "Performance Test Task", "completed" to false)
    
    val startTime = System.currentTimeMillis()

    // Making a number of requests for load testing
    repeat(100) {
        given()
            .baseUri("http://localhost:8080")
            .contentType("application/json")
            .body(todo)
            .post("/todos")
    }

    val endTime = System.currentTimeMillis()
    val duration = endTime - startTime

    println("Total time for 100 requests: $duration ms")
    assert(duration < 5000) // Ensuring the requests are handled in under 5 seconds
}
```

# Performance test for POST /todos with JMH

## 1.Install the JMH dependency into the project

```kotlin
dependencies {
    implementation("org.openjdk.jmh:jmh-core:1.35")
    implementation("org.openjdk.jmh:jmh-generator-annprocess:1.35")
    testImplementation("io.rest-assured:rest-assured:4.4.0")
    testImplementation("org.junit.jupiter:junit-jupiter-api:5.7.0")
}
```
## 2.Writing a performance test
Create a TodoBenchmark.kt file in the src/main/kotlin folder at the root of the project.
Insert the following code into this file, which will test the performance of the POST /todos endpoint:

```kotlin
import org.openjdk.jmh.annotations.Benchmark
import org.openjdk.jmh.annotations.Warmup
import org.openjdk.jmh.annotations.Measurement
import org.openjdk.jmh.annotations.State
import io.restassured.RestAssured.*
import io.restassured.http.ContentType

@State(org.openjdk.jmh.annotations.Scope.Thread)
open class TodoBenchmark {

    val todo = mapOf("text" to "Test Task", "completed" to false)

    @Benchmark
    @Warmup(iterations = 3) // 3 прогрева
    @Measurement(iterations = 5) // 5 измерений
    fun testPostTodo() {
        given()
            .baseUri("http://localhost:8080")
            .contentType(ContentType.JSON)


## 3.Create a task to run JMH```kotlin

tasks.register("runBenchmark", JavaExec::class) {
    main = "org.openjdk.jmh.Main"
    classpath = sourceSets["main"].runtimeClasspath
}
```

## 4.Running the performance test

./gradlew runBenchmark


