# Django-CMS Installation

## Preparation

To install and create requirements file for django-cms for Django 2.0.9:

```
$ python3.6 -m venv venv
$ source venv/bin/activate
$ pip install --upgrade pip
$ pip install --upgrade setuptools
$ pip install -r requirements/local.txt
$ pip install django-cms
$ pip install django-filer
$ pip install djangocms-text-ckeditor
$ pip install djangocms-link djangocms-file djangocms-picture djangocms-video \
djangocms-googlemap djangocms-snippet djangocms-style djangocms-column
$ pip freeze > requirements/django-cms.txt
```

## Django Project

Create a new django project, or work with an existing project.

```
$ django-admin.py startproject <project-name>
```

## Django Project Settings

Modify the base settings for the Django Project to configure django-cms

### Installed Apps

```
INSTALLED_APPS = [
    # This provides some styling that helps make django CMS administration
    # components easier to work with. Optional, but recommended.
    'djangocms_admin_style',
    'django.contrib.admin',
    ...
    # cms and menus are the core django CMS modules.
    'cms',
    'menus',
    # django-treebeard is used to manage django CMS’s page and
    # plugin tree structures.
    'treebeard',
    # sekizai required by django-cms
    'sekizai',
    'filer',
    'easy_thumbnails',
    'mptt',
    # Most useful optional django-cms plugins
    'djangocms_text_ckeditor',
    'djangocms_link',
    'djangocms_file',
    'djangocms_picture',
    'djangocms_video',
    'djangocms_googlemap',
    'djangocms_snippet',
    'djangocms_style',
    'djangocms_column',
]
```

### Middleware

```
MIDDLEWARE = [
    ...
    # Required by django-cms
    'django.middleware.locale.LocaleMiddleware',
    'cms.middleware.user.CurrentUserMiddleware',
    'cms.middleware.page.CurrentPageMiddleware',
    'cms.middleware.toolbar.ToolbarMiddleware',
    'cms.middleware.language.LanguageCookieMiddleware',
    'cms.middleware.utils.ApphookReloadMiddleware',
]
```

### Templates

```
TEMPLATES = [
    {
        ...
        'DIRS': [
            ...
            'templates',
        ],
        ...
        'OPTIONS': {
            'context_processors': [
                ...
                # sekizai required by django-cms
                'sekizai.context_processors.sekizai',
                'cms.context_processors.cms_settings',
            ],
        },
    },
]
```

### Other Settings

```
# django.contrib.sites, required by django-cms
SITE_ID = 1

# Django CMS requires you to set the LANGUAGES setting. This should list all
# the languages you want your project to serve, and must include the language
# in LANGUAGE_CODE.

LANGUAGES = [
    ('mr', 'Marathi'),
    ('hi', 'Hindi'),
    ('en-us', 'English'),
]

# Django CMS requires at least one template for its pages. The first template
# in the CMS_TEMPLATES list will be the project’s default template.

CMS_TEMPLATES = [
    ('home.html', 'Home page template'),
]

# Add a media root for Django CMS local development configuration

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

# Django CMS thumbnail plugin

THUMBNAIL_HIGH_RESOLUTION = True

THUMBNAIL_PROCESSORS = (
    'easy_thumbnails.processors.colorspace',
    'easy_thumbnails.processors.autocrop',
    'filer.thumbnail_processors.scale_and_crop_with_subject_location',
    'easy_thumbnails.processors.filters'
)
```

## URL Configuration

Modify the main app's urls.py file to include the django CMS URLs. For
deployment, you also need to configure suitable media file serving.

```
from django.contrib import admin
from django.urls import path
from django.conf.urls import url, include
from django.conf import settings
from django.conf.urls.static import static


urlpatterns = [
    path('admin/', admin.site.urls),
    url(r'^', include('cms.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## Create a home template

In the root of the project, create a ```templates``` directory, and in that, ```home.html```, a minimal django CMS template:

```
{% load cms_tags sekizai_tags %}
<html>
    <head>
        <title>{% page_attribute "page_title" %}</title>
        {% render_block "css" %}
    </head>
    <body>
        {% cms_toolbar %}
        {% placeholder "content" %}
        {% render_block "js" %}
    </body>
</html>
```

This is worth explaining in a little detail:

 - ```{% load cms_tags sekizai_tags %}``` loads the template tag libraries we use in the template.
 - ```{% page_attribute "page_title" %}``` extracts the page’s page_title attribute.
 - ```{% render_block "css" %}``` and ```{% render_block "js" %}``` are Sekizai template tags that load blocks of HTML defined by Django applications. django CMS defines blocks for CSS and JavaScript, and requires these two tags. We recommended placing ```{% render_block "css" %}``` just before the ```</head>``` tag, and and ```{% render_block "js" %}``` tag just before the ```</body>```.
 - ```{% cms_toolbar %}``` renders the django CMS toolbar.
 - ```{% placeholder "content" %}``` defines a placeholder, where plugins can be inserted. A template needs at least one ```{% placeholder %}``` template tag to be useful for django CMS. The name of the placeholder is simply a descriptive one, for your reference.

## Database Configuration

Create database tables and check CMS configuration

```
$ python manage.py migrate
$ python manage.py createsuperuser
```

## Check CMS Configuration

Check the Django CMS Configuration

```
$ python manage.py cms check
```

## Launch the project

Start up the runserver:

```

$ python manage.py runserver
```

and access the new site, which you should now be able to reach at ```http://localhost:8000```. Login if you haven’t done so already.

