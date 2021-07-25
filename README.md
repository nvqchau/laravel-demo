# Laravel Demo with Docker

1. Create new project folder `laravel-demo`
2. Move to `laravel-demo` folder
3. Create `app` folder as the lavavel application source code folder
4. Create `docker-compose.yml` file
  -  There is `web`, `db`, and `pgadmin` services
  - `web` service uses `webdevops/php-apache-dev:8.0` image. This is a apache server with PHP v8.0 in DEV environment. It has some initial configurations as:
    - `WEB_ALIAS_DOMAIN: laravel.docker.localhost`. Add this domain to the system `hosts` and you could access it instead of localhost. Change to any domain as needed
    - `WEB_DOCUMENT_ROOT: /app/public`. This is the application root. `/app` is the source code root and `/app/public` is the apache's laravel running root
    - `COMPOSER_VERSION: 2`. This is the composer's version. If your project requires version, change it to 1
    - ```
      ports:
        - "80:80"
      ```
      Mean access to the lavavel app by "http://localhost/ or "http://laravel.docker.localhost"
  - `db` service uses `postgres` image. It has some initial configurations as:
    - `POSTGRES_DB: laravel` --> Use for lavarel demo
    - `POSTGRES_USER: postgres` --> Use for laravel demo
    - `POSTGRES_PASSWORD: postgres` --> Use for lavarel demo
  - `pgadmin` serivce uses `dpage/pgadmin4` as database management tool. Access by "http://localhost:8080" (Check `docker-compose.yml`)
5. Run the docker by:
```bash
docker-compose up -d
[+] Running 3/0
 ⠿ Container demo_web_1      Running     0.0s
 ⠿ Container demo_pgadmin_1  Running     0.0s
 ⠿ Container demo_db_1       Running     0.0s
 ```
6. Access to the `web` service by: `docker exec -it demo_web_1 bash`
(Why `demo_web_1`, because it's container's name, check above or check the container list by: `docker ps`)
7. Inside `web` service, move to `app` folder (`cd app`). For now, the folder is empty
8. Next, we will install laravel application in to this folder.
  - Installation Via Composer: (https://laravel.com/docs/master/installation#installation-via-composer)
    ```bash
    composer create-project laravel/laravel {folder_name}
    ```
    We're already inside the working directory, then our correct command is:

    ```bash
    composer create-project laravel/laravel .
    ```
    Please notice that there is a dot `(.)` instead of the `folder_name`
9. Because we're mouting our project `app` folder to the `web` service's `app` folder. In other words, when we're installing the lavarel application inside the docker, our `app` folder also get the laravel source code.
10. After installing, run the application: `php artisan serve`, and check on browser by "http://localhost"
11. Edit the `app/.env` to connect to the postgres database
    ```env
    DB_CONNECTION=pgsql
    DB_HOST=db
    DB_PORT=5432
    DB_DATABASE=laravel
    DB_USERNAME=postgres
    DB_PASSWORD=postgres
    ```
  - Database connection type is `pgsql`. Check `app/config/database.php` to learn other types.
  - Database host is `db`. It's the docker's service (Check the `docker-compose.yml` file). We could use the docker's service as the database host because `web` service and `db` service have the same network `db-network`
  - Database name is `lavarel` as `db` service initial configurations.
  - Database usernamd and password is `postgres` as `db` service initial configurations.

12. After saving the `app/.env`, check the database connection by the migrate seed command
    ```bash
    php artisan migrate --seed
    Database seeding completed successfully.
    ```

