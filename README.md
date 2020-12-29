
#### Project Setup

- if `venv` is not installed:

    - for Mac or Linux:

        - python3 -m pip install --user virtualenv  

    - for Windows:

        - py -m pip install --user virtualenv

1) Create <a href = "https://docs.python.org/3/library/venv.html"> virtual environment </a>

    ~~~ bash
    python3 -m venv car_env
    ~~~

    - if Python 3.7 needed

        ~~~ bash
        python3.7 -m venv ~/venv/py3.7
        ~~~

    - if using Windows

        ~~~ bash
        py -m venv car_env
        ~~~

2) Activate virtual environment

    - run `deactivate` within your `venv terminal` to exit the virtual environment 
    - the command line path should begin with `(car_env)`

        ~~~ bash
        source car_env/bin/activate
        ~~~

    - if venv created using Python 3.7

        ~~~ bash
        source ~/venv/py3.7/bin/activate
        ~~~

    - if using Windows

       ~~~ bash
       .\env\Scripts\activate
       ~~~

3) Install Django

    ~~~ bash
    pip install django
    ~~~

4) Create a project directory

    - Syntax `django-admin startproject name [directory]`
    - The `.` tells Django to install the project files in the current directory

       ~~~ bash
       django-admin startproject carproj .
       ~~~

    - the following directory structure is created

        <img src = "proj_files_orig.png" width = "10%"/>

5) Launch default project
    - At this time, ignore any warnings regarding unapplied migrations
    - The default server runs at http://127.0.0.1:8000/
    - The default server does not serve static files but can be configured to do so
    - `CTRL + BREAK` quites server

        ~~~ bash
        python manage.py runserver
        ~~~

    - the default project should be running
        <img src = "default_proj_runserver.png" width = "30%"/>

6) After quitting server, install an app named `pages`

    - Syntax `django-admin startapp name [directory]`
    - `python manage.py` can be used instead of `django-admin`

        ~~~ bash
        python manage.py startapp pages
        ~~~

    - the following directory structure is created
        <img src = "pages_app_default.png" width = "10%"/>

7) Add the `pages` app to the project's `settings.py`

    - open pages/app.py and locate the class name

        ~~~ py
        class PagesConfig(AppConfig):
            name = 'pages'
        ~~~

    - add this to `settings.py`

        ~~~ py
        ...
        INSTALLED_APPS = [
        'pages.apps.PagesConfig',
        'django.contrib.admin',
        'django.contrib.auth',
        ~~~    

8) Update the project's `urls.py` to include `pages.urls`
    
    - add the `include` import
    - the `''` first argument points to the home page
    - the `namespace` pages allow precise reversing of `named URL patterns`

        ~~~ bash
        from django.contrib import admin
        from django.urls import path, include

        urlpatterns = [
            path('admin/', admin.site.urls),
            path('', include('pages.urls', namespace = 'pages'))
        ]
        ~~~        

9) Create `urls.py` in the app's `pages` folder to map URLs to views

    ~~~ py
    from django.urls import path
    from . import views

    urlpatterns = [
        path('', views.home, name = 'home'),
    ]
    ~~~

10) Create the `home` view in pages app `views.py`

    - A view is a callable which takes a request and returns a response
    - Django provides base view classes which will suit a wide range of applications. All views inherit from the View class, which handles linking the view into the URLs, HTTP method dispatching and other common features
    - the `home` view will render a `template`

        ~~~ py
        from django.shortcuts import render

        # Create your views here.
        def home(request):
            return render(request, 'pages/home.html')
        ~~~

11) Create the `home` template

    - Add `template\pages\pages.html` within the top-level project folder

        <img src = "templates_folder_location.png" width = "20%">

        ~~~ html
        <h2> Test Home page </h2>
        ~~~

12) Add `templates` to the `DIRS` key in  `settings.py`

    ~~~ py
    ...
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': ['templates'],
            'APP_DIRS': True,
            ...
    ~~~

13) Test that template renders simple test page

    - restart the server `python manage.py runserver` & refresh the browser
    - the test page should now render

        <img src = "test_template.png" width = "30%"/>

## Building up project

### Set up static templates

1) Create landing page in `home.html`

2) Create static folder inside `carproj` and add static content
    - css, fonts, img, js

3) Register static files inside of `settings.py`, then run `collectstatic`
    - add os import   `import os`
    - `os.path.join` joins path names into a single path
    - add to the end of the file

        ~~~ py
        ...
        STATIC_URL = '/static/'
        STATIC_ROOT = os.path.join(BASE_DIR, 'static')  # (BASE_DIR, 'db.sqlite3)'
        STATICFILES_DIRS = [
            os.path.join(BASE_DIR, 'carproj/static'),
        ]
        ~~~

    - `collectstatic` collects and organizes the static files into the absolute location of the collected files provided in STATIC_ROOT

        ~~~ bash
        python manage.py collectstatic
        ~~~

    - this creates a `static` folder inside the `carproj` folder. Django also creates the `admin` folder
  
        <img src = "static_folder.png" width = "20%"/>

4) Modify `home.html` to use the static files

    - add {% load static %} to the top of the file. This directs Django's template engine to use files from the `static` folder for the template
    - modify all `href` that include css, ico, or js files
    - also do the same for the `src`  in img tags with the alt tag `banner`

        - before

            ~~~ html
            <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
            ...
            <img class="d-block w-100 h-100" src="img/car-1.jpg" alt="banner">
            ...
            ~~~

        - after

            ~~~ html
            <link rel="stylesheet" type="text/css" href="{% static 'css/bootstrap.min.css' %}">
            ...
            <img class="d-block w-100 h-100" src="{% static 'img/
            car-1.jpg' %}" alt="banner">
            ...
            ~~~

    - update paths to `img/logos/black-logo.png` to `static/img/logos/black-logo.png` since the header img will not be rendered dynamically
    - NOTE: you may need to clear the browser cache to see this change take effect

5) Set up Base Template

    - Create `base.html` inside of the `templates` folder
    - Move everything from top of `home.html` to `<!-- Main header end -->` into `base.html`
    - Remove the `body` and `html` closing tags from `home.html` and add to the bottom of `base.html`
    - Remove all `script` tags from the bottom of `home.html` and move to the bottom of `base.html` above the closing `body tag`
    - Above the first `script` tag, add the following to allow rendering of `home.html`  content after the header content defined in `base.html`
        ~~~ html
        {% block content %}

        {% endblock %}
        ~~~

    - Configure `home.html` to use base template

        - At the top of `home.html` add:
    
            ~~~ html
            {% extends 'base.html' %}

            {% block content %}

            {% load static %}
            ~~~

        - At the bottom of `home.html` add  `{% endblock %}` to indicate the end of the content

6) Break up `base.html` into sections

Create an `includes` folder within the `templates` folder and create the following files:

- `footer.html`
- `navbar.html`
- `topbar.html`

    - Remove the `top header` code from `base.html` and copy into `topheader.html`
    - Remove the `main header` code from `base.html` and copy into `navbar.html`
        - add `{% load static %}` to the top of the `navbar.html`
        - update `navbar.html` to utilize static files
            - before 

                ~~~ html
                <img src="img/logos/black-logo.png" alt="logo">
                ~~~ 

            - after
                ~~~ html
                <img src= "{% static 'img/logos/black-logo.png' %}"  alt="logo">
                ~~~
    - Remove the `footer` code from `home.html` and copy into `footer.html`

### Adding Dynamic routes

1) Add routes to the project `urls.py ` file for the about, services, & contact pages

    ~~~ py
    urlpatterns = [
        path('', views.home, name = 'home'),
        path('about', views.about, name = 'about'),
        path('services', views.services, name = 'services'),
        path('contact', views.contact, name = 'contact'),
    ]
    ~~~

2) Create views in `views.py`

    ~~~ py
    def about(request):
        return render(request, 'pages/about.html')

    def services(request):
        return render(request, 'pages/services.html')

    def contact(request):
        return render(request, 'pages/contact.html')
    ~~~

3) Create `about.html` inside of `templates/pages`

    ~~~ html
    {% extends 'base.html' %}

    {% block content %}
    {% load static %}

        <!-- Sub banner start -->
        <div class="sub-banner overview-bgi">
            <div class="container breadcrumb-area">
                <div class="breadcrumb-areas">
                    <h1>About Us</h1>
                    <ul class="breadcrumbs">
                        <li><a href="{% url 'home' %}">Home</a></li>
                        <li class="active">About Us</li>
                    </ul>
                </div>
            </div>
        </div>
        <!-- Sub Banner end -->

        <!-- Service center start -->
        <div class="about-car content-area-5">
            <div class="container">
                <div class="row">
                    <div class="col-xl-5 col-lg-6">
                        <div class="about-car-photo">
                            <div id="carDetailsSlider" class="carousel car-details-sliders slide">
                                <!-- main slider carousel items -->
                                <div class="carousel-inner">
                                    <div class="active item carousel-item" data-slide-number="0">
                                        <img src = "{% static 'img/car/car-1.jpg' %}" class="img-fluid" alt="slider-car">
                                    </div>
                                    <div class="item carousel-item" data-slide-number="1">
                                        <img src = "{% static 'img/car/car-2.jpg' %} class="img-fluid" alt="slider-car">
                                    </div>
                                    <div class="item carousel-item" data-slide-number="2">
                                        <img src = "{% static 'img/car/car-3.jpg' %} class="img-fluid" alt="slider-car">
                                    </div>
                                </div>
                                <!-- main slider carousel nav controls -->
                                <ul class="carousel-indicators car-properties list-inline nav nav-justified">
                                    <li class="list-inline-item active">
                                        <a id="carousel-selector-0" class="selected" data-slide-to="0" data-target="#carDetailsSlider">
                                            <img src = "{% static 'img/car/car-1.jpg' %}" class="img-fluid" alt="small-car">
                                        </a>
                                    </li>
                                    <li class="list-inline-item">
                                        <a id="carousel-selector-1" data-slide-to="1" data-target="#carDetailsSlider">
                                            <img src = "{% static 'img/car/car-2.jpg' %}" class="img-fluid" alt="small-car">
                                        </a>
                                    </li>
                                    <li class="list-inline-item">
                                        <a id="carousel-selector-2" data-slide-to="2" data-target="#carDetailsSlider">
                                            <img src = "{% static 'img/car/car-3.jpg' %}" class="img-fluid" alt="small-car">
                                        </a>
                                    ...
        <!-- Service center end -->



        <!-- Our team start -->
        ....
        <!-- Our team end -->   
    {% endblock %}
    ~~~

    ~~~ html
    <h2> Simple services page </h2>
    ~~~

    ~~~ html
    <h2> Simple contact page </h2>
    ~~~

4) Create `services.html` inside of `templates/pages` 

    ~~~ html
    {% extends 'base.html' %}

    {% block content %}
    {% load static %}

        <!-- Sub banner start -->
        <div class="sub-banner overview-bgi">
            <div class="container breadcrumb-area">
                <div class="breadcrumb-areas">
                    <h1>Services</h1>
                    <ul class="breadcrumbs">
                        <li><a href = "{% url 'home' %}">Home</a></li>
                        <li class="active">Services</li>
                    </ul>
                </div>
            </div>
        </div>
        <!-- Sub Banner end -->

        <!-- Services start -->
        ...
        <!-- Services end -->


    {% endblock %}
    ~~~

5) Create `contact.html` inside of `templates/pages`

    ~~~ html
    {% extends 'base.html' %}

    {% block content %}
    {% load static %}

        <!-- Sub banner start -->
        ...
        <!-- Sub Banner end -->

        <!-- Contact 2 start -->
        ...
        <!-- Contact 2 end -->

    {% endblock %}
    ~~~

6) Move the `Full Page Search` and `Car Overview Modal` into the bottom `base.html` to include the search functionality on every page

    ~~~ html
    {% include 'includes/navbar.html' %}

    <!-- this is the main content from home.html-->
    {% block content %}

    {% endblock %}

    <!-- Full Page Search -->
    <div id="full-page-search">
        <button type="button" class="close">×</button>
        <form action="https://storage.googleapis.com/theme-vessel/carhouse/index.html#" class="search-header">
            <input type="search" value="" placeholder="type keyword(s) here. Eg: audi, benz etc" />
            <button type="submit" class="btn btn-sm button-theme">Search</button>
        </form>
    </div>

    <!-- Car Overview Modal -->
    ...
    ~~~



7) Update the links in `navebar.html`

    ~~~ html
        <li class="nav-item">
            <a class="nav-link" href="{% url 'about' %}">
                About
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" href="{% url 'services' %}">
                Services
            </a>
        </li>


        <li class="nav-item dropdown">
            <a class="nav-link" href="{% url 'contact' %}">Contact</a>
        </li>
    ~~~


