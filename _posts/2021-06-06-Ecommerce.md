---
layout: post
title:  "E-commerce web"
author: penny
categories: [  ]
image: https://i.pinimg.com/originals/73/23/1c/73231c1309d3b2374083bf312fde1d98.jpg
---
Introduction to building website

The ongoing COVID-19 pandemic has dramatically changed the way people shop and do business. With more and more consumers preferring to shop online rather than in-person, small business owners have an opportunity to expand their customer base and increase their revenue by setting up a simple e-commerce website with a database. A dynamic website can help small businesses create an engaging and interactive online presence that attracts new customers and allows existing customers to easily place orders and interact with the business. Setting up a website involves several key steps, including choosing a web hosting provider, selecting a database management system, designing the website's front-end, creating the back-end infrastructure, and integrating the database with the website. In this introduction, we will guide you through the basic steps involved in setting up a dynamic website for a small business with a database, highlighting important considerations and best practices along the way. With the right tools and knowledge, small business owners can create a successful online presence that helps them navigate the challenges of the pandemic and thrive in a digital world.



Many prefer to use Flask due to its ease of use and lightweight nature. Flask makes it easy to start building a web application without having to deal with the complexities of a full-stack web framework. As a lightweight micro-framework, it provides the flexibility to build a custom web application tailored to the specific needs of the project. 



#### 1. Plan and Design 

To start a project, it is always important to plan out the features and functionality you want to include, including the color theme, payment gateway integration and etc. Popular payment gateways include PayPal, Stripe, Braintree and Square, all those have API access and allow backtesting. 



#### 2. Set up virtual environment

Starting your project with a virtual environment tool virtualenv, helps to keep project dependencies organized and prevents conflicts between different projects. Once you have created a virtual environment, you can activate it by running the command. Useful packages include Flask-login, Flask-admin, Flask-Security, Flask-dance (for third party login such as google), Flask_sqlalchemy and etc.

```bash
virtualenv -p python3 <desired-path>
source <venv>/bin/activate
```



#### 3. Build Backend - Database

Ask yourself a few questions before you start building the backend:

1. What information I want to show to the client ? 
2. What information I want to collect from the website ?

Drawing a quick database schema can also be very helpful in visualizing the data model and identifying the relationships between different entities. Picture below is an example of the standard schema and the grey lines shows how the tables are linked. The "order_detail" table share a common key "order_id" with the "orders" table. This would allow you to link specific items in an order to their corresponding product records, so you can accurately track inventory levels and update product availability as items are sold.



<img src="https://live.staticflickr.com/65535/49641865616_3f95e3d78b_c.jpg" width="80%">



#### 4. Build Frontend 

You can quickly get the website to running by using Jinja to dynamically generate HTML pages based on data from your database and user input. This allows you to create customized web pages that can display product listings, shopping carts, checkout forms, and other essential features of an e-commerce website. A basic set up looks like the example below. 

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



#### 5. Add e-commerce features

As the website grows, you can add additional features and functionalities to enhance the user experience. For instance, payment gateways, search functionality, product reviews, wish lists, and social media integration to increase user engagement and boost sales. With Flask's modular design and scalability, you can add these features to the website as needed, making it a versatile and flexible tool for building an e-commerce website.



In order to make a dynamic website, you need to have a basic understanding of JavaScript programming and be familiar with both client-side and server-side development. The main challenge of building a dynamic website is connecting the database to the website and passing data between them. It's like building a bridge between two cities over the sea. One way to do this is by using HTML5 web storage to store shopping cart information and using AJAX to transfer data from the front-end to the back-end, where it can be stored in the database. If you're not familiar with these technologies, I recommend watching some YouTube tutorials to learn more.

[JavaScript Shopping Cart Tutorial](https://www.youtube.com/watch?v=1Q74A6ZQxdY&list=PLoN_ejT35AEhzNoPStBzAkpqAu3YQwPj7)



#### 6. Testing

This involves running different types of tests such as unit tests, integration tests, and end-to-end tests to verify the functionality of different components of the website. You can use testing frameworks such as Pytest and unittest to write and run tests for your website. Debugging any issues or errors that arise is also crucial to make sure that your website is functioning smoothly.

Links to good articles on web testing.   [unittesting](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-vii-unit-testing-legacy)



#### 7. Deploy

Once you have tested your website and are confident that it works as expected, the next step is to deploy it to a production environment where it can be accessed by users. There are various hosting services available such as Heroku, AWS, and Google Cloud Platform that can be used to deploy your website. You will need to configure the hosting environment, set up the database and server, and deploy your code to the server. It is also important to consider issues such as scalability, security, and availability when deploying your website. I used pythoneverywhere.com to host my website. It is affordable and easy to use. It provides a web-based development environment and a platform for hosting web applications written in Python. With a paid account, you can host up to three web applications for \$5 per month.



When building a website, it's important to ensure that the site is secure and the sensitive customer information such as names, addresses, and payment details are protected from potential hackers or other security threats. One way to accomplish this is to use encryption to protect the data as it is transmitted between the client and server. First, you'll need to obtain an SSL (Secure Sockets Layer) certificate, which verifies the identity of your website and encrypts all data transmitted between the server and the client. You can obtain a SSL certificate from a trusted certificate authority such as Let's Encrypt (FREE !!!) Once you have the certificate, configure the web server to use HTTPS instead of HTTP. HTTPS is a secure version of the HTTP protocol that encrypts all data transmitted between the client and server. 

In addition to using encryption, you should also take other security measures such as regularly updating your software and plugins, using strong passwords, and implementing measures such as two-factor authentication to protect your website from potential threats. It's also a good idea to regularly back up your website to protect against data loss in the event of a security breach or other issue.



#### 8. Google Search

If you want your website to appear in google search, 

1. Verify the domain on google search console. You can open the console by typing in google 

   ```
   site:xxx.com
   ```

2. Complete the DNS verification as google requested

3. Buy a domain name. 

4. Edit CNAME record in the DNS manage section. Here I used goDaddy as an example.

```
host: www
points to: yourwebsitename.com
```

5. Then go back to google console, search for your url in the URL inspection section and submit the index request. Google will perform several test on the website and if passed, you will receive a confirmation email from google. 



Overall, Flask is a flexible and lightweight web framework that simplifies database integration and offers a range of built-in features that can be customized and extended to create dynamic websites for small businesses during the pandemic.
