# Django Deployment Guide with Gunicorn and Nginx on Windows

## Introduction

This guide provides detailed instructions on setting up and deploying a Django application using Gunicorn as the WSGI server and Nginx as the reverse proxy on a Windows environment.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Install Python and Pip](#install-python-and-pip)
  - [Set Up Django Project](#set-up-django-project)
  - [Set Up Gunicorn](#set-up-gunicorn)
  - [Install and Configure Nginx](#install-and-configure-nginx)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Conclusion](#conclusion)

## Prerequisites

Before starting, ensure you have the following installed:

- **Python 3.x**: [Download Python](https://www.python.org/downloads/)
- **Pip**: Comes with Python 3.x
- **Django**: Web framework for Python
- **Gunicorn**: WSGI HTTP server
- **Nginx**: Web server and reverse proxy

## Installation

### Install Python and Pip

1. Download and install Python from the [official Python website](https://www.python.org/downloads/). Ensure the "Add Python to PATH" option is checked during installation.

2. Verify the installations:

    ```bash
    # Verify Python installation
    python --version

    # Verify Pip installation
    pip --version
    ```

### Set Up Django Project

1. Navigate to your project directory:

    ```bash
    cd path\to\your\project
    ```

2. Create and activate a virtual environment:

    ```bash
    python -m venv venv
    venv\Scripts\activate
    ```

3. Install Django:

    ```bash
    pip install django
    ```

4. Create a new Django project:

    ```bash
    django-admin startproject myproject
    ```

5. Navigate to the project directory:

    ```bash
    cd myproject
    ```

6. Run initial migrations:

    ```bash
    python manage.py migrate
    ```

7. Start the development server:

    ```bash
    python manage.py runserver
    ```

### Set Up Gunicorn

1. Install Gunicorn:

    ```bash
    pip install gunicorn
    ```

2. Start Gunicorn:

    ```bash
    gunicorn myproject.wsgi:application
    ```

### Install and Configure Nginx

1. Download Nginx from the [official site](https://nginx.org/en/download.html) and extract the files to a directory (e.g., `C:\nginx`).

2. Navigate to the Nginx directory and start Nginx:

    ```bash
    cd C:\nginx
    start nginx
    ```

3. Configure Nginx:

    Open the `nginx.conf` file located in `C:\nginx\conf\` and modify it as follows:

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

4. Create a `logs` directory in the Nginx installation folder:

    ```bash
    mkdir C:\nginx\logs
    ```

## Configuration

1. **Update Django Settings:**

   Ensure your `settings.py` file is configured to use a production database and that the `ALLOWED_HOSTS` setting includes your domain or IP address.

   ```python
   # settings.py

   ALLOWED_HOSTS = ['localhost', '127.0.0.1']
