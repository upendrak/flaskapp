# Deploying a Flask application in Heroku cloud: An end-to-end tutorial

Run with `./run.py`

```
* Serving Flask app "flaskexample" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 287-027-838
```

You should be able to run this app on your own system via the familiar invocation and visiting [http://localhost:5000](http://localhost:5000):

# Deploying the Flask app to the cloud via Heroku

This walkthrough will acquaint you with the popular [Heroku cloud application platform](https://devcenter.heroku.com/start). Previously, we have been able to create Python Flask apps (see above) and run them locally on our own computers. With a cloud service, we are able to put our websites onto the public World Wide Web, with a publicly accessible URL.

A live version of this lesson's very simple app can be found at this URL: https://flaskinsightherokuapp.herokuapp.com/

(it'll be slow because I'm using Heroku's free tier)

Deploying an app on the cloud just means that we're putting code on a computer that we have no direct control over. Writing the Python app code is the same as it was before, but we have to follow a few Heroku conventions before Heroku will execute our code on its own computers.

Some of the instructions in this tutorial comes from these this official Heroku tutorial: [Getting Started on Heroku with Python](https://devcenter.heroku.com/articles/getting-started-with-python?singlepage=true). However, be warned, the official tutorial includes a lot of extra steps that may not apply to your system. I've tried to filter them to a minimum.

------------------

## Sign up for Heroku

Heroku, being a Software as a Service (SaaS)-type of service, requires you to create an account and login before you can start using its computers. Don't worry, creating an account and running a simple app is free and doesn't require a credit card.

You can create an account at this URL: [https://signup.heroku.com/dc](https://signup.heroku.com/dc)

## Setup Heroku

After creating your account and logging in, you will be presented with a dashboard where you can manage your applications but I advise you to head over to the [heroku cli](https://devcenter.heroku.com/articles/heroku-cli) and download one for your Operating System as it’s much easier to use than the dashboard.

## Authenticate with Heroku with `heroku login`

Installing the Heroku toolbelt will give you access to the `heroku` command which has several subcommands for interacting with the Heroku service. 

The first command you need to run is `heroku login`, which will ask you to enter your login credentials so that every subsequent `heroku` command knows who you are:

(You will only have to do this __once__)

~~~sh
$ heroku login
~~~

# Let's create a Heroku Flask app

## Git Repository

To deploy to Heroku, you should have a git repository set up. I will assume that by the time you reach the point to deploy your application, you already have a git repository, so I will not go into the details on how to set up one. For this tutorial, I am going to use this current repository.

## Create a virtual environment

It's better to create a virtual environment to keep it separate.

~~~sh
$ virtualenv flaskapp
$ source flaskapp/bin/activate
~~~

## Install flask and Gunicorn

Install flask using `pip install flask`. Flask is needed for running flask apps. Heroku does not provide a web server. It expects your application to start it’s own server. Flask web server is not good for production deployments, so I suggest you install gunicorn using `pip` in your virtual environment.

~~~sh
$ pip install flask gunicorn
~~~

## Declare app dependencies

Heroku recognizes an app as a Python app by looking for key files. Including a `requirements.txt` in the root directory is one way for Heroku to recognize your Python app. Before you deploy to Heroku, ensure your `requirements.txt` file is up-to-date and has the flask and gunicorn. You can update your requirements file before deployment with the following command

~~~sh
$ pip freeze > requirements.txt
~~~

## Specifying a Python runtime

By default, new Python applications on Heroku use the Python runtime indicated in Specifying a Python version. If you’re running a Python application that requires a different supported runtime, or if you simply want to lock your project against patch updates until you’re ready to upgrade, you can specify which runtime to use for your app.

To specify a Python runtime, add a `runtime.txt` file to your app’s root directory that declares the exact version number to use:

~~~sh
$ cat runtime.txt 
python-3.6
~~~

Note: The `runtime.txt` format is case-sensitive and must not include spaces. You must also specify all three version number components (major, minor, and patch) in runtime.txt. If you don’t follow this format, your app will fail to deploy.

To check which version of Python you’re running locally, activate your virtual environment and check with the -V flag:

~~~sh
$ python -V
Python 3.6.7 :: Anaconda, Inc.
~~~

## Procfile

Heroku requires you to have a Procfile in your application root directory. A `Procfile` tells Heroku, how to run your application. Create a Procfile with the following command. Make sure not to add any file extensions such as txt to the file name

~~~sh
$ touch Procfile
~~~

After creating the Procfile, add the following lines to it

~~~sh
$ web: gunicorn flaskexample:app --log-file=-
~~~

- The **web** label is used by Heroku to start the web server for your application. Heroku expects this label to be in your Procfile.

- **flaskexample:app** specifies the module and the name of application that the will be started by gunicorn. Make sure you have a module called **flaskexample** in your application root directory and a Flask app called **app** in your `__init__.py` file in case you add the above line to your Procfile. The module name and application name can be any other name. 

## Deploy to Heroku

Before deploying to Heroku, make sure your master branch is well merged with any other branches and all pull requests are closed. Also make sure that it has a `Procfile` and the `requirements` file is up to date. Create an app on Heroku, which prepares Heroku to receive your source code.

To deploy your project from git to Heroku, you need to create a heroku app. To create an app on Heroku for your project, run this command in your terminal;

~~~sh
$ heroku apps:create flaskinsightherokuapp
Creating ⬢ flaskinsightherokuapp... done
https://flaskinsightherokuapp.herokuapp.com/ | https://git.heroku.com/flaskinsightherokuapp.git
~~~

When you create an app, a git remote (called heroku) is also created and associated with your local git repository.

After checking and you are satisfied that your master branch has all the above, run the following commands to deploy to heroku;

~~~sh
$ git push heroku master
~~~

The application is now deployed. Ensure that at least one instance of the app is running:

~~~sh
$ heroku ps:scale web=1
~~~

Now visit the app at the URL generated by its app name. As a handy shortcut, you can open the website as follows:

~~~sh
$ heroku open
~~~