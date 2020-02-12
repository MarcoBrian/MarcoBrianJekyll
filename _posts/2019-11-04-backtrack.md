---
title: "Backtrack"
date: 2019-11-04
tags: [Software Engineering , Agile & Scrum , Web Development]
excerpt: "Sprint Management Tool for Developers and Managers"
toc: true
toc_label: "Contents"
toc_sticky: false
---
# Introduction 

This was a software engineering group project made by 5 members (Ritvik, Pranav, Rajat, Rishabh, *special thanks to these guys!* , and me ). The goal of this project was to get ourselves acquainted with a practical implementation of the software development process of using the Scrum framework.  Technically, the project is a web development project. The web application was written with Python (using Django as the web-framework) for the backend and React (using Material-UI) for the application’s front-end. We use a REST framework for the communication between our frontend and the backend. The development of this project was separated into Sprints , where each of our sprints lasts for roughly 2 weeks. In each of our sprints we aim to develop an additional feature for the application. 

What we are trying to build here is a management tool for developers and managers in Agile teams to manage their sprint activities. Developers would be able to keep track of their tasks and Manager could view the current status of multiple projects that they are managing. Of course due to the limitation of time and resources, this application will never be able to outrank the management tools in the market (Jira for example). But overall this was a learning experience, and thus why I am writing it down. 

You can find the Django class diagram [here](https://marcobrian.github.io/assets/backtrack/DjangoClassDiagram.pdf)

## Inception Phase
In the beginning we had an initial phase for requirements engineering. This was the time where we gathered as much details about the project and gaining domain knowledge. This was when we built our [vision document](https://marcobrian.github.io/assets/backtrack/visiondoc.pdf) and created an idea of what features to build on following Sprints. Asking the right questions at this stage is crucial because we have to understand what it is that we are building, to make sure that we are creating the correct system. 

*Note - Review the vision document before going onwards.*

## Sprint 1

In the first sprint we implemented a simple CRUD (Create, Read, Update, Delete) web application. Our application at this stage is equivalent to a simple TO-DO list. In this stage, only one developer could use this application and he/she could CRUD the product backlog. The model design at this point is still very simple, and could still be explained in a few statements. In our django models, we created the developer model , the project model and the product backlog items model. In this simple 3 model application, the developer is associated with one project (one to one relationship) and each project is associated to many product backlog items (one to many relationship). Most of our time here is spent to be acquainted with the new technologies that were used. This is part of the risk-driven model of Agile development, because we were handling new technologies it if important that we would drive down the risk early. 

## Sprint 2

In this stage, we implemented sprint features of the application (creating sprints and managing current sprints). At this stage we were able to create new sprints and add product backlog items to the newly created sprint. We can then manage the product backlog items that are in the current sprint and assign tasks for each product backlog items. We are able to edit the tasks to contain the amount of effort hours needed to complete it, this way we can keep track of how much more time is needed to complete a product backlog item. 

## Sprint 3 

In this stage, we implemented the authentication and authorization for users. In this one we had to use a Django REST-AUTH library for creating the token for user authentication. Now different developers can have access to the application and also see different pages that are being shown to them. Developers will not be able to take on tasks that were already taken by other developers. This is also the stage where we finally implement two different types of users : managers & developers. Managers will have a completely different page from developers. They will be seeing a page where they can manage multiple projects. Managers will be able to see the product backlog items and their statuses for the projects, but they won’t be able to make any changes to it. 

## Demos

**Step 1** Logging in

<figure>
  <img src="/images/backtrack/login.JPG" alt="login">
  <figcaption> Enter user credentials here to login </figcaption>
</figure>

**Step 2** Create project

<figure>
  <img src="/images/backtrack/Capture-1.JPG" alt="create-project">
  <img src="/images/backtrack/Capture-2.JPG" alt="create-project-2">
  <figcaption> Add developers and manager to project </figcaption>
</figure>

**Step 3** Populate Product Backlog

<figure>
  <img src="/images/backtrack/Capture-4.JPG" alt="PBI">
</figure>

<figure>
  <img src="/images/backtrack/Capture-5.JPG" alt="PBI-2">
  <figcaption> Add product backlog items (PBIs) to the project's product backlog </figcaption>
</figure>

**Step 4** Create Sprint

<figure>
  <img src="/images/backtrack/Capture-3.JPG" alt="create-sprint">
  <figcaption> Create sprint once product backlog has been populated </figcaption>
</figure
    
<figure>
  <img src="/images/backtrack/Capture-6.JPG" alt="create-sprint-2">
  <figcaption> PBI from product backlog is moved to the current sprint, and tasks has been created for the corresponding PBI  </figcaption>
</figure>

**Step 5** Developer choosing tasks

<figure>
  <img src="/images/backtrack/Capture-8.JPG" alt="task-developer">
  <figcaption> Another developer has taken ownership for a task from the PBI and therefore it cannot be selected anymore </figcaption>
</figure>

<figure>
  <img src="/images/backtrack/Capture-9.JPG" alt="task-developer-2">
  <figcaption> You took ownership for a task and then you can update the status for the current task (ongoing/completed) </figcaption>
</figure>

<figure>
  <img src="/images/backtrack/Capture-10.JPG" alt="task-developer-3">
  <figcaption> Completed a task </figcaption>
</figure>

**Step 6** Manager view

<figure>
  <img src="/images/backtrack/Capture-12.JPG" alt="manager">
  <figcaption> The manager can view the product backlog for the project and monitor the status </figcaption>
</figure>

**Download links**

* [Vision document](https://marcobrian.github.io/assets/backtrack/visiondoc.pdf)

* [Django Class Diagram](https://marcobrian.github.io/assets/backtrack/visiondoc.pdf)

