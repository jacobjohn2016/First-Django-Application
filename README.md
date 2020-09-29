# First Python and Django Application

Source: [scotch.io/tutorials](https://scotch.io/tutorials/build-your-first-python-and-django-application)

* MVT - Model View Template
  * the app gets data from a model, 
  * the view does something to the data and 
  * then renders a template containing the processed information
* Django comes with some default apps preinstalled such as the authentication and session manager apps.

## Setting Up the Environment

```bash
virtualenv -p /usr/local/bin/python3 env
```

* `-p` flag to specify python version
* `env` directory to create version in

## Creating a project

```bash
django-admin startproject helloapp
```

* Creates a skeleton project called `helloapp` with following structure:

```bash
helloapp
├── helloapp
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py # project settings
│   ├── urls.py # routes in the project
│   └── wsgi.py
└── manage.py
```

## Creating an App within Project

```bash
cd helloapp
python manage.py startapp howdy
```

* Now, new folder structure will look like:

```bash
helloapp
├── helloapp
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-38.pyc
│   │   └── settings.cpython-38.pyc
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── howdy # new app
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
└── manage.py
```

* add app to `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'howdy'
]
```

* run server:

```bash
python manage.py runserver
```

## Migrations

* make it easy for you to change your database schema (model) without having to lose any data. 
* Any time you create a new database model, running migrations will update your database tables to use the new schema without you having to lose any data or go through the tedious process of dropping and recreating the database yourself.

```bash
python manage.py migrate
```

## Urls & Templates

* Re-route home page or `/` to show howdy app.
* In `urls.py` inside `/helloapp`:

```python
from django.contrib import admin
from django.urls import path
from django.conf.urls import include
from howdy import urls as howdyurls

urlpatterns = [
    path('admin/', admin.site.urls),
    path('',include(howdyurls))
]

```

* Now, create the url definition in the `howdy` app:
* Create a `urls.py` file in the `howdy` folder and add:

```python
# howdy/urls.py
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$',views.HomePageViews.as_view()),
]
```

* This code imports the views from our `howdy` app (relative import) and expects a view called `HomePageView` to be defined.
* open the `views.py` file in the `howdy` app and define the view as follows:

```python
# howdy/views.py
from django.shortcuts import render
from django.views.generic import TemplateView

# Create your views here.
class HomePageView(TemplateView):
    def get(self, request, **kwargs):
        return render(request, 'index.html', context=None)
```

* Django takes in a `request` and returns a rendered response.
* The `get` method expects a HTTP GET reques to the url defined in our `urls.py`
  * We can rename `get` to `post` to create a post method.
* Once the HTTP GET request has been received:
  * the method renders a `index.html` template 
  * from a `howdy/templates` folder
* Create the `index.html` file as follows:

```html
<!-- howdy/templates/index.html -->
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Howdy!</title>
</head>

<body>
    <h1>Howdy!</h1>
    <h2>I am Learning Django!</h2>
    <a href="/about/">About me</a>
</body>

</html>
```

## Linking Pages

* Create an `about.html` page in the `howdy/templates` folder:

```html
<!-- howdy/templates/about.html -->
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Howdy!</title>
</head>

<body>
    <h1>Welcome to the about page</h1>
    <p>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc quis neque ex. Donec feugiat egestas dictum. In
        eget erat
        sit amet elit pellentesque convallis nec vitae turpis. Vivamus et molestie nisl. Aenean non suscipit velit. Nunc
        eleifend convallis consectetur. Phasellus ornare dolor eu mi vestibulum, ornare tempus lacus imperdiet. Interdum
        et
        malesuada fames ac ante ipsum primis in faucibus. Pellentesque ut sem ligula. Mauris volutpat vestibulum dui in
        cursus.
        Donec aliquam orci pellentesque, interdum neque sit amet, vulputate purus. Quisque volutpat cursus nisl, in
        volutpat
        velit lacinia id. Maecenas id felis diam.

    </p>
    <a href="/">Go back home</a>
</body>

</html>
```

* Add it to the `howdy/urls.py`:

```python
# howdy/urls.py
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$',views.HomePageViews.as_view()),
    url(r'^about/$', views.AboutPageView.as_view())
]
```

* Create a class view in `howdy/views.py` as follows:

```python
# howdy/views.py
from django.shortcuts import render
from django.views.generic import TemplateView

# Create your views here.
class HomePageView(TemplateView):
    template_name = "index.html"


class AboutPageView(TemplateView):
    template_name = "about.html"
```

* If you set the `template_name` attribute, a get request to that view will automatically use the defined template.
* Finally add it to `urls.py`:

```python
from django.contrib import admin
from django.urls import path
from django.conf.urls import include
from howdy import urls as howdyurls

urlpatterns = [
    path('admin/', admin.site.urls),
    path('',include(howdyurls)),
    path('about/',include(howdyurls))
]
```