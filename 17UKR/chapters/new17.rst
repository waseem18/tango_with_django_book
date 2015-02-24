.. _new17-label:



Що нового в TwD 1.7
==============

.. warning:: Будь ласка, зверніть увагу, що ця версія книги все ще чернетка. Хоча, загалом, все зроблено однак деякі посилання та скріншоти потребують оновлення. Будь ласка, повідомляйте про будь-які помилки, проблеми і т.д., або відправляйте запити на зміну через GitHub: https://github.com/leifos/tango_with_django_book/tree/master/17 


У цій версії онлайн підручника(книги), ми оновили і додали кілька речей:

* Перероблено код для роботи з Django 1.7
	
	* Взаємодію з базою даних оновлено з ``syncdb`` на ``migratesql`` та ``migrate``
	* Відпрацювання відповідей оновлено з ``render_to_response`` до ``render``. Так що, тепер нема потреби робити запит контексту в кожному виді(view).
	* The ``url`` template tag is now being used in templates, which provides a relative reference to urls rather than an absolute reference..
	* Loading static files in the templates is now done with {% load staticfiles %}
	* Using ``slugify`` to create well formed URL strings

* Додано новий розділ про автентифікацію

	* Where the login and registration is done with Django-Registration-Redux (see Chapter :ref:`login-redux-label`)

* The Bootstrap chapter has been updated to use Bootstrap 3.2.0 (see Chapter :ref:`bootstrap-chapter`)

	* Also, includes some notes on how to use Django-Bootstrap-Toolkit
	
* A new chapter has been added on using template tags (see Chapter :ref:`template-tag-chapter` )

* Add a chapter on using JQuery with Django (see Chapter :ref:`jquery`)

* The chapter on testing has been expanded - but is still a work in progress (see Chapter :ref:`test-chapter`)

	* Includes how to use the package ``coverage`` to measure test coverage (see http://nedbatchelder.com/code/coverage/ )
	
	

	
	
	
