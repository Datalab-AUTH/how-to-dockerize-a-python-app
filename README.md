# About

This is a tutorial on how to dockerize a Python application. We will use
a very simple Flask app as an example.

# Prerequisites

You should already have the following installed in your system:
* a Python interpreter
* [Docker](https://docs.docker.com/engine/install/)

It is also assumed you are running the following commands in a terminal
window.

# Create a Python virtual environment

From within your project's top-level directory, run:
```
python3 -m venv venv/
source venv/bin/activate
```
The terminal prompt will change to indicate you are now inside the
virtual environment.

You will need to run the `activate` command every time you want to work
within the virtual environment.

Whenever you want to exit the virtual environment you can simply run:
```
deactivate
```
in your terminal.

# Create your python app

For this example we're going to use a very simple Flask app with a
single endpoint that just returns a "Hello Datalab!" message.

See contents of file `myapp.py`.

# Install prerequisites

With the virtual environment activated, run:
```
pip install flask # along with every other library you may need
pip freeze > requirements.txt
```

The `pip freeze` command stores all extra libraries that are installed
within the Python virtual environment to the `requirements.txt` file. We
can use that file later to recreate the same environment. This file
*should* be created from within a virtual environment so it is not
polluted by any other packages/libraries that may have been installed
globally.

At this point your app should work if you launch it like this:
```
./myapp.py
```
and be visible from http://127.0.0.1:5001/

OK, it works, so shut it down (^C) so that we can go on...

# Create a docker container

So let's package this up in a docker container. First create a file
named `Dockerfile`. See the respective file in this repo. You can edit
the python version used and also edit the app name to match your own app
of course.

If there is a build step in your app, append it to the `RUN` command.
Here's an example:
```
RUN pip install -r requirements.txt && \
  python setup.py build
```

You can create a docker container that will launch your python app as
soon as it is started with:
```
docker build -t myapp .
```

The `-t` option gives a tag (name) to your container and the `.` just
means to build whatever is in the present directory.

As soon as the `build` command finishes, you should be able to see your
container if you run:
```
docker images
```

# Start your container

You can start your dockerized app with:
```
docker run --name myapp -p 5001:5001 myapp
```

As Flask exposes the application through port 5001, with the `-p
5001:5001` part, you specify that you want this port to be accessible
from the host system. Otherwise it will only be available from *within*
the container, which may not be very useful.

If you don't provide a name with the `--name` option, docker will
assigne a random one.

At this point your app should be available from http://127.0.0.1:5001/

With this setup, you should be able to rebuild and run your dockerized
app in any system that supports docker without having to worry about
dependencies and different Python or library versions.

# Stopping the container

To stop the container, just run:
```
docker stop myapp
```

If you didn't assign a specific name when you ran it, find out how the
running docker container is called first by running:
```
docker ps
```
which shows all running or stopped docker containers. The name is shown
in the last column.

You may then also delete the container if you run
```
docker rm myapp
```

# .gitignore file

(Optionally) add a .gitignore file to your git repo so that you don't
add the `venv` directory and anything else you might not want, like
byte-compiled python files in your git repo. See example in this repo.

