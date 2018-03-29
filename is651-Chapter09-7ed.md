 

## Chapter 9 - Microservices
### Introduction

We will use this [introduction to microservices](https://www.nginx.com/blog/introduction-to-microservices) from [nginx](https://www.nginx.com/), section 1 only (cached pdf [here](../Microservices_Designing_Deploying.pdf)). We will revisit microservices and read additional sections in chapter 12.  In order for us to really understand the distinction between monoliths and microservices, we will examine a popular design pattern used in web frameworks.

### Web Frameworks

Almost all new development for distributed systems today is based around the web infrastructure of using HTTP to access web resources and web services. Creating presentation tier clients with web-based programming frameworks for this infrastructure helps the architects and developers to effectively and quickly build, test, and deploy these applications.
We will survey web-based frameworks in this chapter for major implementation technologies and do some programming exercises to make the discussion more concrete.

The four tier web infrastructure is shown in figure 9.1 with the logical presentation tier highlighted as where these web-based frameworks are focused at delivering web applications. The frameworks involve all layers, however, in that the frameworks use javascript in the web browser, web servers to deliver content, application server technology such as PHP for server-side processing, and databases for maintaining state.

![The web infrastructure](is651-images/f9-1_opt.png)

Figure 9.1. The web infrastructure. 

Software frameworks are generic, partially written applications for a specific application architecture that make creating new applications quicker and easier. The frameworks offer a large collection of libraries that take care of all the routine programming tasks and leave the programmer to implement the custom functions that are required for the new application. This makes programmers much more productive, but removes flexibility. So as long as the needed application follows the patterns given by the framework, it works well. If a new pattern is needed, the programmer might well be better advised to program from scratch. Web-based applications are well suited to a collection of design patterns that are implemented in web frameworks. Most web frameworks have integrated development environments (IDEs) that are used to easily access the web framework libraries while programming.

Design patterns are an influential concept in software engineering that represent reusable solutions to a commonly occurring programming and software design problems. It is not code but a description of the pattern. Programmers use code to implement the design pattern. Web frameworks are code that implement proven, relevant design patterns.

Design patterns gained popularity in computer science after the book Design Patterns: Elements of Reusable Object-Oriented Software was published in 1994 by the so-called "Gang of Four" (Gamma et al.). A very common design pattern used in web frameworks is the model, view,
controller (MVC) pattern which separates interface, logic, and data models so that code developed with this pattern is more easily maintained, tested, and developed. Figure 9.2 shows the basic MVC pattern.

The general operation of MVC can be described as follows. A web browser makes a request to a web server with a nice looking URL as we defined them earlier for REST web services. The routing layer associated with the web framework translates the nice URL structure to call the specific web applications offered by the controller. The controller gets data from the model for processing. The model is responsible for getting data from the database and creating objects because web frameworks are typically implemented in object-oriented languages and cannot directly operate on relational databases. The controller program participates in a SOA and so can also request SOAP or REST web services. The controller then instantiates a template view for the results of the data processing. This view is usually a web page that is returned to the web server by the controller and the web server returns it to the requesting client. The view templates usually use technology like PHP (or JSP or ASP) that we covered earlier where programming code can be mixed with HTML in a template.

![MVC](is651-images/f9-2_opt.png)

Figure 9.2. MVC. 

Another design pattern used by web frameworks is object relational mapping (ORM). In the MVC pattern described above we saw that the model must get data from the database. The ORM layer is between the model and the database and creates a virtual object database that can be used from within the object-oriented programming language. It also uses application (object) caching to improve performance.

There are hundreds of web frameworks in many different programming languages. We will survey only a few of them and concentrate on an example using PHP since we studied that previously. We will classify them according to the three major implementation technologies of J2EE (java), .NET (Microsoft), and web scripting environments.

There are many web frameworks that use the java web application server technology. A java web application must run in a servlet container that communicates with a web server via the java servlet API. Servlet containers are also called web containers. Java servlet containers typically run as a process that creates a new thread for every request.
The most recent and commonly used framework for java is java server faces (JSF) which uses the MVC pattern. The structure of JSF is shown in figure 9.3.

![JSF](is651-images/f9-3_opt.png)

Figure 9.3. JSF. 

The faces servlet is the controller that does processing using java objects and instantiates views with Java server pages (JSP) that contain JSF tags. JSF tags are a special tag library that make programming simpler for common web scenarios. There are many IDEs that support JSF.
A common one is NetBeans. Java web application servers can run on almost any operating system.

ASP.NET is an MVC web framework from Microsoft that builds on the .NET component model and uses programming languages such as C#. All the same concepts apply. The web browser requests a page (with a .aspx extension)
and the routing layer converts the URL to an action in the front controller. Since C# is an object-oriented language, there is an ORM layer for the model. Views are created with active server pages (ASP)
that allow code to be mixed with HTML but the code is executed server-side. The ASP.NET framework is highly optimized for Microsoft server environments and makes programmers very productive with the Microsoft Visual Studio IDE, but of course, it can only be used with the Microsoft operating system.

There are many frameworks based on web scripting languages. A few examples are (where the framework name is followed by the programming language in parentheses):

-   Ruby on rails (Ruby)
-   Express (server-side Javascript)
-   Django (Python)
-   CodeIgniter (PHP)

Ruby on rails (ROR) has been very influential and has influenced the design of many other web frameworks that use other scripting languages. The web frameworks for scripting languages are all MVC, use object-oriented programming languages and follow the same patterns we have already seen. We will look at a java based MVC example four our applied MVC exercise.  

#### Silex

We will also install a different style of micro-framework that works well for <em>single-page apps</em> called [silex](http://silex.sensiolabs.org/).  This install also is basically just an unzip of the zipped source.  Read the docs to see how the routing is different than TinyMVC.  You will not be doing much with this framework now except showing the start 'hello' file, but we will use it in chapter 11. You can see it in my gl account here at http://swe.umbc.edu/~canfield/silex/web/index.php/hello/kip. You can get rid of the ugly index.php (front controller or router) using a <em>.htaccess</em> file inside the web directory with this one-line content (but with your username of course):

`FallbackResource /~canfield/silex/web/index.php`

This allows you to use urls such as:

`http://swe.umbc.edu/~canfield/silex/web/hello/kip` 

The front controller is still called each time, but since it is in every url, the .htaccess file inserts it. This allows one to have 'pretty' urls.


### Chapter 9 Exercises

Do the end-of-chapter exercises for each chapter of the book by following the link in the on-line syllabus.
