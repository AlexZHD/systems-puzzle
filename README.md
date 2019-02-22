# Solution for the Insight DevOps Engineering Systems Puzzle

## Table of Contents
1. [Puzzle details](README.md#puzzle-details)


# Puzzle details

open a terminal, `cd` into this repo, and then enter these two commands:

    docker-compose up -d db
    docker-compose run --rm flaskapp /bin/bash -c "cd /opt/services/flaskapp/src && python -c  'import database; database.init_db()'"

This "bootstraps" the PostgreSQL database with the correct tables. After that you can run the whole system with:

    docker-compose up -d

At that point, the web application should be visible by going to `localhost:8080` in a web browser. 

# Solution debug
02.21 

    Initial review of Dockerfile:

        no initial errors

        flaskapp exposed port 5001

    Initial review docker-compose.yml:

        networks, ports and env. variables check

            `localhost:8080` and nginx needs to listen on port 80, fixed port orders

    First run of system:         

        "
            docker-compose up -d db
        "  

        ckeck db log file "database system is ready to accept connections"

        db is running on port 5432

        "
            docker-compose run --rm flaskapp /bin/bash -c "cd /opt/services/flaskapp/src && python -c  'import database; database.init_db()'"
        " 

        build was executed, database was connected, because --rm flag flaskapp was 
        
        removed when finished

        "
            docker-compose up -d
        "

        nginx image initial download and build

        nginx, flaskapp and postgres are running

        "502 bad gateway error" of "nginx/1.13.5" on localhost:8080

    Solution:

        nginx listening on port 80 and "proxy_pass http://flaskapp:5001" 

        flaskapp needs to listen on port 5001, update app.py with 

        "app.run(host='0.0.0.0', port=int("5001"))" 

        run docker-compose rebuild and application works  
