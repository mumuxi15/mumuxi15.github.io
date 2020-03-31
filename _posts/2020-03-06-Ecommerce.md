---
layout: post
title:  "E-commerce web"
date:   2020-03-06 19:07:19
preview: https://i.pinimg.com/originals/73/23/1c/73231c1309d3b2374083bf312fde1d98.jpg
---
Intro: E-commerce Web



I am writing this blog during the coronavirus pandemic that is affecting all our lives. Recent news puts New York at more than 10,000 confirmed cases and it is becoming increasingly harder to get fresh food. Since everyone is supposed to be effectively quarantined except those in essential businesses, there are a lot of people relying on food delivery businesses. A friend of mine who owns a small meal preparation business has seen increasing demandfor his meals and I am helping him build an e-commerice website for his business. Previously he was using wix.com, but as the business expands and more functionality is required, the need to migrate to a dynamic website and to maintain his own databases became increasingly evident. Although there are thousands of blogs or tutorial about building your 1st Python web app, most of them ends on http://127.0.0.1:8080 . This tutorial will guide you till your website appears on google search. I will only briefly mention the major steps, potential challenge and tips, but I put down the links I found useful while learning. 



I used Flask for this project, another good choice would be Django. There are many arguments on choosing one over the other, I personally do not have a strong preference as this is not a large website application. Your should choose the framework based on your conditions. Flask is chosen here because of its it is super light and easy to start. The downside is that sometimes some supporting packages may be out of maintenance or out-of-date such as Flask-OAuth.



#### 1. Set up virtual environment

Always start a project with virtualenv. Useful packages include Flask-login, Flask-admin, Flask-Security, Flask-dance (for third party login such as google), Flask_sqlalchemy and etc.

```bash
virtualenv -p python3 <desired-path>
```



#### 2. Draw a database schema

Ask yourself a few questions before you start building the website:

1. What information I want to show to the client ? 
2. What information I want to collect from the website ?

It might be useful to draw a quick databse schema and figure below is an example of the standard schema and the grey lines shows how the tables are linked. 



<img src="https://live.staticflickr.com/65535/49641865616_3f95e3d78b_c.jpg" width="80%">



#### 3. Build a draft version from exisiting working app

Let's begin by implementing on the existing examples provided by the Flask development team. The traditional standard layout of a web app contains a run.py where app is defined, a template folder including all the html files and a static folder storing personalized styles and javascript functions. Instead of declaring everything as global variables, the modern approach is to build a function that accepts a configuration object as an argument and returns a Flask application instance. Such way allows you to modify the function in the testing environment to create its own application for testing.  

Project Tree Example using Blueprints

```
e_commerce_web
├── /application
│   ├── __init__.py
│   ├── /auth
│   │   ├── auth_routes.py
│   │   └── /templates
│   ├── /main
│   │   ├── main_routes.py
│   │   └── /templates
│   ├── /static
│   └── /templates
│       ├── layout.html
│       ├── home.html
│       └── login.html
├── requirements.txt
└── wsgi.py
└── venv
```

The function create_app() is used to build a Flask app

*\__int__.py* 

```python
# ... #
def create_app():
	app = Flask(__name__)

	app.config['SECRET_KEY'] = ''
	app.config['SQLALCHEMY_DATABASE_URI'] = db_path
	app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
	app.config['OAUTH_CONFIG'] = {''}
	db.init_app(app)

	login_manager = LoginManager()
	login_manager.login_view = 'auth.login'
	login_manager.init_app(app)

	from .models import User

	admin = Admin(app,index_view=CustomAdminIndexView())
	#...#
  
	@login_manager.user_loader
	def load_user(user_id):
	# use user_id in the query for the user
		return User.query.get(int(user_id))


	# blueprint for auth routes in our app
	from .auth import auth as auth_blueprint
	app.register_blueprint(auth_blueprint)

	# blueprint for non-auth parts of app
	from .main import main as main_blueprint
	app.register_blueprint(main_blueprint)
  
	# register google / facebook login
	init_oauth_providers(app)

	return app
```

Reference:

[Flask-login document](https://flask-login.readthedocs.io/en/latest/)

[Flask-login github demo](https://github.com/pallets/flask/tree/master/examples/tutorial/flaskr/)

[Organzing Flask Apps with Blueprints](https://hackersandslackers.com/flask-blueprints/)



#### 4. Add e-commerce features

Step 3. Add e-commerical features such as product display and shopping cart functions.

This section requires basic javascript programming skills and some knowledge of client-side and server-side. The major challenge of building a dynamic website over static is to pass data between database and website, akin to building a bridge across the sea to connect two cities. I used HTML5 webstorage to store shopping cart status in conjunction with ajax to transfer data to the backend and then to be stored in the database. If you not familiar with jsp, ajax, I recommend you going through some youtube tutorials.

[JavaScript Shopping Cart Tutorial](https://www.youtube.com/watch?v=1Q74A6ZQxdY&list=PLoN_ejT35AEhzNoPStBzAkpqAu3YQwPj7)



#### 5. Testing

Here are some links to good articles on web testing.

[unittesting](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-vii-unit-testing-legacy)

[Selenium](https://scotch.io/tutorials/test-a-flask-app-with-selenium-webdriver-part-1)

#### 6. Deploy

I used pythoneverywhere.com as it is one of the cheapest and easiest. Hosting a single website only cost $5 per month and they also provide free accounts. I do not recommend Amazon for hosting personal website, as the price could go beyond your expectation with no cap. 

#### 7. Google Search

If you have bought a domain name on goDaddy, you can link it to your *.pythonanywhere.com by following  [this tutorial](https://wpsmackdown.com/domain-redirect-godaddy/)

If you want your website to appear in google search, 

1. Verify the domain on google search console. You can open the console by typing in google 

   ```
   site:xxx.com
   ```

2. Complete the DNS verification as google requested

3. go to https://www.freewebsubmission.com/ to submit your website











The website is currently in the testing phase. Only existing members will be able to order meals . Any orders placed from outside will not be processed. 

https://hellokike.pythonanywhere.com/



