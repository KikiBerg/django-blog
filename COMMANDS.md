# INITIATE A DJANGO PROJECT, USEFUL COMMANDS

### Part 1: Creating the project

**Installing packages**: 
- Install the Django Python package: `pip3 install Django~=4.2.1`
- Add it to the requirements.txt file: `pip3 freeze --local > requirements.txt`
--------
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
--------
- In **views.py** import `from django.http import HttpResponse` at the top of the file
- Add a view function to return a HttpResponse e.g. "Hello, Blog!":
`def my_blog(request):
return HttpResponse("Hello, Blog!")`
--------
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
