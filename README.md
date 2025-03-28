# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Classically, the `Subscriber` is defined as an interface (or trait in Rust) to allow multiple types of subscribers with different behaviours. This approach provides flexibility and scalability. However, for BambangShop a single model would suffice since all subscribers are handled identically. However, this may lead to scalability issues in the future when multiple types of subscribers need to be implemented. Those new subscribers may have different behaviours in how they handle notifications and a single model would not suffice.
2. `DashMap` provides O(1) lookup, insertions, and deletions by key (ID or URL), whereas `Vec` requires O(n) searches. Moreover, using `Vec` does not guarantee unique ID, as two or more elements in a `Vec` can have the same ID due to incorrect implementations. This makes `DashMap` preferable due to its fast operations on large-scale data and its guarantee of unique IDs.
4. `DashMap` provides a thread-safe implementation of `HashMap`, allowing concurrent access without manual locking. On the other hand, a Singleton ensures a single instance but does not provide thread safety. To achieve thread safety with a Singleton, Rust's built-in synchronization primitives like `Arc` or `Mutex` would be needed, which can introduce additional complexity. Overall, using `DashMap` is not strictly necessary, but it can be beneficial due to its built-in thread safety.

#### Reflection Publisher-2
1. Repositories handle database operations like fetching, saving, and updating data, while services handle business logic. Only using model means we are tightly coupling database logic and business logic, making code harder to maintain. This also complicates testing, as different responsibilities are mixed together. By separating these responsibilities, we can easily swap components with different implementations, improving flexibility and scalability.
2. Each model would have to handle multiple responsibilities which increases complexity. Tightly coupled interactions complicates maintainance, leading to potential issues when scaling the codebase. For example, the `Subscriber` model directly use `REQWEST_INSTANCE` to send notifications, which limits flexibility and makes testing more difficult. What would happen if we want to replace `reqwest` with other HTTP libraries? We have to change every reference to `REQWEST_INSTANCE` in the codebase which will be a difficult task as the codebase becomes larger. By introducing service and repository layers, we can decouple these dependencies, improve modularity, and make the codebase more scalable.
3. Yes, I have. Postman is a tool to test APIs by sending requests, inspect responses, and automate testing workflows. One Postman feature that I use on a day-to-day basis is Collections. It allows me to organize API requests and run them in a structured way. The team collaboration feature is also valuable for group projects, allowing seamless sharing of API requests. Overall, Postman improves API testing for both individual and team-based development.

#### Reflection Publisher-3
1. We use the push model of the observer pattern, where the subject pushes notifications to the observers. This means observers receive notification updates automatically without needing to request data, allowing for real-time updates as soon as changes occur.
2. If we used the pull model instead, the observer would need to constantly check for updates rather than receiving them automatically. The advantage of the pull model is that it gives observers more control over when they request updated data. However, it also has drawbacks, such as updates not being received in real time. Additionally, frequent polling may lead to increased resource usage and increased server load. Given that notifications need to be sent automatically, the push model is the better choice, as it ensures instant updates without requiring observers to constantly check for new data.
3. Without multithreading, the program must wait until all subscribers are notifiesd, which can lead to performance issues, especially when notifying subscribers takes a long time. Using multithreading allows notifications to be sent concurrently, without forcing the main program wait, improving efficiency and reducing delays.
