# Django Deployment Guide with Waitress and Nginx on Windows

## Introduction

Deploying a Django application involves several steps to ensure it runs efficiently and securely in a production environment. This guide walks you through deploying a Django application using Waitress as the WSGI server and Nginx as the reverse proxy on a Windows system. Waitress is a robust WSGI server, while Nginx handles client requests and forwards them to Waitress, improving performance and security.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Install Python and Pip](#install-python-and-pip)
  - [Set Up Django Project](#set-up-django-project)
  - [Set Up Waitress](#set-up-waitress)
  - [Install and Configure Nginx](#install-and-configure-nginx)
- [Configuration](#configuration)
- [Prototyping with Figma](#prototyping-with-figma)
- [Troubleshooting](#troubleshooting)
- [Conclusion](#conclusion)

## Prerequisites

Before starting, ensure the following software is installed:

- **Python 3.x**: Download from [Python Official Website](https://www.python.org/downloads/).
- **Pip**: Comes with Python 3.x.
- **Django**: Web framework for Python.
- **Waitress**: WSGI server for Python.
- **Nginx**: Web server and reverse proxy.

## Installation

### Install Python and Pip

1. **Download and Install Python**:
   - Go to the [Python Downloads Page](https://www.python.org/downloads/) and download the latest version of Python 3.x.
   - During installation, ensure you select the "Add Python to PATH" checkbox. This makes Python and Pip accessible from the command line.

2. **Verify Installations**:
   - Open Command Prompt and run:

    ```bash
    python --version
    pip --version
    ```

   - This will display the installed versions of Python and Pip, confirming the installation.

### Set Up Django Project

1. **Navigate to Your Project Directory**:

    ```bash
    cd path\to\your\project
    ```

2. **Create and Activate a Virtual Environment**:
   - Create a virtual environment to manage dependencies:

    ```bash
    python -m venv venv
    ```

   - Activate the virtual environment:

    ```bash
    venv\Scripts\activate
    ```

3. **Install Django**:

    ```bash
    pip install django
    ```

4. **Create a New Django Project**:

    ```bash
    django-admin startproject myproject
    ```

5. **Navigate to the Project Directory**:

    ```bash
    cd myproject
    ```

6. **Run Initial Migrations**:

    ```bash
    python manage.py migrate
    ```

7. **Start the Development Server**:
   - Test your setup by running:

    ```bash
    python manage.py runserver
    ```

   - Open your browser and go to `http://127.0.0.1:8000/` to see the Django welcome page.

### Set Up Waitress

1. **Install Waitress**:

    ```bash
    pip install waitress
    ```

2. **Start Waitress**:
   - Launch Waitress to serve your Django application:

    ```bash
    waitress-serve --host=127.0.0.1 --port=8000 myproject.wsgi:application
    ```

   - Waitress will now serve your Django application at `http://127.0.0.1:8000`.

### Install and Configure Nginx

1. **Download and Install Nginx**:
   - Download Nginx from the [official Nginx site](https://nginx.org/en/download.html).
   - Extract the files to a directory, for example, `C:\nginx`.

2. **Start Nginx**:
   - Navigate to the Nginx directory and start Nginx:

    ```bash
    cd C:\nginx
    start nginx
    ```

3. **Configure Nginx**:
   - Open `nginx.conf` located in `C:\nginx\conf\` with a text editor and modify it as follows:

    ```nginx
    worker_processes  1;

    events {
        worker_connections  1024;
    }

    http {
        include       mime.types;
        default_type  application/octet-stream;

        sendfile        on;
        keepalive_timeout  65;

        server {
            listen 80;
            server_name localhost;

            location / {
                proxy_pass http://127.0.0.1:8000;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
            }

            error_log logs/error.log;
            access_log logs/access.log;
        }
    }
    ```

4. **Create a `logs` Directory**:
   - To ensure logs are saved properly, create a `logs` directory in the Nginx installation folder:

    ```bash
    mkdir C:\nginx\logs
    ```

## Configuration

1. **Update Django Settings**:
   - Ensure your `settings.py` file is configured for production:

    ```python
    ALLOWED_HOSTS = ['localhost', '127.0.0.1']

    SESSION_COOKIE_SECURE = True
    SESSION_COOKIE_HTTPONLY = True
    SESSION_EXPIRE_AT_BROWSER_CLOSE = True

    STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
    ```

   - This configuration ensures that Django’s static and media files are handled properly and that cookies are secure.

## Prototyping with Figma

Before implementing the design in code, prototyping was done using Figma. This process involved:

1. **Creating Wireframes**:
   - Outline the layout and functionality of the application.

2. **Developing High-Fidelity Mockups**:
   - Incorporate colors, typography, and UI elements to visualize the final design.

3. **Exporting Design Assets**:
   - Export design assets and specifications from Figma for use in development.

   - [Figma Prototype Link](https://www.figma.com/design/STZaMxOvYTg4H2kQ1k4Fao/Untitled?node-id=0-1&t=JzNbV6bhWIoAeSW9-0)

## Troubleshooting

1. **Common Issues**:
   - **Port Conflicts**: Ensure no other applications are using port 8000.
   - **Permissions Errors**: Run Command Prompt as Administrator if encountering permission issues.
   - **Nginx Configuration Errors**: Verify the Nginx configuration with `nginx -t`.

2. **Debugging Tips**:
   - Check logs in `C:\nginx\logs` for Nginx errors.
   - Use `curl` or Postman to test endpoints and ensure they are correctly proxied.

## Conclusion

This guide covered the essential steps for deploying a Django application with Waitress and Nginx on Windows. By setting up Waitress as the WSGI server and Nginx as the reverse proxy, you can enhance performance, security, and scalability. For a production environment, always follow best practices, such as securing your application and monitoring server performance.

---

In this setup, Nginx acts as a reverse proxy. It receives client requests, forwards them to the Django application running on Gunicorn, and then sends the response from Gunicorn back to the client. This allows Nginx to handle tasks like SSL termination, load balancing, and serving static files, while Gunicorn focuses on running the Python application.
