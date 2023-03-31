
# Goal and Architecture
TBD

# Services Setup
1. Create network to connect all the services 
    ```sh
    docker network create mynetwork
    ```

2. Run mysql and phpmyadmin containers
    ```sh
    cd ./jenkins_mysql_flyway
    docker compose -f docker-compose-db.yml -p db up -d
    ```
    * Log in to phpmyadmin 
        * `http://localhost:8081`
        * username:password is `root:root` or `admin:admin`

3. Run gitbucket and jenkins containers
    ```sh
    docker compose -f docker-compose-jenkins.yml -p devops up -d
    ```
    * Log in to gitbucket
        * `http://localhost:8082`
        * username:password is `root:root`
    * Start jenkins
        * `http://localhost:8080`
        * Create admin user with username:password as `admin:admin`
        * Install the Gitbucket plugin

4. Create a repo, `flyway_repo`, in Gitbucket and check in all the files in `~/DevOps_In_DE/jenkins_mysql_flyway/flyway_repo` to the repo
   * Run commands with `root:root` as username:password
        ```sh
        cd ./jenkins_mysql_flyway/flyway_repo
        git init
        git add .
        git commit -m "first commit"
        git remote add origin http://localhost:8082/git/root/flyway_repo.git
        git push -u origin main
        ```
    * ==short video== TBD

5. Create a pipeline, `flyway_pipeline`, in Jenkins and have it connect to the repo, `flyway_repo`, in Gitbucket
    * Repo url is `http://gitbucket:8080/git/root/flyway_repo.git`
    * ==short video== TBD

# Trigger
1. Move all `*.sql` files in `~/DevOps_In_DE/jenkins_mysql_flyway/flyway_repo/sql_to_deploy` to `~/DevOps_In_DE/jenkins_mysql_flyway/flyway_repo/sql` 
    ```sh
    mv ./sql_to_deploy/* ./sql
    ```

2. Run flyway using the jenkins pipline, `flyway_pipeline`
    * Check in code 
        ```sh
        git add .
        git commit -m 'run versioned sql query'
        git push
        ```
    * Manually trigger the pipeline by hit `Build Now`
    * Check out database `db-name` to see if changes applied
    * ==short video== TBD

* If debugging is needed, try run flyway in local
    ```sh
    export PROJECT_FULLPATH=. && docker compose up && docker compose down
    ```

# Clean up
* Remove all the containers
    ```sh
    cd ./jenkins_mysql_flyway
    docker compose -f docker-compose-db.yml -p db down
    docker compose -f docker-compose-jenkins.yml -p devops down

    docker network prune -f
    docker volume prune -f
    ``
* Remove local git repo, `flyway_repo`
    ```sh
    cd ./jenkins_mysql_flyway/flyway_repo
    rm -rf .git
    ```

# Future works
*  Figure it out how to automatically run the pipeline in jenkins by checking in the code to the repo in Gitbucket. For some reasons, the webhook was not working as expected. 
    * https://stackoverflow.com/questions/49574298/how-to-trigger-auto-build-in-jenkins-via-gitbuckets-webhook
    * https://www.edureka.co/community/49753/auto-build-job-jenkins-there-change-code-github-repository

# References
* https://hackmamba.io/blog/2022/04/running-docker-in-a-jenkins-container/
* https://writeitdifferently.com/postgresql/flyway/2020/03/15/running-postgresql-and-flyway-with-docker-compose.html
