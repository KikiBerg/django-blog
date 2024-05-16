# INITIATE A DJANGO PROJECT, USEFUL COMMANDS

### Part 1: Creating the project

**Installing packages**: 
- Install the Django Python package: `pip3 install Django~=4.2.1`
- Add it to the requirements.txt file: `pip3 freeze --local > requirements.txt`

**Creating the Django project**:
- `django-admin startproject project_name .`
- **Don't forget the dot at the end.** The dot specifies the directory location. 
- Migrate changes and keep the database updated, when there are changes in models.py: `python manage.py migrate`
- In **my_project/settings.py** file, paste the hostname between the square brackets of `ALLOWED_HOSTS` and save: `ALLOWED_HOSTS = ['8000-reponame-djangoproject-randomdigitsandletters.ws-eu111.gitpod.io']`
- Start the Django server: `python3 manage.py runserver`
--------
### Part 2: Creating the new app
- `python3 manage.py startapp app_name`
- In **settings.py** add the app to the list of `INSTALLED_APPS`
- Surround the app name in single quotes, and use a trailing comma before and after

- In **views.py** import `from django.http import HttpResponse` at the top of the file
- Add a view function to return a HttpResponse e.g. "Hello, Blog!":
`def my_blog(request):
return HttpResponse("Hello, Blog!")`

- In **my_project/urls.py** import the **include** function by appending it after a comma to the existing *django.urls import.*
- Import the my_blog view: `from blog.views import my_blog`
- Add the new path to the urlpatterns: `path('blog/', my_blog, name='blog'),`
--------
### Part 3: Running the server and testing the app
- Always make sure to **save all your files** before running the project. You can also take this opportunity to git add, commit and push your work.
- Open a browser window by returning to the terminal tab and running the Django server `python3 manage.py runserver`. Now you can see the text from the view function in the browser.
- In **settings.py** make sure to have pasted the **hostname** inside the `ALLOWED_HOSTS` before running the project.
--------
### Part 4: Deployment
#### Part 4.1: Create the Heroku app
- On the **Settings tab** click reveal config vars. 
- Add a key of DISABLE_COLLECTSTATIC and a value of 1 and click Add.

#### Part 4.2: Update the code for deployment
- Install a production-ready webserver for Heroku: `pip3 install gunicorn~=20.1`
- Add gunicorn==20.1.0 to the requirements.txt file: `pip3 freeze --local > requirements.txt`
*Note: gunicorn is a production equivalent of the manage.py runserver used in development but with speed and security optimisation.*

- Create a file named **Procfile** at the root directory of the project (same directory as requirements.txt).
*Note: The Procfile has no file extension.*
- In the Procfile, declare this is a web process followed by the command to execute your Django project: `web: gunicorn my_project.wsgi`. *Note: This assumes your project is named my_project.*
- Note the **space** after the colon.

*Note: gunicorn my_project.wsgi is the command heroku will use to start the server. It works similarly to python3 manage.py runserver.*

- In **settings.py** file replace DEBUG=True with DEBUG=False
- Append the Heroku hostname to the ALLOWED_HOSTS list, in addition to the local host: `,'.herokuapp.com'` *Note: Remember the comma and the dot before herokuapp.*
- Push the code to GitHub:
1.  git add .
2. git commit -m "..."
3. git push

#### Part 4.3: Deploy to Heroku
- On the Deploy tab in the Heroku app dashboard: 1. connect to the GitHub repo, 2. click on Deploy Branch
- You will see a 404 error that the current path didn’t match any of the URL patterns as you have not written the urlpattern yet.- Append /app_name (/blog in this case) to the app URL to see the text.

--------

### Part 5: Creating the database

- In the **settings.py** file change the value of DEBUG back to True
> **env.py**
- Create a file named **env.py** at the top level of the project. *Note: You can either create a new file in the file structure or type the terminal command touch env.py*
- Add env.py to the **.gitignore file**. This is to prevent the secret data you will add to it from being pushed to GitHub.
- Import Python's operating system module and use it to set the value of the DATABASE_URL constant to the URL you copied from the chosen PostgreSQL database: `import os`, `os.environ.setdefault(
    "DATABASE_URL", "<your-database-URL>")`

> **Install packages**
- Install the two packages required to connect to your PostgreSQL database: `pip3 install dj-database-url~=0.5 psycopg2~=2.9`
- Then add them to the requirements file: `pip3 freeze --local > requirements.txt` or `pip freeze >> requirements.txt`
- *Note 1: psycopg2 is a driver for interacting with PostgreSQL databases using Python. The dj-database-url Python package is a utility to connect Django to a database using a URL.*
- *Note 2: The packages in requirements.txt are listed alphabetically. The most recently installed package is not always at the end.*
- *Note 3: The requirements.txt file is an important part of the deployment. After each new package is installed, recreate the file.*
- See the installed packages: `pip3 list`

> **Import packages**
- In **settings.py** file, import the appropriate packages
    - Connect the settings.py file to the env.py file*:
        - `import os`
        - `import dj_database_url` *Note: You will use dj_database_url in a later step* 
        - `if os.path.isfile('env.py'): import env`

> **Database connection**
- In the **settings.py** file comment out the local sqlite3 database connection
- Connect to the environment variable DATABASE_URL you previously added to the env.py file: `DATABASES = {
    'default': dj_database_url.parse(os.environ.get("DATABASE_URL"))
}`
- Now our project is connected to the database and we can create database tables with Django's migrate command: `python3 manage.py migrate`

--------

### Part 6: Entering data
Let's enter our first data into the database. As Django comes with built-in auth and admin apps we can create a superuser with admin access to our database:
> **superuser**
- Choose a memorable user name, use your email address and choose a secure password:
`python3 manage.py createsuperuser`
- *Note: Django will check your password security.*

#### USE THE NEXT SECTION CAREFULLY!!!
> **Run commands to view information in CI database:**
Use the Django shell in order to run commands to view this information.
The Django shell allows you to write Python statements from the command line although they're being executed from within Django.
Run the command `python3 manage.py shell` to open the Django shell, where you can type the code line by line, or paste it in one go.
- We can query the tables that exist in the database with the following code snippet:

    - `from django.db import connection`
    - `table_names = connection.introspection.table_names()`
    - `print(table_names)`

- To query the users within the User model, you can run the following code snippet:
    - `from django.contrib.auth.models import User`
    - `users = User.objects.all()`
    - `for user in users:
    print(f"Username: {user.username}, Email: {user.email}, First Name: {user.first_name}, Last Name: {user.last_name}")`

- In order to exit the Django shell, type `exit`
--------
### Part 4b: Deployment and connecting Heroku to the PostgreSQL database
- On the **Settings tab** click reveal config vars. 
- Add a new config var with a key of `DATABASE_URL` and the value of the PostgreSQL database URL.
- *Note: You can copy this from your env.py file (minus the quotes)*

--------

### Part 7: SECRET_KEY
> **env.py**
- Make up a new SECRET_KEY value: `os.environ.setdefault(
    "SECRET_KEY", "comeupwithacombinationofletters,numbersandsymbols"
)`

> **settings.py**
- Modify the settings.py file to retrieve the new SECRET_KEY from the environment variables: `SECRET_KEY = os.environ.get('SECRET_KEY')`

> **Heroku**
- Set the SECRET_KEY as a config variable on Heroku
- Push the code to Github and redeploy
- *Note: If you run both the local and browser app, and get no errors after adding the SECRET_KEY to both, it should mean the change has been successful.*

--------

### Part 8: Creating the model
- Debug > True
- In my_app/**models.py** file add a new import at the top for the User model: `from django.contrib.auth.models import User`
    - Create a class named `Post` below the comment inheriting from the `Model` class: `class Post(models.Model):`
        - Add an attribute *title* defined as a *character field* with a max length of 200 characters: `title = models.CharField(max_length=200, unique=True)` Note: The title values should be unique to avoid having blog posts of the same name confusing your users.
        - Add an attribute *slug* defined as a *slug field* with a max length of 200 characters: `slug = models.SlugField(max_length=200, unique=True)` Note: In Django, the slug is what you'll use to build a URL for each of your posts. 
        - Add an attribute *author* defined as a *Foreign Key* to the User model: `author = models.ForeignKey(
    User, on_delete=models.CASCADE, related_name="blog_posts"
)` Note: One user can write many posts, so this is a one-to-many or Foreign Key. The cascade on delete means that on the deletion of the user entry, all their posts are also deleted.
        - Add an attribute *content* defined as a *text field*: `content = models.TextField()` Note: This is the blog article content.
        - Add an attribute *created_on* defined as a *date time field*: `created_on = models.DateTimeField(auto_now_add=True)` Note: The auto_now_add=True means the default created time is the time of post entry.
        - Add an attribute *status* defined as an *integer field* with a default of 0: `status = models.IntegerField(choices=STATUS, default=0)`
            - Create the constant STATUS above the class as a tuple: `STATUS = ((0, "Draft"), (1, "Published"))` Note: A draft is defined as zero and published as one, so you can see the default is to save as a draft.     

--------

### Part 9: Use the model to update the database
Now we have created a Post model we need to convert that Python class into instructions for the creation of the database table structure.

- `python3 manage.py makemigrations blog` Note: A blog/migrations/0001_initial.py file is created containing the instructions on what table to build.
- `python3 manage.py migrate blog`: we create that table in the database.
- In my_app/**admin.py** file import the Post model and register it:
    - `from .models import Post`
    - `admin.site.register(Post)` (Register it here: *# Register your models here.*)
- In my_project/**settings.py** file add:
    - `CSRF_TRUSTED_ORIGINS = [
    "https://*.codeanyapp.com",
    "https://*.herokuapp.com"
]` Note: you need to add **your local development server URL domain** (e.g gitpod.io if you work on Gitpod) and your **production server URL domain**
- Start the Django server in the terminal: `python3 manage.py runserver`
    - open the app and append /admin to the URL in the browser
    - log in as the superuser
    - There is now a Posts option under Django Administration.

- Debug > False
- Git add, commit, push
- Deploy on heroku > Open the app, append /admin to the URL in the browser and log in as the superuser. There is now a Posts option under Site Administration. 

--------


### Good habits

+ Deploy early and regurarly
+ Add, commit and push to Github often
+ Recreate **requirements.txt** file after every new package
+ Always switch **DEBUG** to **False** before deploying

--------

### Key files in project folder:
The top level in Django is a project. A project is like a container for everything we want to do. By default, the project contains a settings file and some other administrative files.
- Key files to note are: **manage.py**, **settings.py**, and **urls.py**.

1. **manage.py**: this file is in the root directory, above the project folder. It is used to create apps, run our project and perform some database operations.

2. **settings.py**: this file contains the project-wide settings, such as installed apps and database connection information, among other things.

* Key settings in settings.py:
  * `ALLOWED_HOSTS`: Determines which host or server names the project can run on.
  * `SECRET_KEY`: Defines an encryption key for leaving a virtual signature.
  * `INSTALLED_APPS`: Provides a list of applications that are enabled for this Django installation.
  * `DEBUG`: Allows for the extended, yellow error messages to be displayed.

3. **urls.py**: This file defines how URLs in the Django project map to specific views in the apps. It essentially acts as a routing table, directing users to the appropriate view functions based on the URL they request. The imported views from the apps are responsible for handling user requests and generating the appropriate responses.

--------

### Key files in apps folder:
Inside the project, we create apps. An app’s structure is like a Python package with multiple Python modules within a directory. They’re the things that actually do something, such as a blog, a to-do list, or a poll. A project can contain many, many apps. You could do everything you want with just one app, but for maintainability and good design practices, it is better to separate different functionality into different apps.

* Key files to note are: **models.py**, **views.py**, **admin.py**
  * **models.py**: our database models are stored here, which define the structure of the database used by our app.
  * **views.py**: this file contains the view code for our app. 
  * **admin.py**: the admin.py file is where we register our custom models so that they can be accessed through the admin panel.