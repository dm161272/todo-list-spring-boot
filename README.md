Certainly! Below is a step-by-step guide to create a simple Todo REST API using Spring Boot, Hibernate, PostgreSQL, and Gradle with Java as the main language.

1. Set Up the Project
Build File (build.gradle)
Create a new Gradle project and add the following dependencies in your build.gradle file:

groovy
Copy code
plugins {
    id 'org.springframework.boot' version '3.0.0'
    id 'io.spring.dependency-management' version '1.0.13.RELEASE'
    id 'java'
}

group = 'com.example'
version = '1.0.0'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    runtimeOnly 'org.postgresql:postgresql'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
    useJUnitPlatform()
}
2. Configure Application Properties
Create an application.properties file in the src/main/resources directory with the following content:

properties
Copy code
spring.datasource.url=jdbc:postgresql://localhost:5432/tododb
spring.datasource.username=your_db_username
spring.datasource.password=your_db_password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
3. Create the Todo Entity
Create a Java class for the Todo entity.

Todo.java
java
Copy code
package com.example.demo.entity;

import javax.persistence.*;

@Entity
@Table(name = "todos")
public class Todo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    @Column(nullable = false)
    private String description;

    @Column(nullable = false)
    private boolean completed;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public boolean isCompleted() {
        return completed;
    }

    public void setCompleted(boolean completed) {
        this.completed = completed;
    }
}
4. Create the Todo Repository
Create a repository interface for the Todo entity.

TodoRepository.java
java
Copy code
package com.example.demo.repository;

import com.example.demo.entity.Todo;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface TodoRepository extends JpaRepository<Todo, Long> {
}
5. Create the Todo Service
Create a service class to handle business logic.

TodoService.java
java
Copy code
package com.example.demo.service;

import com.example.demo.entity.Todo;
import com.example.demo.repository.TodoRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class TodoService {

    @Autowired
    private TodoRepository todoRepository;

    public List<Todo> getAllTodos() {
        return todoRepository.findAll();
    }

    public Todo getTodoById(Long id) {
        return todoRepository.findById(id).orElseThrow(() -> new RuntimeException("Todo not found"));
    }

    public Todo createTodo(Todo todo) {
        return todoRepository.save(todo);
    }

    public Todo updateTodo(Long id, Todo todo) {
        Todo existingTodo = getTodoById(id);
        existingTodo.setTitle(todo.getTitle());
        existingTodo.setDescription(todo.getDescription());
        existingTodo.setCompleted(todo.isCompleted());
        return todoRepository.save(existingTodo);
    }

    public void deleteTodo(Long id) {
        todoRepository.deleteById(id);
    }
}
6. Create the Todo Controller
Create a controller class to handle HTTP requests.

TodoController.java
java
Copy code
package com.example.demo.controller;

import com.example.demo.entity.Todo;
import com.example.demo.service.TodoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/todos")
public class TodoController {

    @Autowired
    private TodoService todoService;

    @GetMapping
    public ResponseEntity<List<Todo>> getAllTodos() {
        return ResponseEntity.ok(todoService.getAllTodos());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Todo> getTodoById(@PathVariable Long id) {
        return ResponseEntity.ok(todoService.getTodoById(id));
    }

    @PostMapping
    public ResponseEntity<Todo> createTodo(@RequestBody Todo todo) {
        return ResponseEntity.status(HttpStatus.CREATED).body(todoService.createTodo(todo));
    }

    @PutMapping("/{id}")
    public ResponseEntity<Todo> updateTodo(@PathVariable Long id, @RequestBody Todo todo) {
        return ResponseEntity.ok(todoService.updateTodo(id, todo));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTodo(@PathVariable Long id) {
        todoService.deleteTodo(id);
        return ResponseEntity.noContent().build();
    }
}
7. Create the Application Class
Create the main application class to run the Spring Boot application.

DemoApplication.java
java
Copy code
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
8. Run the Application
Ensure PostgreSQL is running and accessible with the database tododb created. You can then run the Spring Boot application using the following command:

sh
Copy code
./gradlew bootRun
Testing the API
You can test the API using tools like Postman or cURL. Here are some example requests:

Create a Todo

sh
Copy code
curl -X POST "http://localhost:8080/api/todos" -H "Content-Type: application/json" -d '{"title":"Sample Todo", "description":"This is a sample todo", "completed":false}'
Get All Todos

sh
Copy code
curl -X GET "http://localhost:8080/api/todos"
Get Todo by ID

sh
Copy code
curl -X GET "http://localhost:8080/api/todos/1"
Update Todo

sh
Copy code
curl -X PUT "http://localhost:8080/api/todos/1" -H "Content-Type: application/json" -d '{"title":"Updated Todo", "description":"This is an updated todo", "completed":true}'
Delete Todo

sh
Copy code
curl -X DELETE "http://localhost:8080/api/todos/1"
This completes the setup of a simple Todo REST API using Spring Boot, Hibernate, PostgreSQL, and Gradle with Java.
