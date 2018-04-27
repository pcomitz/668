 

## Chapter 11 - REST Revisited

### Introduction

We discussed the basics of the REST architectural style in chapter 7. We will add a little more detail about the principles and then do several case studies in this chapter using REST and REST APIs in order to make these principles concrete. The principles are:

REST web services must have a uniform interface. For systems deployed with HTTP, this means that the HTTP methods are used. The POST method is often overloaded for clients like web browsers that do not support PUT or DELETE. Another case is to get around long URLs. Many clients and servers have limits on the length of a URL and for GET, the querystring is in the URL. Apache, for example, will not respond to requests for URLs longer than 8 kb. So if a client can't make a GET request to because of URL length restrictions, a POST request can simulate it because there is no length restriction for the HTTP entity body.

REST should be stateless. There are two kinds of state: application state for the client, and resource state for the server. For REST architectures, the benefits of statelessness come from keeping the server stateless and pushing it to client applications. The client can keep state in a simpler fashion since it is only interested in itself and the web service receives the client application state only in a request so resource state is the same for all clients.

REST must handle some state. Of course, statelessness is a goal and cannot be achieved all the time. An interesting example of non-stateless behavior violating statelessness the API key for REST APIs such as the one from Flickr. Flickr, like all such organizations, cannot really trust clients to keep all state information themselves. I know this will come as a shock, but there are some bad people out there that will lie and cheat. So Flickr has to keep track of API key sessions and this effects scalability. Flickr has many machines serving customers (server farm) and so this causes a complication. Every server in the farm needs to track the sessions and the load balancer needs to make sure that same session requests goes to the same machine. We saw an example of this complication with transactions and HTTP cookies in chapter 8.

REST should support caching. HTTP GET or HEAD requests should be cached.
Servers should use the Expires and cache- control headers in HTTP enforce this. PUT, POST, or DELETE requests should not be cached.
Furthermore, any cached responses for that URI should expire.

The REST architecture is composed of hierarchical layers. Each layer only sees the next layer that it is connected to. We have seen this behavior with the proxy clients that we have used in previous exercises where an HTML client connects with a PHP proxy client that requests the web service. This reduces complexity and improves scalability with proxy clients, caching, and load balancing.

### RSS Feeds Revisited

RSS feeds are really the first REST web service (before it was called that!). A RESTful URL HTTP request gets an XML return. We will use another PHP library to help us manipulate RSS feeds called magpie (http://magpierss.sourceforge.net/). It is an XML parser that works with all versions of RSS. listing 11.3 shows how to use the magpie library in a PHP program. Any library for any language that works with XML web services must use a parser.

    < ?php
        require_once 'magpie/rss_fetch.inc'; //1
        $url = 'http://feeds.washingtonpost.com/rss/sports'; //2
        $rss = fetch_rss($url);
        $i=0;
        echo "Site: ", $rss->channel['title'], "<br>"; //3
        foreach ($rss->items as $item) { //4
            $title = $item[title];
            $url = $item[link];
            echo "<a href=$url>$title</a></li><br>";
            if(++$i==3) break;
        }
    ?>

Listing 11.3. A PHP program using the magpie library.

The comment numbers are referenced below:

1.  As usual, we must include the library code for use in the     program.
2.  I have used an RSS feed from the Washington Post sports. One can, of course, substitute the URL for any feed here. You should go to the feed URL and see that it is typically transformed by default in your web browser, but the program receives the XML. You can see this by using curl from the command-line as:
    
    `curl http://feeds.washingtonpost.com/rss/sports`
    
    You will see the XML feed rather than the HTML that your browser displays.
3.  Magpie parses the XML of the feed into PHP arrays. That is how the code can address tags using array notation such as `channel['title']`. Note that magpie returns a PHP object and then uses the arrow notation to access each tag as in: `$rss->channel['title']`.
4.  The foreach loop gets the desired subset of these tag variables for display.

There are two basic kinds of XML parsers:

-   Object-based (DOM)
-   Event-based
    -   Push Parsers (SAX)
    -   Pull Parsers (StAX)

Object-based parsers parse the XML and create a single hierarchical object in memory that can be manipulated programmatically. Your web browser works this way to create the DOM and it is a very powerful technique as we saw in chapter 2. The drawbacks are that it can be a slow parser and consume a lot of memory. This is not a problem in your web browser since the documents are usually a manageable size and the DOM is required for javascript to operate globally. Other programs do not have these requirements and so an event-based parser works well. DOM parsers are usually built on top of event-based ones.

Event-based parsers are also called stream parsers. They read through the XML document and programmatically do something whenever an event happens such as reading a certain tag. They do not store the whole document in memory and are therefore very fast and not memory intensive.
There are two kinds of event-based parsers, push and pull parsers.

A push parsing model is a model where the parser pushes parsing events to the application. SAX (simple API for XML) is the most common example of a push parsing API for XML. An application using SAX would define a number of callbacks that would be called by the parser when certain events occur. For example, an application can define a startElement method that the parser will call every time it sees the beginning of a new element. SAX is a one-pass parser that goes through the document once from beginning to end and sends one event to the calling application for each defined XML construct it encounters. Magpie is this kind of parser and is created with an open-source XML parser library called expat.

A pull parsing mode is a model in which the application drives the parser (in contrast to the push model, where the parser drives the application), by pulling parsed XML constructs from the parser one by one, according to its needs. A Pull parser returns XML events to a client on request. An example of a pull parser is StAX (streaming API for XML) which is commonly used in java frameworks for web services.

###Mashups

A mashup is an application that uses and combines data from two or more sources to create new services. They are typically deployed on the web by creating a web browser-based application that requests data from the sources using web services and then combines them in such a way as to add value. You can see that REST APIs make this an efficient way to create powerful applications. There are many consumer-oriented mashups such as ones that use data from a source and then overlay it on google maps using location information. Business mashups are also common for combined information from various sources for business intelligence purposes. Mashups can be (most typically) client-based as are the ones that aggregate data in the web browser. They can also be server-based,
however, where the server aggregates the data and sends it to the client in the combined format. This is the way that an older technology called portals works. They are the same thing as a mashup, but pre-date the use of the web. The on-line course syllabus has links to some representative mashups. Mashups are an excellent example of what a SOA offers.

We can create a simple mashup using RSS from Rolling Stone and the Omdbapi API for movies. We will go over this example in lecture in detail using the following files. Note that for any php file here, you can replace the .php with .txt to see the code (except for the first final hw one!).

1. Here is the <em>final</em> mashup that we are doing for hw [here](https://swe.umbc.edu/~canfield/rest/mashup/omdbapi_mashup_final2.php).
2. Here is a <em>starter</em> file for you to modify [here](https://swe.umbc.edu/~canfield/rest/mashup/omdbapi_mashup_starter2.php). Be sure to make the url change each time to reflect a different title and [urlencode](http://www.w3schools.com/tags/ref_urlencode.asp) the title using the built-in [php function](http://php.net/manual/en/function.urlencode.php).
3. Note that the starter calls the [helper function](https://swe.umbc.edu/~canfield/rest/mashup/omdbapi.txt) that I have written for you.
<a name="restapi"></a>

###Creating a REST API

We have been using REST APIs from around the Internet in clients that we created. In this section we want to create our own REST API to the catalog database that we developed in chapter 9. I developed a cdStore service and you made some other kind of catalog available on line. A REST API to the cdStore service will be geared towards program to program processing rather than human users as in chapter 9.  

Every rest api has at least 5 cases (6 with PATCH) for CRUD (create, read, update, delete):

- GET with no record id - reads all records - http://url/path/
- GET with a record id - reads a specific record - http://url/path/id
- POST with no record id - creates a new record - http://url/path/
- PUT (or PATCH) with a record id - updates a record - http://url/path/id
- DELETE with a record id - deletes a record - http://url/path/id

All other combinations are illegal.  The php program must detect which actual http method is used for a request and then parses the url to find out if it has an id or not.  We will use the silex web framework to implement the REST API because the routing makes the http method explicit and that makes writing the api easy.  Recall the page in silex from chapter9: https://swe.umbc.edu/~canfield/silex/web/index.php/hello/kip that uses the get method.

        $app->get('/hello/{name}', function($name) use($app) { 
                return 'Hello '.$app->escape($name);
        }); 

All requests go to the single file *index.php* and then the program parses the query string to detect any record ids or parameters.  This called a *router* because it is a single point of entry to the framework that decides what to call or sometimes called a *front controller*. Typically a [*.htaccess file*](http://en.wikipedia.org/wiki/Rewrite_engine) would be used to hide the index.php and allow the request to have a pretty url like `http://host/web/hello/kip` instead of `http://host/web/index.php/hello/kip`.

Here is a rest api written in silex for the previous 'grade roster' application database:

        < ?php

        require_once '../vendor/autoload.php';
        use Symfony\Component\HttpFoundation\Request;
        use Symfony\Component\HttpFoundation\Response;

        // init Silex app
        $app = new Silex\Application();

        //configure database connection
        $app->register(new Silex\Provider\DoctrineServiceProvider(), array(
            'db.options' => array(
                'driver' => 'pdo_mysql',
                'host' => 'xxx',
                'dbname' => 'yyy',
                'user' => 'yyy',
                'password' => 'zzz',
                'charset' => 'utf8',
            ),
        ));

        // get route for "/grades" URI: load grades list and return it in JSON format
        $app->get('/grades', function () use ($app) {
          $sql = "SELECT * FROM test3";
          $grades = $app['db']->fetchAll($sql);
          
          return $app->json($grades, 200); //return code from http
        });

        // get route for "/grades/{id}" URI: load specific grade info and return it in JSON format
        $app->get('/grades/{id}', function ($id) use ($app) {
          $sql = "SELECT * FROM test3 WHERE id = ?";
          $grade = $app['db']->fetchAssoc($sql, array((int) $id));

          return $app->json($grade, 200);
        })->assert('id', '\d+');

        // post route for "/grades" 
        $app->post('/grades', function (Request $request) use ($app) { 
          $grade = array('name'=>$request->get('name'), 'grade'=>$request->get('grade'));
          $app['db']->insert('test3', $grade);

          return $app->json($grade, 201); 
        });

        // wrong!! create route for "/grades/name/grade" - needs to use post 
        $app->get('/grades/{name}/{grade}', function ($name, $grade, Request $request) use ($app) { 
          $grade = array('name'=>$name, 'grade'=>$grade);
          $app['db']->insert('test3', $grade);

          return $app->json($grade, 201); 
        });

        // put route for "/grades/{id}"
        $app->put('/grades/{id}', function ($id, Request $request) use ($app) { 
          $grade = $request->request->all();
          $app['db']->update('test3', $grade, array('id'=>$id));

          return $app->json($grade, 200); 
        })->assert('id', '\d+');

        // patch route for "/grades/{id}"
        $app->patch('/grades/{id}', function ($id, Request $request) use ($app) { 
          $grade = $request->request->all();
          $app['db']->update('test3', $grade, array('id'=>$id));

          return $app->json($grade, 200); 
        })->assert('id', '\d+');

        // delete route for "/grades/{id}"
        $app->delete('/grades/{id}', function ($id) use ($app) { 
          $app['db']->delete('test3', array('id'=>$id));

          return new Response("grade ".$id." deleted\n", 200);
        })->assert('id', '\d+');

        // default route
        $app->get('/', function () {
          return "List of available methods:<br/><ol>
          <li> /grades - returns list of existing grades;</li>
          <li> /grades/{id} - returns list of student grades by id</li></ol>";
        });

        $app->run();

Implement this and try each of the *method calls*. The following shows you two examples of using curl from the command line to access the rest api where I have called my router *rest.php* that contains the rest api code.  The router can be called anything although many times it will be index.php.  Install or use [curl](https://curl.haxx.se/) on your own computer rather than using it on gl.  Alternatively, you can open a free  [codeanywhere.com](https://codeanywhere.com) account and use the command-line there - see chapter 12. Even if you use your own computer for curl, create a php workspace in codeanywhere and share it with me at this time.

-  `curl -v -X GET 'https://swe.umbc.edu/~canfield/silex/web/rest.php/grades'`
-  `curl -v -X POST 'https://swe.umbc.edu/~canfield/silex/web/rest.php/grades' -d 'name=joe&grade=3'`

The methods *put*, *patch*, and *post* require a querystring that is put into the '-d' option for curl.  If you create records, be sure to delete them also.

Note that there is a *wrong* route in the code that violates the rest api rules.  This is to show that one can make an api that works perfectly, but is not conforming to rest api rules.

###Chapter 11 Exercises

Do the end-of-chapter exercises for each chapter of the book by following the link in the on-line syllabus.
