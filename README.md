# Django Appointment 📦

🆕 **New**: The documentation website is now
available [here](https://django-appt-doc.adamspierredavid.com). It includes reasons for using the
application, its features, and detailed configuration instructions.

⚠️ **IMPORTANT**: If upgrading from a version before 2.x.x, please note significant database changes were introduced in
version 2.0.0. Please read
the [migration guide](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/master/docs/migration_guides/v2_1_0.md) before
updating. Version 3.x.x introduces the ability to send email reminders for appointments using Django Q for efficient
task scheduling. It also allows clients to reschedule appointments if it is allowed by admins.

Django-Appointment is a Django app engineered for managing appointment scheduling with ease and flexibility. It enables
users to define custom configurations for time slots, lead time, and finish time, or use the default values
provided. This app proficiently manages conflicts and availability for appointments, ensuring a seamless user
experience.

For a detailed walkthrough and live example of the system, please refer to
[this tutorial](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/master/docs/explanation.md).

Detailed documentation can be found in
the [docs' directory](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/master/docs/README.md).
For changes and migration information, please refer to the release
notes [here](https://github.com/DevDenysVuikiv/Appointment-scheduler/releases)
and [here](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/master/docs/release_notes).

## Features ✨

1. Customizable time slots, lead time, and finish time.
2. Competent handling of appointment conflicts and availability.
3. Seamless integration with the Django admin interface for appointment management.
4. Custom admin interface for managing appointment/staff member editing, creation, availability, and conflicts.
5. User-friendly interface for viewing available time slots and scheduling appointments.
6. Ability to send email notifications to clients upon scheduling an appointment and email reminders for
   appointments, leveraging Django Q for task scheduling and efficiency.

## Key features introduced in previous versions.

- For more information, please refer to
  this [documentation](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/master/docs/history).

## Added Features and Bug Fixes in version 3.x.x

See the [release notes](https://github.com/DevDenysVuikiv/Appointment-scheduler/releases/tag/v3.3.1).
For older version,
see their [release notes](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/master/docs/release_notes).

## Quick Start 🚀

1. Install Django-Appointment in your project using pip:

   ```bash
   pip install django-appointment
   ```

2. Add "appointment" (& "django_q" if you want to enable email reminders) to your `INSTALLED_APPS` setting like so:

   ```python
   INSTALLED_APPS = [
       # other apps
       'appointment',
       'django_q',
   ]
   ```

3. Then, incorporate the appointment URLconf in your project's `urls.py`:

   ```python
   from django.urls import path, include
   
   urlpatterns = [
       # other urls
       path('appointment/', include('appointment.urls')),
   ]
   ```
4. In your Django's `settings.py`, append the following:

   ```python
   AUTH_USER_MODEL = 'models.UserModel'  # Optional if you use Django's user model
   ```

   For instance, if you employ a custom user model called `UserClient` in an app named `client`, you would add it like:

   ```python
   AUTH_USER_MODEL = 'client.UserClient'
   ```

   But if you're using the default Django user model (like most of us), there's no need to add this line since Django
   automatically sets it to:

   ```python
   AUTH_USER_MODEL = 'auth.User'
   ```

   Ensure your `create_user` function includes the following arguments, even if they are not all used (in case you're
   using a custom user model with your own logic for creating users):

   ```python
   def create_user(first_name, email, username, last_name=None, **extra_fields):
       pass
   ```

   This function will create a passwordless user.
   After doing so, a link to set the password will be sent to the user's email upon completing the appointment request.

   Another variable that is worth configuring is the website's name in your `settings.py`:

   ```python
   APPOINTMENT_WEBSITE_NAME = 'Chocolates'
   ```

   It will be used in the footer of the emails sent to clients upon scheduling an appointment:

   ```html
   <p>® 2023 {{ APPOINTMENT_WEBSITE_NAME }}. All Rights Reserved.</p>
   ```

   To be able to send email reminders after adding `django_q` to your `INSTALLED_APPS`, you must add this variable
   `Q_CLUSTER` in your Django's `settings.py`. If done, and users check the box to receive reminders, you and they
   will receive an email reminder 24 hours before the appointment.

   Here's a configuration example that you can use without modification (if you don't want to do much research):

   ```python
   Q_CLUSTER = {
      'name': 'DjangORM',
      'workers': 4,
      'timeout': 90,
      'retry': 120,
      'queue_limit': 50,
      'bulk': 10,
      'orm': 'default',
   }
   USE_DJANGO_Q_FOR_EMAILS = True  # 🆕 Use Django Q for sending ALL email.
   ```

5. Next would be to create the migrations and run them by doing `python manage.py makemigrations appointment` and right
   after, run `python manage.py migrate` to create the appointment models.

6. Start the Django Q cluster with `python manage.py qcluster`.

7. Launch the development server and navigate to http://127.0.0.1:8000/admin/ to create appointments, manage
   configurations, and handle appointment conflicts (the Admin app must be enabled).
8. You must create at least one service before using the application on the admin page. If your service is free, input 0
   as the price. If your service is paid, input the price in the price field. You may also provide a description for
   your service.
9. Visit http://127.0.0.1:8000/appointment/request/<service_id>/ to view the available time slots and schedule an
   appointment.

## Template Configuration 📝

If you're using a base.html template, you must include the following block in your template:

```
{% block customCSS %}
{% endblock %}

{% block title %}
{% endblock %}

{% block description %}
{% endblock %}

{% block body %}
{% endblock %}

{% block customJS %}
{% endblock %}
```

At least the block for css, body and js are required; otherwise the application will not work properly. 
Jquery is also required to be included in the template.

The title and description are optional but recommended for SEO purposes.

See an example of a base.html template [here](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/main/appointment/templates/base_templates/base.html).


## Customization 🔧

1. In your Django project's `settings.py`, you can override the default values for the appointment scheduler.
   More information regarding available configurations can be found in
   the [documentation](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/master/docs/README.md#configuration).
2. Modify these values as needed for your application, and the app will adapt to the new settings.
3. For further customization, you can extend the provided models, views, and templates or create your own.


## Docker Support 🐳

Django-Appointment now supports Docker, making it easier to set up, develop, and test.

### Getting Started with Docker for Development or Local Testing

Using Django-Appointment with Docker is primarily intended for **development purposes** or **local testing**.
This means you'll need to ___clone the project from the GitHub repository___ to get started.

Here's how you can set it up:

1. **Clone the Repository**: Clone the Django-Appointment repository to your local machine:

   ```bash
   git clone https://github.com/adamspd/django-appointment.git
   ```

   or using SSH:
   ```bash
   git clone git@github.com:adamspd/django-appointment.git
   ```

   then go to the project's directory:
   ```bash
   cd django-appointment
   ```

2. **Prepare an .env File**: Create an `.env` file in the root directory of your project with your configuration
   settings.
   You should include your email host user and password for Django's email functionality (if you want it to work):

   ```plaintext
   EMAIL_HOST_USER=your_email@example.com
   EMAIL_HOST_PASSWORD=your_password
   ADMIN_NAME='Example Name'
   ADMIN_EMAIL=django-appt@example.com
   ```

   > **Note:** The `.env` file is used to store sensitive information and should not be committed to version control.

3. **Build and Run the Docker Containers**: Run the following command to build and run the Docker containers:

   ```bash
   docker-compose up -d --build
   ```

   or
   ```bash
   docker compose up -d --build
   ```

4. **Make Migrations and Run**: Create the migrations with the following command:

   ```bash
   docker-compose exec web python manage.py makemigrations appointment
   ```

   or
    ```bash
   docker compose exec web python manage.py makemigrations appointment
   ```

   Then, apply the migrations with the following command:
   ```bash
   docker-compose exec web python manage.py migrate
   ```

   or
    ```bash
   docker compose exec web python manage.py migrate
   ```

5. **Create a Superuser**: After the containers are running, create a superuser to access the Django admin interface:

   ```bash
    docker-compose exec web python manage.py createsuperuser
    ```

   or
   ```bash
    docker compose exec web python manage.py createsuperuser
    ```

6. **Access the Application**: Once the containers are running, you can access the application at `localhost:8000`. The
   Django admin interface is available at `localhost:8000/admin`. And from there, add the necessary configurations.
   [Follow this documentation](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/main/blob/master/docs/explanation.md).
7. **Shut Down the Containers**: When you're finished, you can shut down the containers with the following command:

   ```bash
   docker-compose down
   # docker compose down
   ```

   > **Note:** I used the default database settings for the Docker container.
   > If you want to use a different database, you can modify the Dockerfile and docker-compose.yml files to use your
   > preferred database.
   

## Compatibility Matrix 📊

A compatibility matrix is available to help you determine which versions of Django and Python are compatible with the
package.
The matrix is updated regularly to reflect the latest compatibility test results. For more information, please
refer to
the [compatibility matrix here](https://github.com/DevDenysVuikiv/Appointment-scheduler/blob/main/compatibility_matrix.md).

## Support 💬

For support or inquiries regarding the Appointment Scheduler app, please refer to the documentation in the "docs"
directory or visit the GitHub repository for more information.

## Contributing 🤝

Contributions are welcome! Please refer to
the [contributing guidelines](https://github.com/DevDenysVuikiv/Appointment-scheduler/CONTRIBUTING.md) for more
information.

> **Important**: Please consider starring the repository if you find it useful. It helps me to know that the project is
> appreciated and that I should continue to maintain it. Thank you!

## Code of Conduct 📜

Please refer to the [code of conduct](https://github.com/DevDenysVuikiv/Appointment-scheduler/CODE_OF_CONDUCT.md) for
more information.

## Security policy 🔒

Please refer to the [security policy](https://github.com/DevDenysVuikiv/Appointment-scheduler/SECURITY.md) for more
information.
