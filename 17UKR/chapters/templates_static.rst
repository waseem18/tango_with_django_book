.. _templates-label:

Шаблони та статичне медіа
=========================
В цьому розділі ми будемо розширювати знання Django знайомлячись з рушієм шаблонів, а також, як поводитися з *статичним медіа* на ваших веб-сторінках. 

.. _model-setup-templates-label:

Використання шаблонів
---------------------
До цього часу ви підключали лише кілька речей для створення веб-сторінок на Django. Пара видів, для котрі, в свою чергу, з'єднані з парою URL відображень. Тепер ми заглибимось і з'ясуємо як створювати суміші із шаблонів.

Добре спроектовані веб-сайти має у своїй структурі або макетах багато повторів. Якщо ви бачите загальний заголовок або нижній колонтитул на сторінках веб-сайту  то `повтори макетів сторінок <http://www.techrepublic.com/blog/web-designer/effective-design-principles-for-web-designers-repetition/>`_ допомагають користувачам з навігацією, підтримують організацію веб-сайту та зміцнюють почуття безперервності. Django надає `шаблони  <https://docs.djangoproject.com/en/1.7/ref/templates/>`_ щоб зробити легшим для розробників досягнення цілей розробки,  а також відділити логіку додатку від  задач презентації. В цій главі ви створите базовий шаблон, що буде використовивутися при створенні HTML сторінок. Цей шаблон потім буде відправлено через вид Django. У розділі :ref:`model-using-label` ми використаємо цей крок у поєднанні з моделями для надсилання динамічно сгенерованих даних.

Конфігурування каталогу шаблонів
................................
Щоб користуватися шаблонами потрібно налаштувати каталог де ці шаблони будуть зберігатися. 

У теці проекту Django (наприклад ``<workspace>/tango_with_django_project/``), створіть новий каталог на ім'я  ``templates``. В ньому ж (в новоствореному каталозі) створіть каталог ``rango``. Таким чином, каталог ``<workspace>/tango_with_django_project/templates/rango/`` буде місцем зберігання шаблонів додатку ``rango``. 

Щоб налаштувати проект Django де знаходяться шаблони відкрийте файл проекту ``settings.py``. Додайте кортеж ``TEMPLATE_DIRS`` до ``settings.py`` і вкажіть шлях до новоствореного каталогу ``templates``, так, як показано нижче:

.. code-block:: python
	
	
	TEMPLATE_DIRS = ('<workspace>/tango_with_django_project/',)

Зауваже, що *потрібен абсолютний шлях* до каталогу ``templates``. Якщо ви працюєте в команді, або на різних комп'ютерах це може стати проблемою в майбутнєму. Ви будете мати різні імена, що означає різні шляхи до вашого каталогу ``<workspace>``. *Жорстко закодовані* шляхи, що наведені вище, не одне й те саме на різних комп'ютерах. Звичайно, ви можете виправляти каталог шаблонів для кожної окремої установки, але це був би досить огидний спосіб вирішення проблеми. Отже, що ми можемо зробити?

.. warning::
	Дорога до пекла встелена жорстко закодованими шляхами. 
 	`Жорстко закодовані <http://en.wikipedia.org/wiki/Hard_coding>`_ шляхи вважаються `програмним антипатерном <http://sourcemaking.com/antipatterns>`_ та роблять ваш проект менш `переносним <http://en.wikipedia.org/wiki/Software_portability>`_.

Динамічні шляхи
...............
Вирішення проблеми жорсткого кодування шляхів полягає у використанні вбудованих функцій Python для автоматизації роботи зі шляхом до каталогу ``templates``. У такий спосіб абсолютний шлях може бути отриманий незалежно від розташування проекту Django  файловій системі. Це, в свою чергу, означає що код вашого проекту робиться більш *переносним.* 

В Django 1.7 файл ``settings.py``  тепер має змінну ``BASE_DIR``. В ній зберігається шлях до каталогу в котрому міститься файл проекту ``settings.py``. Знаення отримано за допомогою спеціального атрибуту Python ``__file__`` котрий `встановлюється рівним абсолютному шляху до вашого модуля <http://stackoverflow.com/a/9271479>`_.  Якщо ``__file__`` абсолютний шлях до файла налаштувань, тоді виклик ``os.path.dirname()`` абсолютний шлях до каталогу. Повторний виклик ``os.path.dirname()`` видалить ще один шар, так щоб  ``BASE_DIR`` містила ``<workspace>/tango_with_django_project/``. Переглянте цей процес у дії, якщо цікаво, додавши такі рядки:

.. code-block:: python
	
	print __file__
	print os.path.dirname(__file__)
	print os.path.dirname(os.path.dirname(__file__))
	


Давайте скористуємось цим зараз. Створіть нову змінну в ``settings.py`` на ім'я ``TEMPLATE_PATH`` та збережіть в ній шлях до каталогу ``templates``. З використанням функції ``os.path.join()`` ваш код має виглядати як в наведеному нижче прикладі:

.. code-block:: python
	
	TEMPLATE_PATH = os.path.join(BASE_DIR, 'templates')

Ми скористалися ``os.path.join()`` для поєднання змінної ``BASE_DIR`` та  ``'templates'`` і отримання в результаті, наприклад ``<workspace>/tango_with_django_project/templates/``. Тепер ми можемо замінити жорстко закодований шлях в ``TEMPLATE_DIRS`` на ``TEMPLATE_PATH``:

.. code-block:: python
	
	TEMPLATE_DIRS = (
	    # Put strings here, like "/home/html/django_templates" or "C:/www/django/templates".
	    # Always use forward slashes, even on Windows.
	    # Don't forget to use absolute paths, not relative paths.
	    TEMPLATE_PATH,
	)

Ми можемо тримати змінну ``TEMPLATE_PATH`` з початку файлу ``settings.py`` для зручного доступу, якщо її треба буде змінити. Для цього ми і зробили додаткову змінну.

.. warning:: При поєднанні шляхів користуйтеся функцією ``os.path.join()``. Вона забезпечить використання правильних слешів, в залежності від операційної системи. На POSIX-сумісних операційних системах використовується прямий слеш(/), а на Windows зворотній(\). У випадку ручного додавання слешу ви можете отримати помилку при зміні операційної системи.




.. _adding-a-template-label:

Створення шаблону
.................
Створіть файл ``index.html`` і розмістіть його в каталозі ``templates/rango/``. Додайте в нього такий код HTML:

.. code-block:: html
	
	<!DOCTYPE html>
	<html>
	
	    <head>
	        <title>Rango</title>
	    </head>
	    
	    <body>
	        <h1>Rango says...</h1>
	        hello world! <strong>{{ boldmessage }}</strong><br />
	        <a href="/rango/about/">About</a><br />
	    </body>
	
	</html>

З коду зрозуміло, що це проста HTML сторінка буде виводити привітання *hello world*. Зверніть увагу на не-HTML код у вигляді ``{{ boldmessage }}``. Це *змінна шаблону Django* і ми можемо встановити її значення для відображення. Що ми зараз і зробимо.

Щоб скористатися цим шаблоном потрібно переконфігурувати вид ``index()``, котрий ми створили раніше. Замінемо передачу простого повідомлення на передачу нашого шаблону.

В ``rango/views.py`` додайте імпорт:

.. code-block:: python
	
	from django.shortcuts import render

Тепер змініть функцію ``index()`` як показано. Прочитайте коментарі, щод дізнатися що робить кожен рядок коду.

.. code-block:: python
	
	def index(request):
	     
	    # Створимо словник для передачі контексту в рушій шаблону.
	    # Зауважте- ключ boldmessage такий самий як {{ boldmessage }} в шаблоні!
	    context_dict = {'boldmessage': "I am bold font from the context"}
	    
	    # Повертаємо опрацьовану відповідь для відправки клієнту.
	    # Ми скористалися допоміжною функцією для полегшення життя.
	    # Увага, другий параметр- це ім'я шаблону котрий ми хочемо використати.
		
	    return render(request, 'rango/index.html', context_dict)


Сочатку ми створили словник пар ключ-значення для використання з шаблоном, потім викликали допоміжну функцію ``render()``. Ця фунція приймає запит користувача ``request``, ім'я файлу шаблону та словник контексту. Функція ``render()`` приймає ці дані та об'єднує з шаблоном для отримання HTML сторінки. Потім ця сторінка повертається до веб-переглядача користувача.

Коли файл шаблону завантажується у систему шаблонів Django створюється *контекст шаблону*. Контекст шаблону це , по суті, словник Python що відображає змінні шаблону на змінні Python. У створений раніше шаблон ми додали змінну ``boldmessage``. А в ``index(request)`` рядок ``I am bold font from the context`` відображається на змінну ``boldmessage``. Таким чином, рядок ``I am bold font from the context`` замінює у шаблоні будь-який зразок  ``{{ boldmessage }}``.

Тепер, коли ви оновили вид для застосування шаблону, запустіть сервер розробки Django і відвідайте http://127.0.0.1:8000/rango/. Ви побачите ваш шаблон у всій красі, так, як показано на малюнку :num:`fig-rango-hello-world-template`. 

Якщо ні, прочитайте повідомлення про помилку щоб з'ясувати в чому проблема, а потім двічі перевірте всі зміни що були зроблені вами. Впевніться що було зроблено все потрібне. Найбільш розповсюджена помилка з неправильним шляхом до шаблонів в ``settings.py``. Іноді варто додати команду ``print`` до ``settings.py`` і перевірити значення ``BASE_DIR`` та ``TEMPLATE_PATH``.

Цей приклад демонструє як використовувати шаблони у видах. Однак ми розлянули лише невелику частину функціональності Django стосовно шаблонів. Ми будемо користуватися шаблонами у більш складний спосіб при подальшому вивченні цього посібника. Тим часом ви можете дізнатися більша про `шаблони з офіційної документації <https://docs.djangoproject.com/en/1.7/ref/templates/>`_.

.. _fig-rango-hello-world-template:

.. figure:: ../images/rango-hello-world-template.png
	:figclass: align-center

	Копія екрана з Google Chrome що відмальовує шаблон з посібника.

Роботи зі статичним медіа
-------------------------
Якщо відверто, веб-сайт *Rango* доволі плаский, не використано ні стилів ні зображень.  `Cascading Style Sheets (CSS) <http://en.wikipedia.org/wiki/Cascading_Style_Sheets>`_, `JavaScript <https://en.wikipedia.org/wiki/JavaScript>`_ і малюнки це *статичне медіа*, файли котрі ми можемо включити до нашого веб-сайту, додати стиль та запровадити динамічну поведінку. З такими файлами працюють трохи у інший спосіб ніж зі звичайними веб-сторінками. Вони не генеруються льоту як наші HTML сторінки. У цьому розділі показано, як налаштувати ваш проект Django для обслуговування статичних медіа для клієнта. Ми також модифікували шаблон, щоб включити деякі приклади статичних медіа.

Налаштування каталогу статичних медіа
.....................................
Щоб використовувати статичне медіа спочатку потрібно створити каталог зберігання медіафайлів. В каталозі проекту (наприклад ``<workspace>/tango_with_django_project/``) створіть нову теку і назвіть її ``static`` та в ній створіть ще каталог на ім'я ``images``.

Тепер в теці ``static/images`` розмістіть зображення. Як показано на малюнку :num:`fig-rango-picture`, ми обрали зображення хамелеона `Rango <http://www.imdb.com/title/tt1192628/>`_ - підхожий талісман.

.. _fig-rango-picture:

.. figure:: ../images/rango-picture.png
	:figclass: align-center

	Хамелеон Rango з теки статичного медіа.

Ми маємо теку ``static``, але про це потрібно розповісти Django так само як ми зробили з текою ``templates`` раніш. До файлу ``settings.py`` потрібно додати дві змінні:  ``STATIC_URL`` та кортеж ``STATICFILES_DIRS``, так як показано нижче:

.. code-block:: python
	
	STATIC_PATH = os.path.join(BASE_DIR,'static')

	STATIC_URL = '/static/' # You may find this is already defined as such.
	
	STATICFILES_DIRS = (
	    STATIC_PATH,
	)

Ви ввели трохи коду, але що він робить? Перша змінна ``STATIC_URL`` втановлює базовий URL відносно котрого ваш додаток Django буде шукати файли статичного медіа коли запущено сервер. Наприклад, коли запущено сервер розробки Django зі ``STATIC_URL`` втановленим рівним ``/static/``, як в наведеному вище коді, статичне медіа буде доступне за адресою ``http://127.0.0.1:8000/static/``.  `Офіційна документація по роботі зі статичним медіа <https://docs.djangoproject.com/en/1.7/ref/settings/#std:setting-STATIC_URL>`_ попереджає, що життєво важливо щоб були саме такі слеші.

В той час як ``STATIC_URL`` визначає URL для доступу до медіа через веб-сервер, ``STATICFILES_DIRS`` дає можливість встановити розміщення новоствореної теки ``static`` на локальному диску. Як і кортеж ``TEMPLATE_DIRS``, ``STATICFILES_DIRS`` потребує абсолютного шляху до теки ``static``. Тут ми знову скористаємося ``BASE_DIR`` щоб створити ``STATIC_PATH``.

Оновивши обидва налаштування, знову запустіть сервер розробки Django. Якщо хочете подивитися зображення Rango, відвідайте URL ``http://127.0.0.1:8000/static/images/rango.jpg``. Якщо не вийшло, перевірте налаштуванняв файлі ``settings.py`` та перезавантажте сервер розробки. Якщо вийшло, спробуйте додати ще файли до теки ``static`` і спробуйте  зробити запит до них через веб-переглядач.

.. caution:: При використанні сервера розробки Django наведений спосіб роботи з медіа файлами чудово працює, але вкрай непридатний для робочого - або *живого* - оточення. `Офіційна документація Django <https://docs.djangoproject.com/en/1.7/howto/static-files/deployment/>`_ надає подальшу інформацію про розгортання статичних файлів в робочому оточенні.

Статичні медіа-файли і шаблони
------------------------------
Тепер, коли ви маєте налаштований для роботи зі статичним медіа проект Django є можливість використовувати їх у шаблонах.

Щоб скористатися статичним медіа, наприклад, відкрийте файл ``index.html``, що в теці ``<workspace>/templates/rango/``. Змініть код HTML як показано далі. Два рядки, що ми додали, виділено коментарями HTML.

.. code-block:: html

	<!DOCTYPE html>
	
	{% load staticfiles %} <!-- New line -->
	
	<html>
	
	    <head>
	        <title>Rango</title>
	    </head>
	    
	    <body>
	        <h1>Rango says...</h1>
	        hello world! <strong>{{ boldmessage }}</strong><br />
	        <a href="/rango/about/">About</a><br />
	        <img src="{% static "images/rango.jpg" %}" alt="Picture of Rango" /> <!-- New line -->
	    </body>
	
	</html>

Поперше, потрібно, за допомогою тегу ``{% load static %}``, повідомити систему шаблонів Django що ми збираємося використовувати статичне медіа. Це дає можливість викликати тег шаблонів ``static``, як зроблено в ``{% static "rango.jpg" %}``. Як ви можете побачити, теги шаблонів Django позначають фігурними дужками ``{ }``. В цьому прикладі, тег ``static`` поєднує ``STATIC_URL`` з ``"rango.jpg"``, так, що в результаті буде отримано наведний нижче код HTML.

.. code-block:: html

	<img src="/static/images/rango.jpg" alt="Picture of Rango" /> <!-- New line -->

Якщо, чомусь, зображення не може бути завантажено, завжди добре вказувати альтернативний текст. Саме для цього й призначено атрибут ``alt`` - вказаний в ньому текст використовується якщо є проблеми завантаження зображення.

З такими незначними змінами, знову запустіть сервер розробки та відвідайте  ``http://127.0.0.1:8000/rango``. Сподіваюсь, що ви побачите щось як на наведеному малюнку :num:`fig-rango-site-with-pic`.

.. _fig-rango-site-with-pic:

.. figure:: ../images/rango-site-with-pic.png
	:figclass: align-center

	Наш перший шаблон Rango з зображенням хамелеона Rango.

Використовуйте функцію ``{% static %}`` для посилань на статичне медіа в шаблонах. Далі наведено приклад для JavaScript, CSS та зображень в шаблонах - всі з коректною HTML розміткою.

.. code-block:: html
	
	<!DOCTYPE html>
	
	{% load static %}
	
	<html>
	
	    <head>
	        <title>Rango</title>
	        <link rel="stylesheet" href="{% static "css/base.css" %}" /> <!-- CSS -->
	        <script src="{% static "js/jquery.js" %}"></script> <!-- JavaScript -->
	    </head>
	    
	    <body>
	        <h1>Including Static Media</h1>
	        <img src="{% static "images/rango.jpg" %}" alt="Picture of Rango" /> <!-- Images -->
	    </body>
	
	</html>

Зрозуміло, що статичні файли повинні бути в каталозі ``static``. Якщо файл відсутній, або є помилка у посиланні, консоль сервера розробки Django видає відповідне повідомлення про помилку. Спробуйте зробити посилання на відсутній файл і подивіться що трапиться.

Для отримання подальшої інформації з використання статичного медіа зверніться до  `документації Django по роботі зі статичними медіа-файлами в шаблонах <https://docs.djangoproject.com/en/1.7/howto/static-files/#staticfiles-in-templates>`_.

.. caution:: Потурбуйтеся про те щоб гарантувати присутність в шаблоні `декларації типу документа <http://en.wikipedia.org/wiki/Document_Type_Declaration>`_ (наприклад ``<!DOCTYPE html>``) у *першому рядку* веб-сторінки. Саме цьому ми розмістили команду шаблону Django ``{% load static %}`` на один рядок нижче декларації типу документу, а не з самого верху. Це вимога різновидів HTML/XHTML щоб декларація типу документа була у першому рядку. Команди Django що розташовані раніше будуть видалені з остаточно опрацьованого виводу, але є імовірність залишку пустих рядків, в результаті чого  вихідний документ не буде `відповідати <http://www.w3schools.com/web/web_validate.ASP>`_ на `сервісі перевірки W3C <http://validator.w3.org/>`_.

#TODO(leifos): Зверніть увагу, що це не найкраща практика, коли ви йдете до розгортання, і що вони повинні побачити:: https://docs.djangoproject.com/en/1.7/howto/static-files/deployment/ і що наступне рішення працює, коли ``DEBUG=True``

#TODO(leifos): змінна DEBUG в settings.py, дозволяє вам контролювати повідомлення коли трапляються помилки і використовується для зневадження. Небезпечно залишати DEBUG рівним True при розгортанні програми. Якщо DEBUG встановлюється рівним False, потрібно визначити змінни ALLOWED_HOSTS в settings.py. У випадку локального комп'ютера вона повинна бути рівною ``127.0.0.1``. Також потребує оновлення файл urls.py:


.. code-block:: python


	from django.conf import settings # New Import
	from django.conf.urls.static import static # New Import


	if not settings.DEBUG:
		urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)


#TODO(leifos): Можливо ми опишемо все це в розділі про розгортання... ймовірно це більш доцільно


The Static Media Server
-----------------------
Now that you can dispatch static files, let's look at uploading media. Many websites provide their users with the ability to do this - for example, to upload a profile image. This section shows you how to add a simple development media server to your Django project. The development media server can be used in conjunction with file uploading forms which we will touch upon in Chapter :ref:`login-label`.

So, how do we go about setting up a development media server? The first step is to create another new directory called ``media`` within our Django project's root (e.g. ``<workspace>/tango_with_django_project/``). The new ``media`` directory should now be sitting alongside your ``templates`` and ``static`` directories. After you create the directory, you must then modify your Django project's ``urls.py`` file, located in the project configuration directory (e.g. ``<workspace>/tango_with_django_project/tango_with_django_project/``). Add the following code to the ``urls.py`` file.

.. code-block:: python
	
	# At the top of your urls.py file, add the following line:
	from django.conf import settings
	
	# UNDERNEATH your urlpatterns definition, add the following two lines:
	if settings.DEBUG:
	    urlpatterns += patterns(
	        'django.views.static',
	        (r'^media/(?P<path>.*)',
	        'serve',
	        {'document_root': settings.MEDIA_ROOT}), )

The ``settings`` module from ``django.conf`` allows us access to the variables defined within our project's ``settings.py`` file. The conditional statement then checks if the Django project is being run in `DEBUG <https://docs.djangoproject.com/en/1.7/ref/settings/#debug>`_ mode. If the project's ``DEBUG`` setting is set to ``True``, then an additional URL matching pattern is appended to the ``urlpatterns`` tuple. The pattern states that for any file requested with a URL starting with ``media/``, the request will be passed to the ``django.views.static`` view. This view handles the dispatching of uploaded media files for you.

With your ``urls.py`` file updated, we now need to modify our project's ``settings.py`` file. We now need to set the values of two variables. In your file, add ``MEDIA_URL`` and ``MEDIA_ROOT``, setting them to the values as shown below.




.. code-block:: python
	
	MEDIA_URL = '/media/'
	MEDIA_ROOT = os.path.join(BASE_DIR, 'media') # Absolute path to the media directory

The first variable ``MEDIA_URL`` defines the base URL from which all media files will be accessible on your development server. Setting the ``MEDIA_URL`` for example to ``/media/`` will mean that user uploaded files will be available from the URL ``http://127.0.0.1:8000/media/``. ``MEDIA_ROOT`` is used to tell Django where uploaded files should be stored on your local disk. In the example above, we set this variable to the result of joining our ``PROJECT_PATH`` variable defined in Section :ref:`model-setup-templates-label` with ``/media/``. This gives an absolute path of ``<workspace>/tango_with_django_project/media/``.

.. caution:: As previously mentioned, the development media server supplied with Django is very useful for debugging purposes. However, it should **not** be used in a production environment. The official `Django documentation on static files <https://docs.djangoproject.com/en/1.7/ref/contrib/staticfiles/#static-file-development-view>`_ warns that such an approach is *"grossly inefficient and insecure"*. If you do come to deploying your Django project, read the documentation to see an alternative solution for file uploading that can handle a high volume of requests in a much more secure manner.

You can test this setup works by placing an image file in your newly created ``media`` directory. Drop the file in, start the Django development server, and request the image in your browser. For example, if you added the file ``rango.jpg`` to ``media``, the URL you should enter would look like ``http://127.0.0.1:8000/media/rango.jpg``. The image should show in your browser. If it doesn't, you'll need to go back and check your setup.

#TODO(leifos): check that this still works (certainly you can access the images.. need to check the uploading)

Basic Workflow
--------------
With the chapter complete, you should now know how to setup and create templates, use templates within your views, setup and use Django to send static media files, include images within your templates *and* setup Django's static media server to allow for file uploads. We've actually covered quite a lot!

Creating a template and integrating it within a Django view is a key concept for you to understand. It takes several steps, but becomes second nature to you after a few attempts.

#. First, create the template you wish to use and save it within the ``templates`` directory you specified in your project's ``settings.py`` file. You may wish to use Django template variables (e.g. ``{{ variable_name }}``) within your template. You'll be able to replace these with whatever you like within the corresponding view.
#. Find or create a new view within an application's ``views.py`` file.
#. Add your view-specific logic (if you have any) to the view. For example, this may involve extracting data from a database.
#. Within the view, construct a dictionary object which you can pass to the template engine as part of the template's *context*.
#. Make use of the  ``render()`` helper function to generate the rendered response. Ensure you reference the request, then the template file, followed by the context dictionary!
#. If you haven't already done so, map the view to a URL by modifying your project's ``urls.py`` file - and the application-specific ``urls.py`` file if you have one.

The steps involved for getting a static media file onto one of your pages is another important process you should be familiar with. Check out the steps below on how to do this.

#. Take the static media file you wish to use and place it within your project's ``static`` directory. This is the directory you specify in your project's ``STATICFILES_DIRS`` tuple within ``settings.py``.
#. Add a reference to the static media file to a template. For example, an image would be inserted into an HTML page through the use of the ``<img />`` tag. 
#. Remember to use the ``{% load staticfiles %}`` and ``{% static "filename" %}`` commands within the template to access the static files.

The next chapter will look at databases. We'll see how to make use of Django's excellent database layer to make your life easier and SQL free!

Exercises
---------
Give the following exercises a go to reinforce what you've learnt from this chapter.

* Convert the about page to use a template too from a template called ``about.html``.
* Within the ``about.html`` template, add a picture stored within your project's static media.
