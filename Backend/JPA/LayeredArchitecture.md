# Layered Architecture

Chapter 3.1.1

## About
A layered architecture defines interfaces (boundaries between different components or layers of a system) that implement certain concerns or functionalities that specify how different parts of a system interact with one another. It allows changes to be made within that implementation of concern without disrupting other layers.

![Screenshot 2023-11-01 155921](https://github.com/brian6484/StudyNote/assets/56388433/22ec4578-c700-460b-8a94-908dfc3faa15)

* Layers communicate from top to bottom so any given layer is dependent on the interface of the layer below itself.
* Layer eventually knows its upper layer when it receives explicit requests from it (e.g. service getting requests from controller)

## Layers
### Presentation Layer
This handles the user interface logic like control of page and screen navigation. It can also access business entities and render them on the screen.
### Business Layer
It handles the business logic.
### Persistence Layer
This saves or retrieves data from the database. It needs a business domain model so that it knows which entities to keep in a persisten state.
### DB Layer
It shows the persistent representation of current system. If it is SQL, it includes a schema and maybe some stored procedures to execute business logic.
### Helper and utility classes
Every layer has some helper and util classes to help deal with cross-cutting concerns (aspects of a system that affect multiple components/layers of a system). These can include logging, security and caching.

