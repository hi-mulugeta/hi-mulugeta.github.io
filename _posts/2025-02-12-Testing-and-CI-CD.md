---
layout: post
title: Testing and CI/CD
published: true
date: 12-02-2025
categories: [Blogging, documentaion,Tutorial,notes]
tags: [LLM,AI,Ollama,tech]
image:
  path: /assets/img/docker.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: flask is a python full-stack framework.
---
### Lesson Plan: Testing and CI/CD in Python with pytest, GitHub Actions, and Docker

#### **Objective:**
By the end of this lesson, students will be able to:
1. Write unit tests in Python using `pytest`.
2. Set up Continuous Integration (CI) using GitHub Actions.
3. Containerize a Python application using Docker.
4. Implement Continuous Deployment (CD) using Docker and GitHub Actions.

---

### **Lesson Outline**

#### **1. Introduction (15 minutes)**
- **What is Testing?**
  - Importance of testing in software development.
  - Types of tests: unit, integration, end-to-end.
- **What is CI/CD?**
  - Continuous Integration (CI): Automatically testing code changes.
  - Continuous Deployment (CD): Automatically deploying code changes.
- **Tools Overview:**
  - `pytest`: A testing framework for Python.
  - GitHub Actions: A CI/CD tool integrated with GitHub.
  - Docker: A tool to containerize applications.

---

#### **2. Writing Tests with `pytest` (30 minutes)**
- **Installation:**
  ```bash
  pip install pytest
  ```
- **Writing a Simple Test:**
  - Create a Python file `calculator.py`:
    ```python
    def add(a, b):
        return a + b

    def subtract(a, b):
        return a - b
    ```
  - Create a test file `test_calculator.py`:
    ```python
    from calculator import add, subtract

    def test_add():
        assert add(2, 3) == 5
        assert add(-1, 1) == 0

    def test_subtract():
        assert subtract(5, 3) == 2
        assert subtract(10, 7) == 3
    ```
- **Running Tests:**
  ```bash
  pytest
  ```
- **Explanation:**
  - `assert`: Checks if the condition is true; if not, the test fails.
  - `pytest` automatically discovers and runs tests in files named `test_*.py`.

---

#### **3. Setting Up CI with GitHub Actions (30 minutes)**
- **What is GitHub Actions?**
  - A CI/CD tool that allows you to automate workflows.
- **Creating a Workflow:**
  - Create a `.github/workflows/ci.yml` file:
    ```yaml
    name: CI

    on:
      push:
        branches:
          - main
      pull_request:
        branches:
          - main

    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Set up Python
            uses: actions/setup-python@v2
            with:
              python-version: '3.9'
          - name: Install dependencies
            run: |
              python -m pip install --upgrade pip
              pip install pytest
          - name: Run tests
            run: pytest
    ```
- **Explanation:**
  - `on`: Specifies when the workflow should run (e.g., on push or pull request).
  - `jobs`: Defines the jobs to run (e.g., `test`).
  - `steps`: The steps to execute in the job (e.g., install Python, run tests).

---

#### **4. Containerizing the Application with Docker (30 minutes)**
- **What is Docker?**
  - A tool to package applications into containers.
- **Creating a Dockerfile:**
  - Create a `Dockerfile`:
    ```dockerfile
    # Use an official Python runtime as a parent image
    FROM python:3.9-slim

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container
    COPY . .

    # Install any needed packages specified in requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt

    # Run tests
    RUN pytest

    # Make port 80 available to the world outside this container
    EXPOSE 80

    # Run the application
    CMD ["python", "app.py"]
    ```
- **Building and Running the Docker Image:**
  ```bash
  docker build -t my-python-app .
  docker run -p 4000:80 my-python-app
  ```
- **Explanation:**
  - `FROM`: Specifies the base image.
  - `WORKDIR`: Sets the working directory.
  - `COPY`: Copies files from the host to the container.
  - `RUN`: Executes commands (e.g., install dependencies, run tests).
  - `EXPOSE`: Exposes a port.
  - `CMD`: Specifies the command to run the application.

---

#### **5. Implementing CD with GitHub Actions and Docker (30 minutes)**
- **Extending the Workflow:**
  - Update `.github/workflows/ci.yml` to include CD:
    ```yaml
    name: CI/CD

    on:
      push:
        branches:
          - main
      pull_request:
        branches:
          - main

    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Set up Python
            uses: actions/setup-python@v2
            with:
              python-version: '3.9'
          - name: Install dependencies
            run: |
              python -m pip install --upgrade pip
              pip install pytest
          - name: Run tests
            run: pytest

      deploy:
        needs: test
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Build Docker image
            run: docker build -t my-python-app .
          - name: Log in to Docker Hub
            run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin
          - name: Push Docker image
            run: docker push my-python-app
    ```
- **Explanation:**
  - `deploy`: A new job that builds and pushes the Docker image.
  - `needs: test`: Ensures the `deploy` job runs only if the `test` job succeeds.
  - `secrets.DOCKER_USERNAME` and `secrets.DOCKER_PASSWORD`: GitHub secrets for Docker Hub credentials.

---

#### **6. Recap and Q&A (15 minutes)**
- Recap the key concepts: testing, CI/CD, Docker.
- Answer any questions from students.

---

### **Flashcards for Retention**

1. **Q:** What is `pytest` used for?  
   **A:** A testing framework for Python to write and run tests.

2. **Q:** How do you run tests using `pytest`?  
   **A:** Run `pytest` in the terminal.

3. **Q:** What is Continuous Integration (CI)?  
   **A:** Automatically testing code changes.

4. **Q:** What is Continuous Deployment (CD)?  
   **A:** Automatically deploying code changes.

5. **Q:** What is GitHub Actions?  
   **A:** A CI/CD tool integrated with GitHub.

6. **Q:** What is Docker?  
   **A:** A tool to containerize applications.

7. **Q:** What does the `FROM` command in a Dockerfile do?  
   **A:** Specifies the base image.

8. **Q:** What does the `WORKDIR` command in a Dockerfile do?  
   **A:** Sets the working directory in the container.

9. **Q:** What does the `COPY` command in a Dockerfile do?  
   **A:** Copies files from the host to the container.

10. **Q:** What does the `RUN` command in a Dockerfile do?  
    **A:** Executes commands (e.g., install dependencies, run tests).

11. **Q:** What does the `EXPOSE` command in a Dockerfile do?  
    **A:** Exposes a port to the outside world.

12. **Q:** What does the `CMD` command in a Dockerfile do?  
    **A:** Specifies the command to run the application.

13. **Q:** How do you build a Docker image?  
    **A:** Run `docker build -t <image-name> .`

14. **Q:** How do you run a Docker container?  
    **A:** Run `docker run -p <host-port>:<container-port> <image-name>`

15. **Q:** What is the purpose of the `on` key in a GitHub Actions workflow?  
    **A:** Specifies when the workflow should run (e.g., on push or pull request).

16. **Q:** What is the purpose of the `jobs` key in a GitHub Actions workflow?  
    **A:** Defines the jobs to run (e.g., `test`, `deploy`).

17. **Q:** What is the purpose of the `steps` key in a GitHub Actions workflow?  
    **A:** The steps to execute in the job (e.g., install Python, run tests).

18. **Q:** How do you log in to Docker Hub in a GitHub Actions workflow?  
    **A:** Use `echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin`

19. **Q:** How do you push a Docker image to Docker Hub in a GitHub Actions workflow?  
    **A:** Use `docker push <image-name>`

20. **Q:** What is the purpose of the `needs` key in a GitHub Actions workflow?  
    **A:** Ensures a job runs only if another job succeeds.

---

### **Conclusion**
This lesson plan provides a comprehensive guide to testing and CI/CD in Python using `pytest`, GitHub Actions, and Docker. The practical examples, code block comments, and flashcards will help reinforce the concepts and ensure retention.

---
### **Additional resources**
- Github Actions for Continuous Integration (CI) by **Wes Doyle**

  {% include embed/youtube.html id='uv-9tDauS6I' %}
- CS50W - Lecture 7 - Testing and CI/CD

  {% include embed/youtube.html id='WbRDkJ4lPdY' %}
- [Slides](https://cs50.harvard.edu/web) for CS50W(for the above video) 

