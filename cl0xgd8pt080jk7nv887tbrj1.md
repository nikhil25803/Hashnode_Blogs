## How Django helps to fight against Cyber Attacks?

### Overview
>  When a malicious user intentionally tries to access unauthorized activity, steal or leak data, cause harm to computer networks and systems is called **Cyber Attack** and that malicious user is termed as **Hackers**, Hacktivist and sometimes Cybercriminals too!

### Some common types of cyberattacks : 
![1634116954100.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1646890237979/G544w4_9q.jpg)
+ **Malware** - most common type of cyber attack in which the hacktivist tries to access unauthorized operation of the user's system
+ **Phishing** - in phishing the malicious user tries to trick the recipient by emails and messages such they actually believe they received something which is useful to them. Credit card fraud is the most common example of Phishing.
+ **MitM (Man in the middle)** - another very frequently occurring cyber attack in which the hacktivist illegally interferes between the server and the client communication, may steal and leak valuable information.
+ **SQL Injection** - is the malicious attack in which the user manipulates the database. 
+ **Clickjacking** - the process in which the malicious user tricks the victim in such a way that the user thinks that they are clicking on one thing but actually they are clicking on something else.
+ **CSRF Attack** - very frequently occurring activity in which other sites are illegally gets rendered in the browser

### How does Django helps in fighting these cyber attacks?
> Django is popularly known for fighting **SQL Injection**, **Clickjacking**, and **CSRF Attack**

#### SQL Injection 💉 
+ SQL Injection is the malicious activity under which the hacker manipulates the database using SQL(Structured Query Language) and can change, delete, or leak the data.
![608958ea27293628afb3b58b_SQL injection work.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1646976061424/lz14mrH1j.jpg)

+ Even organizations like LinkedIn, Epic Games, Tesla, Fortnite, and Yahoo have been under this attack once

+ For example, you are applying for a job in a company and you have to submit information based on the following model

```py
class Resume(models.Model):
    name = models.CharField(max_length=50)
    age = models.IntegerField()
    gender = models.CharField(max_length=50)
    job_role = models.CharField(max_length=100)
```
+ After submitting the data, it looks like
```("Nikhil", 18, "Male", "SDE")```

+ According to the submitted data, you want to join as a SDE in the company, but what if a hacktivist manipulates the data and deletes the ```job_role``` in the database, you might get into trouble!

+ Using Django ORM(Object-Relational Mapper), in which the Python queries are converted into SQL queries. These query sets are protected from SQL Injection since they are built on the concept of Parameterization.

+ According to "Parameterization", in order to generalize the code any value taken from the user in a query is substituted with a parameter. Data is inserted as a stand-alone value, not the SQL Command.

+ So when the malicious user tries to delete ```SDE"); DELETE FROM app_name;--```, instead of applying this SQL command it will get entered in the database ```("Nikhil", 18, "Male", "SDE"); DELETE FROM app_name;--" )``` as they are passed as a parameter.


#### Clickjacking 🖱️

+ Clickjacking is an interface oriented cyber attack in which the hacktivist tricks the user in such a way that they click on content but un-intentionally they click on something else
![5fb709155ab4dd847b04d05c_What is clickjacking.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647062510318/OdoITXkqM.png)

+ Most of the time it is executed by using **iframe** tag in the HTML and **z-index** properties of CSS. ```<iframe>``` tag holds the potential to embed another website in the current working webpage and can access them too, whereas ```z-index``` used to specify the stack order of element

+ Example<sup> [ Source](https://portswigger.net/web-security/clickjacking)</sup>
```html
<head>
	<style>
		#target_website {
			position:relative;
			width:128px;
			height:128px;
			opacity:0.00001;
			z-index:2;
			}
		#decoy_website {
			position:absolute;
			width:300px;
			height:400px;
			z-index:1;
			}
	</style>
</head>
...
<body>
	<div id="decoy_website">
	...decoy web content here...
	</div>
	<iframe id="target_website" src="https://vulnerable-website.com">
	</iframe>
</body>
```
+ As we can see that the target website is interfaced over the hosted website(decoy_website) using ```<iframe>``` tag, ```z-index``` makes sure that the target website is stacked over the working website and ```opacity:0.00001;``` attribute is used so that that target website is invisible to the user.

+ In Django, using the **X-Frame** option we can tackle this issue, as it is an HTTP response header that indicates if a site can be rendered using **iframe**  or not. If the response returns the header **SAMEORIGIN**, then only the browser will allow the site to get rendered and if the return header is **DENY** then the site will not get rendered.

+ To execute this, we have to include the X-Frame Option in **MIDDLEWARE** available in ```settings.py```
```
MIDDLEWARE = [
    ...
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ...
]
```
 + Then we have to set X-Frame options per views as shown in the code below

```
from django.http import HttpResponse
from django.views.decorators.clickjacking import xframe_options_deny
from django.views.decorators.clickjacking import xframe_options_sameorigin

@xframe_options_deny
def view_one(request):
    return HttpResponse("I won't display in any frame!")

@xframe_options_sameorigin
def view_two(request):
    return HttpResponse("Display in a frame if it's from the same origin as me.")
```
+ For better understanding, go through the [documentation](https://docs.djangoproject.com/en/4.0/ref/clickjacking/) once

#### CSRF(Cross-Site Request Forgery)  
+ A cyber activity in which the malicious user tricks the user by sending a fake request to the server and performing undesired action without the user's concern. As the legit user is authenticated by the server at the time of attack hence it is not possible to identify the malicious one among them.


![cross-site request forgery.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1647634240794/E2QFJxOd2.jpg)

+ User is identified using cookies. It contains a Unique Id that works as an identifier and it is sent along with every HTTP request. Using this, the hacker can also make a request on behalf of the user.

+ In Django, ```CsrfViewMiddleware``` is by default activated in the *Middleware* of **settings.py**. In case it is not enabled, ```csef_protect()``` can also be used.

+ But as this CSRF attack is observed mostly while submitting a form(that targets internal URL), so using ```{% csrf_token %}``` with any form using the **POST** method can prevent this attack.

```
<form method="post"> {% csrf_token %}

</form>
```

+ Another method is using **decorator** method, as this method is not encouraged much as it makes the code base a bit complicated. In ```views.py``` of Django Application, this method is implemented as 

```
from django.shortcuts import render
from django.views.decorators.csrf import csrf_protect

@csrf_protect
def my_view(request):
    c = {}
    # ...
    return render(request, "a_template.html", c)
```

----

Hopefully, I was able to make you understand my views regarding this topic. 

### Thank You!




