# 0x1A-application_server task
![image](https://user-images.githubusercontent.com/106750453/224136269-1a2fe9a1-0629-4b15-804c-3a0d55744680.png)
The web infrastructure is already serving web pages via Nginx that is installed in the first web stack project. While a web server can also serve dynamic content, this task is usually given to an application server. In this project i will add this piece to your infrastructure, plug it to my Nginx and make is serve your Airbnb clone project.

# General

    A README.md file, at the root of the folder of the project, is mandatory
    Everything Python-related must be done using python3
    All config files must have comments

# Bash Scripts

    All your files will be interpreted on Ubuntu 16.04 LTS
    All your files should end with a new line
    All your Bash script files must be executable
    Your Bash script must pass Shellcheck (version 0.3.7-5~ubuntu16.04.1 via apt-get) without any error
    The first line of all your Bash scripts should be exactly #!/usr/bin/env bash
    The second line of all your Bash scripts should be a comment explaining what is the script doing

#Tasks
0. Set up development with Python 
Let’s serve what you built for AirBnB clone v2 - Web framework on web-01. This task is an exercise in setting up your development environment, which is used for testing and debugging your code before deploying it to production.

Requirements:

    Make sure that task #3 of your SSH project is completed for web-01. The checker will connect to your servers.
    Git clone your AirBnB_clone_v2 on your web-01 server.
    Configure the file web_flask/0-hello_route.py to serve its content from the route /airbnb-onepage/ on port 5000.
    Your Flask application object must be named app (This will allow us to run and check your code).

1. Set up production with Gunicorn 
Now that you have your development environment set up, let’s get your production application server set up with Gunicorn on web-01, port 5000. You’ll need to install Gunicorn and any libraries required by your application. Your Flask application object will serve as a WSGI entry point into your application. This will be your production environment. As you can see we want the production and development of your application to use the same port, so the conditions for serving your dynamic content are the same in both environments.

Requirements:

    Install Gunicorn and any other libraries required by your application.
    The Flask application object should be called app. (This will allow us to run and check your code)
    You will serve the same content from the same route as in the previous task. You can verify that it’s working by binding a Gunicorn instance to localhost listening on port 5000 with your application object as the entry point.
    In order to check your code, the checker will bind a Gunicorn instance to port 6000, so make sure nothing is listening on that port.

2. Serve a page with Nginx 
Building on your work in the previous tasks, configure Nginx to serve your page from the route /airbnb-onepage/

Requirements:

    Nginx must serve this page both locally and on its public IP on port 80.
    Nginx should proxy requests to the process listening on port 5000.
    Include your Nginx config file as 2-app_server-nginx_config.

Notes:

    In order to test this you’ll have to spin up either your production or development application server (listening on port 5000)
    In an actual production environment the application server will be configured to start upon startup in a system initialization script. This will be covered in the advanced tasks.
    You will probably need to reboot your server (by using the command $ sudo reboot) to have Nginx publicly accessible

3. Add a route with query parameters 
Building on what you did in the previous tasks, let’s expand our web application by adding another service for Gunicorn to handle. In AirBnB_clone_v2/web_flask/6-number_odd_or_even, the route /number_odd_or_even/<int:n> should already be defined to render a page telling you whether an integer is odd or even. You’ll need to configure Nginx to proxy HTTP requests to the route /airbnb-dynamic/number_odd_or_even/(any integer) to a Gunicorn instance listening on port 5001. The key to this exercise is getting Nginx configured to proxy requests to processes listening on two different ports. You are not expected to keep your application server processes running. If you want to know how to run multiple instances of Gunicorn without having multiple terminals open, see tips below.

Requirements:

    Nginx must serve this page both locally and on its public IP on port 80.
    Nginx should proxy requests to the route /airbnb-dynamic/number_odd_or_even/(any integer) the process listening on port 5001.
    Include your Nginx config file as 3-app_server-nginx_config.

Tips:

    Check out these articles/docs for clues on how to configure Nginx: Understanding Nginx Server and Location Block Selection Algorithms, Understanding Nginx Location Blocks Rewrite Rules, Nginx Reverse Proxy.
    In order to spin up a Gunicorn instance as a detached process you can use the terminal multiplexer utility tmux. Enter the command tmux new-session -d 'gunicorn --bind 0.0.0.0:5001 web_flask.6-number_odd_or_even:app' and if successful you should see no output to the screen. You can verify that the process has been created by running pgrep gunicorn to see its PID. Once you’re ready to end the process you can either run tmux a to reattach to the processes, or you can run kill <PID> to terminate the background process by ID.

4. Let's do this for your API 
Let’s serve what you built for AirBnB clone v3 - RESTful API on web-01.

Requirements:

    Git clone your AirBnB_clone_v3
    Setup Nginx so that the route /api/ points to a Gunicorn instance listening on port 5002
    Nginx must serve this page both locally and on its public IP on port 80
    To test your setup you should bind Gunicorn to api/v1/app.py
    It may be helpful to import your data (and environment variables) from this project
    Upload your Nginx config file as 4-app_server-nginx_config

5. Serve your AirBnB clone 
Let’s serve what you built for AirBnB clone - Web dynamic on web-01.

Requirements:

    Git clone your AirBnB_clone_v4
    Your Gunicorn instance should serve content from web_dynamic/2-hbnb.py on port 5003
    Setup Nginx so that the route / points to your Gunicorn instance
    Setup Nginx so that it properly serves the static assets found in web_dynamic/static/ (this is essential for your page to render properly)
    For your website to be fully functional, you will need to reconfigure web_dynamic/static/scripts/2-hbnb.js to the correct IP
    Nginx must serve this page both locally and on its public IP and port 5003
    Make sure to pull up your Developer Tools on your favorite browser to verify that you have no errors
    Upload your Nginx config as 5-app_server-nginx_config

6. Deploy it! 
Once you’ve got your application server configured, you want to set it up to run by default when Linux is booted. This way when your server inevitably requires downtime (you have to shut it down or restart it for one reason or another), your Gunicorn process(es) will start up as part of the system initialization process, freeing you from having to manually restart them. For this we will use systemd. You can read more about systemd in the documentation posted at the top of this project but to put it succinctly, it is a system initialization daemon for the Linux OS (amongst other things). For this task you will write a systemd script which will start your application server for you. As mentioned in the video at the top of the project, you do not need to create a Unix socket to bind the process to.

Requirements:

    Write a systemd script which starts a Gunicorn process to serve the same content as the previous task (web_dynamic/2-hbnb.py)
    The Gunicorn process should spawn 3 worker processes
    The process should log errors in /tmp/airbnb-error.log
    The process should log access in /tmp/airbnb-access.log
    The process should be bound to port 5003
    Your systemd script should be stored in the appropriate directory on web-01
    Make sure that you start the systemd service and leave it running
    Upload gunicorn.service to GitHub

