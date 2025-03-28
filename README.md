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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
##### 1. Do we need an interface (or trait in Rust) for Subscriber in the Observer design pattern, or a single Model struct is enough?
In the BambangShop implementation, I think a single struct model for `Subscriber` is sufficient for now, as there is only one type of behavior for subscribing to product updates. However, in the Observer pattern, the role of the observer (`Subscriber`) is typically defined by an interface or trait to allow flexibility for different types of subscribers to handle notifications in various ways. In the future, if we wanted to extend the system to have multiple types of subscribers, using a trait for `Subscriber` would help decouple the notification handling logic, making the system more extensible and maintainable. So, while it's not necessary now, it’s a good practice to design with the potential for change.

##### 2. Is using Vec sufficient, or is DashMap necessary for ensuring uniqueness of `id` and `url`?
In the current implementation, I think using a `Vec` to store products and subscribers is not sufficient for ensuring uniqueness and efficient lookups. A `Vec` would require linear searching to find a product by `id` or a subscriber by `url`, which could become inefficient as the system scales. On the other hand, `DashMap` is designed to store key-value pairs with fast, constant-time lookups (`O(1)`) and also guarantees that keys like `id` for products and `url` for subscribers are unique. As both of uniqueness and efficiency are important for scalability, I believe `DashMap` is the right choice for this case.

##### 3. Do we still need DashMap for thread-safety, or could we use the Singleton pattern instead?
I think the Singleton pattern alone is not sufficient to ensure thread-safety in the case of the `SUBSCRIBERS` map. While the Singleton pattern ensures that only one instance of the `SUBSCRIBERS` map exists throughout the application, it does not handle concurrent access. In a multi-threaded environment, we need a thread-safe collection like `DashMap`, which ensures safe concurrent reads and writes. Without `DashMap`, we would have to manually manage synchronization, which could be error-prone and less efficient. Therefore, the combination of the Singleton pattern for a single instance and `DashMap` for thread safety is necessary in this case.

#### Reflection Publisher-2
##### 1. Why Separate “Service” and “Repository” from a Model?
I think separating the Service and Repository from a Model makes the code more organized and easier to maintain. The Repository handles database interactions, while the Service focuses on business logic, making each part of the system more independent. If everything were inside the Model, it would become too complex and harder to modify. By keeping them separate, I find it easier to update business rules without affecting how data is stored, and vice versa. This also makes testing simpler because each part can be tested individually.

##### 2. What happen if we only use the Model?
If we only rely on the Model, I imagine the code would become tightly coupled, making it harder to manage. For example, if `Program`, `Subscriber`, and `Notification` had to handle both data storage and business logic within the same structure, the code would be messy and harder to scale. Every change to one Model could require updates across multiple files, increasing the risk of bugs. I think this would make debugging and testing more difficult, as different concerns would be mixed in a single place.

##### 3. Have you explored more about Postman? How this tool helps you to test your current work?
I’ve explored Postman, and I think it's a really useful tool for testing APIs. It helps me quickly check if endpoints like `subscribe` and `unsubscribe` work correctly by sending HTTP requests and inspecting responses. One feature I find particularly useful is the ability to send various request types (`GET`, `POST`, `DELETE`, etc.) and view responses instantly. I also like that Postman allows me to save and organize requests into collections, making it easier to manage API tests. In future projects, I think features like automated testing and mock servers will be very useful for improving API development and debugging.

#### Reflection Publisher-3
