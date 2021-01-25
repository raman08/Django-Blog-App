# Django Notes

## Installation

- Create a Virtual ENV and install django.
```
pip install django
```

## Creating the new project

- To create a project run
```bash
django-admin startproject <project_name>
```

- Run local host via
```bash
python manage.py runserver
```

##### Directory Structure

1. manage.py => Main manager of our application.
2. <project_name>/urls.py => The place where site looks for the path in our application. Like Table of Contents.
3. <project_name>/settings.py => Settings for our project.


## Creating A new Application.

- To create A new App run
```bash
python manage.py startapp <appName>
```

This will create a New director named <appName> into the root of project.

The views.py is the main file where all our endpoints will be renders.

In views.py, we need to create the functions which will handle the requests submitted by the users.

Also we need to create the urls.py inside the <appName> folder which will handle the urls request to that application.

The urlpattrens has structue like this:

```py
urlpattrens = [
	path('endpoint/', the functions to call, name='name'),
	...
]
```

In main application we need to add the <appName>/urls.py. To do this add

```py
path('<appName>/', include('<appName>/urls.py'))
```

into the main urls.py file inside the urlpattrens list.


## Using Templates

The first thing to do after creating the app is to link it with the main project for looking templates. To do this goto the <appName>/app.py and copy the class name.

Then go to the <project_name>/settings.py and add
` <appName>.apps.<className>, ` to the INSTALLED_APPS list.

This will help django to link the application to our main project.

Next thing to do is to create the `templates/<appName>` folder in the <appName> folder.

Then create the templates html as normal.

##### Linking the htmls to the endpoint

To link the html, simplely go to the required function in the `views.py` and then add
```py
return render(request, <path/to/html/excluding_the_templates_directory>)
```

This will render the html and display it to the webPage when go to the specific link.

To use any external data into our html we just need to add the object/dictnory of our data as our third argument in render function and add the specific key-value pair as required.

**For example:**

Let the data to be add is

```py
posts = [
	{
		'author': 'Raman',
		'title': 'Blog Post 1',
		'content': 'First Post',
		'date_posted': 'Jan 27, 2021'
	},
	{
		'author': 'Raman',
		'title': 'Blog Post 2',
		'content': 'Second Post',
		'date_posted': 'Jan 28, 2021'
	},
	{
		'author': 'Raman',
		'title': 'Blog Post 3',
		'content': 'Third Post',
		'date_posted': 'Jan 29, 2021'
	},

]

```

We just need to wrap in the context directory in our view function like:
```py
def hii(request):
	context = {
		'posts': posts,
	}

	return render(request, 'path/to/html', context)
```

Then we can use posts list in our html like that:
```html
{% for post in posts %}
		<h2>{{ post.title }}</h2>
		<p>By {{post.author}} on {{post.date_posted}}</p>
		<p>{{post.content}}</p>
{% endfor %}
```


### Django Templates Snipptes

- **for loops**

```py
{% for test in tests %}
	html...........
{% endfor %}
```

- **if-else**
```py
{% if condition %}
	html..........
{% else %}
	html..........
{% endif %}
```


### Using template Inheritence

If we have same code if all of our html and wnat to reduce it we can simpley create the base.html in our html templates and then extends it to the required files.

To extend the file simpley add the following line in the required file.

```
{% extends 'location/to/base.html'}
```

To add actual code snippets in the required portions we can use blocks.

In base.html add the blockes in the required portion.

```
{% block <blockName> %}
{% endblock <blockName> %}
```

To add the data in the required block just add the following in the html as per requirment

```
{% block <blockName> %}
	html...........
{% endblock <blockName> %}
```

## Loading Statics files

To load the static files, just create a folder called static in the <appName> folder and add the static files there

To link those, we need to link the static folder in our base.html. To do the same add the `{% load static %}` before linking any static files.

Then to link a file in the html just use required tag with link `{% static 'file/location' %}` and the files get links.

<hr>

If you want to link other pages it is recomended to use the django url then the normal ones. In the urls.py file you have to give name to the required url and then add `{% url 'url_name' %}`. This will be easy to mantain if any of the endpoints changes.

## Acessing the admin page

There is no default user in the django.

To create a new superUser run the following commands:

```
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
```

After the last command this will ask for username and password.

To login just go to the `/admin` and fill your credentials.


## Working With DataBases

The database table are refered as models.

To create a model simpely go to `models.py` in <appName> and create a class with name of your table. Then simply add the name of fiels and type.Save the file and run the following commands.

```
python manage.py makemigrations
python manage.py migrate
```

Now to acess the recently created database there are two ways.

1. **Use shell**

For this run command
```bash
python manage.py shell
```

This will give us a python shell where we can run our databse commands.

2. **Using Admin Pannel**

To use this we first need to regiser our models to the admin page.

- To do so first open the `admin.py` file and import the model.
- Add the following line to register the model.

```py
admin.site.register(<modelName>)
```

- After that you can change and add the data into your models.


#### Some basic database commands

- To get all the data from a particular models

```
<model>.objects.all();
```

- To get the first and last element of the model

```
<model>.objects.first();
<model>.objects.last();
```

- To fiter the contents

```
<model>.objects.filter(key=value);
```


## Creating a Regestation Page

To register a new users we can use buildin classes from `django.contrib.auth.forms` and create a new template. Or we can build our own regestraion template and add it to our path.

*It is needed to have a csrf token in our form for security reasons*

After submiting the form if nothing is done, it will send the post request to the page itself.

For making it stop to do so we need to validate the form and redirect it.

##### Form Validation

To validate the form first grab the post data (if using `UserCreationForm` then use .is_valid funtion).

After validaiton we can use the redirect function from `django.shortcuts` and redirect it to other page.

Example:

```py
if request.method == 'POST':
	form = UserCreationForm(request.POST)

	if form.is_valid():
		return redirect('blog-home')
```

Also we can use the message funcationly to send the flash message.

For that we need to import the messages class and send the message.

```py
from django.contrib import messages
messages.<level>(request, <message_body>')
```

**Avilable levels**

- debug
- info
- sucess
- warning
- error

To display the message use the following in the templates.

```HTML
{% if messages %}
	{% for message in messages %}
		HTML........
	{% endfor %}
{% endif %}
```

#### Adding Custom Fields

If you use the `UserCreationForms` out of the box, they ounl have the userName and Password fields. But if we Want to add Custom fields we can easily do so.

For that,
- Create an new python module.
- Import `forms` from `django` and `UserCreationForms` from `django.contrib.auth.forms`.
- Then create a new class and inherit the `UserCreationForm` or any other avilable form.
- Add the custom fields.
- Create a `meta` class and select the model to use and the order of fileds to display.

**For example:** Create a form with email field

```py
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm

class UserRegisterForm(UserCreationForm):
	email = forms.EmailField()

	class Meta:
		model = User
		fields = ['username', 'email', 'password1', 'password2']

```

- After that import this class to `views.py` and use that.

##### Customzing the form

To customized the form we can use the `django-crispy-forms`.

[Documentation](https://django-crispy-forms.readthedocs.io/en/latest/index.html#) Here.

## Creating Login Logout System

Django has the buildin login-logout system.
To acess this simpely add the following snippet to the `urls.py` as required.

```py
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout'),
]
```

But it doesn't have the templates associated with it. So we will need to create the templates for the same.
We can set the custom templates location with the `template_name` parameter.

The login page has custiom variable `form` which contain all the login form views.

After the login if we want user to redirect to the other page, we need to set the `LOGIN_REDIRECT_URL` to `link_name` of the required name.

```py
LOGIN_REDIRECT_URL = 'url_name'
```


To authenticate the login we can use `user.is_authenticated` globle variable and change the templates accordingly.


##### Blocking the user to acess the certain pages without login

If we want to block the users to block withot login we can use `login_required` decorator.

In the `views.py` add the following

```py
from django.contrib.auth.decorators import login_required
```

Then add the decorator `login_required` to the required views.

**For example:** To stop the user to nevigate to profile view without login add the following

```py
@login_required
def views(request):
	......
```

This will automaticly redirect the user to login page.

But if the login page in other dirctory, go to `settings.py` and add the following

```py
LOGIN_URL = 'login_link_name'
```


## Creating User Profile

The default user model dosen't include the very much settings.
To add it we can create the new model in `model.py` in our application.

We need to do the migrations to add our model to our database.

After creating our new model we can link the model to aur admin page by adding our model to admin.py in aur project root.

After linking the model we can add user profiles via admin pannel.


#### Setting up uploads directory

By default our upladed files are stored in our project root. If we want to change it to other location we can add the following variables in `settings.py`

```py
# Directory Where Uploads are stored
MEDIA_ROOT = os.path.join(BASE_DIR, '<location>')

# How are we going to acess in our app
MEDIA_URL = '/<url_to_acess>/'
```

#### Creating profile when user created

When the new user register the app we need to create the user profile. To do so, we can use signals.

The signal we will be using is called `post_save`. This signal will be fired when an object is created.

This will need a sender, and a receiver. The user will be the sender which will fire up the signal and the receiver is the function will do some work when the signal is fired.

**For Example:**

```py
@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
	if created:
		Profile.objects.create(user=instance)


@receiver(post_save, sender=User)
def save_profile(sender, instance, **kwargs):
	instance.profile.save()
```

When the User profile is created it will send the post_save signal and the receiver(in this case the functions) will be run.

The first function will check if the user profile is created and if so then it will create the profile with the user of the instance.

The second function will run after the first and will save the profile that was created.


#### Updating the user profile

If we want to update the user profile from the front-end we can just create the form (as in regestration form and add it to the profile templates).

## Class based views

We can also work with classes to view our models.

The basic type of class based model is avilable in the django like list_view, delete, update, details etc. We can import that and extend them as required.

**_NOTE:_**
- By default the class view will look for the templates as `<app>/<model>_<viewtype>.html`. To overrite this we can define `template_name` variable and set it to the url of our template.

- The context name is predine as `object_name`. To overrite this we need to define other variable called `context_object_name` and set it to the name we want.


## Pagination

If we have a lot of objects on one page and want to seprate them we can use Pagination.

I will seprate the objects into pages of specific size.

**Shell Commands**

```py

#importing the paginator
from django.core.paginator import Paginator

# Creating the dummy object
posts = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10']

# Creating Paginator
p = Paginator(post, 3) # Params: object to be paginated, no of object per page

p.num_pages ## find total no of pages

p1 = p.page(1) ## Select a specific page.

p1.number ## Finding the pageno of the object

p1.object_list ## Listing the objects on the page

p1.has_previous() ## Find if the page has previous page.

p1.has_next() ## Find if the page has next page.

p1.next_page_number() ## Find the next page no.

p1.previous_page_number() ## Find the previous page no.
```

If you are using the class based views we can simply put `paginate_by` and set it to no of objects per page.

## Resetting Password

To reset the user password we will send user a mail contanning the token to reset the password.

- First we need to create the route to initiate the password rest.

```py
path('<route1>/', auth_views.PasswordResetView.as_view(template_name='users/<template_name>.html'), name='password_reset'),
```

- Fill the required details. It will send the email to the required user. After that it will redirect us to the `PasswordResetDoneView` view.

```py
path('<route1>/<route2>/', auth_views.PasswordResetDoneView.as_view(template_name='users/<template_name>.html'), name='password_reset_done'),
```

- Then go to the mail and it will send the link to reset password. Click on that and you will be redirected to `PasswordResetConfirmView` view. Note that the tokens are required.

```py
path('password-reset-confirm/<uidb64>/<token>/', auth_views.PasswordResetConfirmView.as_view(template_name='users/<template_name>.html'), name='password_reset_confirm'),
```

- Enter the new password and you will be redirected to the `PasswordResetCompleteView` view.

```py
path('password-reset-complete', auth_views.PasswordResetCompleteView.as_view(template_name='users/<template_name>.html'), name='password_reset_complete'),
```

## Sending Mail

Go to the `setting.py` file and add the following variables.

```py
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_Port = 587
EMAIL_USE_TLS = True

EMAIL_HOST_USER = 'email'
EMAIL_HOST_PASSWORD = 'password'
```