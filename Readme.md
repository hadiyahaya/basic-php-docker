# Docker Compose: A Step-by-Step Tutorial Series

Welcome to the Docker Compose tutorial series! Docker Compose allows you to define and run applications using a simple YAML file. 

Before we begin, here are the essential commands you will use throughout this series:
* `docker compose up -d` (Start your environment in the background)
* `docker compose down` (Stop and remove your environment)
* `docker compose build` (Build custom images defined in a Dockerfile)

---

## Part 1: Hello World with PHP (Single Service)

In this first step, we will use a pre-built PHP image that includes the Apache web server to get a simple "Hello World" running instantly.

**1. Create your project structure:**
```text
my-php-app/
├── docker-compose.yml
└── index.php
```

**2. Create `index.php`:**
```php
<?php
echo "Hello, World from Docker Compose!";
```

**3. Create `docker-compose.yml`:**
```yaml
services:
  app:
    image: php:8.5-apache
    ports:
      - "8080:80"
    volumes:
      - ./:/var/www/html
```

**How to run it:**
1. Open your terminal in the `my-php-app` folder.
2. Run `docker compose up -d`.
3. Visit `http://localhost:8080` in your browser. 
4. Run `docker compose down` when you are ready for the next part.

---

## Part 2: Pure PHP with Nginx (Multi-Container)

In the real world, it is common to separate your web server from your PHP processing. Here, we will use Nginx as our web server and a pure PHP-FPM image to process the code.

**1. Update your project structure:**
Add an Nginx configuration file.
```text
my-php-app/
├── docker/
│   └── nginx.conf
├── docker-compose.yml
└── index.php
```

**2. Create `docker/nginx.conf`:**
This tells Nginx to send PHP requests to our PHP container.
```nginx
server {
    listen 80;
    server_name localhost;
    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass php:9000; 
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

**3. Update `docker-compose.yml`:**
We now define two distinct services that talk to each other.
```yaml
services:
  php:
    image: php:8.5-fpm
    volumes:
      - ./:/var/www/html
    depends_on:
      - nginx

  nginx:
    image: nginx:alpine
    ports:
      - "9080:80"
    volumes:
      - ./:/var/www/html
      - ./docker/nginx.conf:/etc/nginx/conf.d/default.conf
```

**How to run it:**
1. Run `docker compose up -d`.
2. Visit `http://localhost:8080`. Nginx is now serving your PHP file!
3. Run `docker compose down` to prepare for the final part.

---

## Part 3: Customizing with a Dockerfile

Using standard images is great, but eventually, you will need to customize your PHP environment (like adding PHP extensions for a database). To do this, we replace the standard PHP image with a custom `Dockerfile`.

**1. Update your project structure:**
Add a Dockerfile.
```text
my-php-app/
├── docker/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── php.ini
├── docker-compose.yml
└── index.php
```

**2. Create `docker/php.ini`:**
A few sensible defaults for local development.
```ini
upload_max_filesize = 20M
post_max_size = 20M
memory_limit = 256M
max_execution_time = 60
display_errors = On
```

**3. Create the `docker/Dockerfile`:**
This file gives Docker instructions on how to build your custom PHP image.
```dockerfile
# Start from the pure PHP-FPM image
FROM php:8.5-fpm

# Set the working directory inside the container
WORKDIR /var/www/html

# (Optional) You can install extensions here, like:
# RUN docker-php-ext-install pdo_mysql

# Copy your custom php.ini settings into the container
COPY docker/php.ini /usr/local/etc/php/conf.d/custom.ini

# Copy your local project files into the container
COPY . /var/www/html/

# Ensure the web server user owns the files
RUN chown -R www-data:www-data /var/www/html
```

**4. Update `docker-compose.yml`:**
Change the `php` service to build from your new Dockerfile instead of downloading a pre-built image.
```yaml
services:
  app:
    build: 
      context: .
      dockerfile: docker/Dockerfile
    volumes:
      - ./:/var/www/html
    depends_on:
      - php
  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./:/var/www/html
      - ./docker/nginx.conf:/etc/nginx/conf.d/default.conf
```

**How to run it:**
1. Because we added a Dockerfile, you must build the image first: `docker compose build`.
2. Start the environment: `docker compose up -d`.
3. Visit `http://localhost:8080` to see your final, custom-built setup in action!
4. Confirm your `php.ini` changes were applied inside the container:
   ```bash
   docker compose exec php php -i | grep memory_limit
   ```
   This should print `memory_limit => 256M => 256M`, matching the value you set in `docker/php.ini`.