# References:
* https://hackmamba.io/blog/2022/04/running-docker-in-a-jenkins-container/
* https://writeitdifferently.com/postgresql/flyway/2020/03/15/running-postgresql-and-flyway-with-docker-compose.html

# Services Setup:
1. Create network to connect all the services 
    ```sh
    docker network create mynetwork
    ```

2. Run mysql and phpmyadmin containers
    ```sh
    docker compose -f docker-compose-db.yml -p db up -d
    ```

3. Run gitbucket and jenkins containers
    ```sh
    docker compose -f docker-compose-jenkins.yml -p devops up -d
    ```

# Trigger
1. Run flyway from local instead of jenkins pipeline 
    ```sh
    cd ./flyway
    export PROJECT_FULLPATH=. && docker compose up
    ```

2. Run flyway by jenkins pipeline

