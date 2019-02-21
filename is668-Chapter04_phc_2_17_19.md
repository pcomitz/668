## Chapter 4 - SOAP

### Introduction

SOAP was originally defined as the "simple object access protocol" in 1998 in a Microsoft project, but since is really has nothing to do with objects, it is now just a name and not an acronym. It is the messaging protocol for XML web services and a requestor sends the message to the provider of a service in a service-oriented architecture (SOA) using SOAP. SOAP is a wire protocol that gets information from one place to another at the application layer in a distributed system. SOAP is a standard XML document that encodes that information. SOAP is transport independent and can use any network protocol to carry the XML, but it is almost always used with HTTP using the regular web infrastructure. The SOAP document is put into the entity body of HTTP. This is very convenient since all information systems today connect to the web and already have firewalls configured to pass port 80 traffic. This was one of the big problems with CORBA (chapter 2). Since there was no such common infrastructure in those days, it had to be created from scratch.
Recall IIOP which was the wire protocol for CORBA. Figure 4.1 repeats the general architecture of XML web services under SOA.

The system works like this:

1.  The requestor searches a registry database for the kind of web service that it needs. A standard XML protocol for this search is the universal description, discovery and integration protocol (UDDI). UDDI is not a very popular standard and we will discuss     alternatives for the registry in chapter 5.
2.  When it finds one, it requests a description of that service from the provider of the service. This description tells the requestor how exactly to invoke the service using SOAP. An XML standard for this is the web services description language (WSDL) and we learn this in chapter 5.
3.  Finally the requestor invokes the service using SOAP and the     provider returns the result with SOAP.

![SOA with XML web services protocols](is651-images/f4-1_opt.png)

Figure 4.1. SOA with XML web services protocols.

When SOAP was first developed, it was used to do RPC-style messaging where for example, the SOAP request made a procedure call and a SOAP response returned the result of the procedure. In those days, there was no XMLSchema and so the SOAP specification included a SOAP encoding that standardized how data structures and types were encoded in SOAP messages. We will see this distinction more specifically in the next chapter on WSDL, but since the adoption of XMLSchema, the SOAP encoding is depreciated and only XMLSchema is typically used to specify types in a SOAP massage now. So we will learn XMLSchema and the related concept of namespaces before we turn back to learning SOAP XML.

### XMLSchema and Namespaces

Recall that XMLSchema is the alternative and more modern method of validating XML documents. We will use the on-line w3schools tutorial on XMLSchema (all sections of Learn Schema) for our examples in this chapter. See the on-line syllabus for further information.

XMLSchema is a schema language that was developed in 2001 and published by the W3C. It has become the dominant schema language today. An older schema language is the one we learned earlier, DTD. Other modern alternatives are RELAX NG and Schematron. A schema language describes a set of rules for a markup language so it can be validated automatically.
XMLSchema has three major differences from DTDs:

-   it has XML syntax
-   it has datatypes
-   it uses namespaces

XMLSchemas, as we will see below, have XML syntax unlike DTDs. XMLSchema files are separate (they cannot be internal) from the XML file(s) that they describe and have the extension .xsd by convention. XMLSchemas also define datatypes unlike DTDs. There are three kinds of types:

-   built-in datatypes
-   simple datatypes
-   complex datatypes

Built-in datatypes correspond to basic fundamental types such as decimal, integer, and string. Simple types can be created for XML elements that contain only text, while complex type can be made up of more complex combinations of elements. See the w3schools tutorial for examples of each. XMLSchemas also support restrictions that can define in detail the acceptable values for any elements or attributes in XML,
such as only integers between 0 and 120. As you can see, XMLSchema is much more powerful than DTDs. It is also the one used with XML web service protocols.

XMLSchema is very similar to a class in object-oriented programming as illustrated in figure 4.2. A class is like a template for creating instances of objects and a schema serves the same purpose and also allows validating those instances.

![Classes are similar to schemas](is651-images/f4-2_opt.png)

Figure 4.2. Classes are similar to schemas.

Listing 4.1 shows the very simple schema of the basic note example at w3schools. We see that the example has XML syntax and datatypes, but it also has namespaces. Namespaces are a general concept from programming to avoid name collision. Name collision happens when the same name is chosen mistakenly for two different objects. For example, if you and I were collaboratively programming, but in separate locations, we would have to make up variable names in our program. It might happen that we would sometimes accidentally choose the same variable name for different logical things. In order to avoid this, we can use namespaces where the names must only be unique within the namespace. So if I used the namespace kip and the other programmer used the namespace sue we could choose any name we wanted. We might adopt a prefix convention where our names were prefixed with our namespace to make the name unique as in kip:item and sue:item.

    < ?xml version="1.0"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
        <xs:element name="note">
             <xs:complexType>
                 <xs:sequence>
                     <xs:element name="to" type="xs:string"/>
                     <xs:element name="from" type="xs:string"/>
                     <xs:element name="heading" type="xs:string"/>
                     <xs:element name="body" type="xs:string"/>
                 </xs:sequence>
             </xs:complexType>
        </xs:element>
    </xs:schema>

Listing 4.1. A simple XMLSchema file.

This is exactly what XML namespaces do. In listing 4.1, we see that the prefix xs is used for all the schema tags. The prefix is defined with the xmlns attribute which points to the actual namespace that always has the form of a URL. These namespace identifiers are arbitrary and only need to guarantee uniqueness. They do not need to point to anything on the web. But for standard namespaces like the one for XMLSchema, they usually do point to some documentation as you can verify.

The prefixes are arbitrary and you can make them up, although for standard namespaces, there are usually conventions like the xs in the example although you will often see xsd instead. A default namespace is declared with the xmlns attribute without any prefix. This means that all elements with no prefix are in that default namespace. XMLSchemas are for defining a namespace for the elements in an XML document as we will see below and so the default is reserved for the content being validated.

Recall the note example of Listing 4.1. An xml document that conforms to that schema is:
<?xml version="1.0"?>

<note
xmlns="https://www.w3schools.com"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="https://www.w3schools.com/xml/note.xsd">
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>

Listing 4.2. XML Document with a reference to an xml schema (from w3schools).


### SOAP structure

The SOAP XML structure is defined in this section. It is, of course,
defined in an XMLSchema. In listing 4.5, we see that it is defined in the namespace http://www.w3.org/2001/12/soap-envelope and since it is a standard schema, it points to the actual schema for SOAP where all the tags for SOAP are defined.

All SOAP documents consist of an envelope and inside that tag is the optional header and required body tags. Lisiting 4.4 shows an RPC-style request for the procedure GetProductPrice with the parameter of productId. The header includes a requirement that whatever provider gets this message, it must understand the transaction ID.

    < ?xml version="1.0" encoding="utf-8"?>
    <soap:Envelope xmlns:soap="http://www.w3.org/2001/12/soap-envelope">
         <soap:Header>
             <tx:Trans xmlns:tx="http://www.example.org/transaction/"
            soap:mustUnderstand="1">
                234
             </tx:Trans>
         </soap:Header>
         <soap:Body xmlns:m="http://www.example.org/product-prices">
             <m:GetProductPrice>
                <m:productId>450R</m:productId>
             </m:GetProductPrice>
         </soap:Body>
    </soap:Envelope>

Listing 4.4. An RPC-style SOAP document.

Anything in the header modifies or offers services to the main payload in the body. We will see many more examples of the header elements in chapter 6. There are two styles for SOAP messages. We have seen the RPC-style and the other is the document style. The document style sends a complete XML document rather than a procedure call as in listing 4.5.
It is a much more loosely-coupled style than RPC and more congruent with SOA. Figure 4.5 shows a document-style SOAP message.

The Body element is a generic container in that it can contain any number of elements from any namespace. This is ultimately where the data goes that you"re trying to send. Since there are unknown foreign namespaces in SOAP, the schema for SOAP must allow these unknown and unpredictable namespaces. It does this by using wildcard attributes that begin with a ##. For example, ##any is for any namespace. We will take a look at the schema for SOAP in one of the exercises.

<?xml version="1.0"?>

<soap:Envelope
xmlns:soap="http://www.w3.org/2003/05/soap-envelope/"
soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">

<soap:Body>
<m:GetPrice xmlns:m="https://www.w3schools.com/prices">
<m:Item>Apples</m:Item>
</m:GetPrice>
</soap:Body>

</soap:Envelope>

Listing 4.5. A Document-style SOAP document.

SOAP is now in version 1.2 and people usually refer to that as version 2. SOAP 1.1 is still the dominant version in use, however, and is called version 1. For XML web services, a requestor creates a SOAP message and typically sends it to a provider using an HTTP binding as in figure 4.3.
The XML is standard for all web services participants, but of course,
the actual program doing the web service is written in some programming language that interfaces with the XML - typically J2EE, .NET, or a web scripting framework.

![Distributed messaging with SOAP](is651-images/f4-8_opt.png)

Figure 4.3. Distributed messaging with SOAP.

A SOAP message may go to many different hosts in a distributed system in order to, for example, complete a workflow. There can be only one ultimate receiver, however, that can operate on the body. All the intermediaries can only operate on the header as we will see in chapter 6. The intermediaries typically do things like encryption,
authentication, and authorization. If a service provider gets an error in processing a SOAP document, there is a special fault tag defined in the SOAP schema that goes in the body of the SOAP response and gives information on the error. An example is shown in listing 4.6. You can see the SOAP fault codes at w3schools.


    < ?xml version="1.0" encoding="UTF-8"?>
    <soap:Envelope
        xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
        xmlns:xsi="http://www.w3.org/1999/XMLSchema-instance"
        xmlns:xsd="http://www.w3.org/1999/XMLSchema">
        <soap:Body>
            <soap:Fault>
                <faultcode xsi:type="xsd:string">
                    soap:Client
                </faultcode>
                <faultstring xsi:type="xsd:string">
                    Failed to locate method.
                </faultstring>
            </soap:Fault>
        </soap:Body>
    </soap:Envelope>

Listing 4.6. SOAP fault.

### SOAP Examples
Let's look at a very simple SOAP Web service, created using php. 

```
<?php
class MyService
{
    public function add($x, $y)
    {
        return $x + $y;
    }
}

$options = array(
    'uri' => 'http://server/namespace',
    'location' => 'http://swe.umbc.edu/~pcomitz/programs/wk4/soapserver.php',
);

$server = new SoapServer(null, $options);
$server->setObject(new MyService());
$server->handle();
?>
```
Listing 4.6. Simple SOAP Service

This simple service accepts two numeric inputs and returns their sum. Note that the SOAP protocol is handled
entirely by the php class  <code>SoapServer</code>.  The class takes two parameters. We will not use the first 
parameter, you may set this parameter to null. The second parameter is the <code>$options</code> array. Set the 
<code>uri</code> parameter to a string that identifies your server and namespace - for practice services such 
as those we will write you may use something generice, as shown above. For <code>location</code>, set this string 
equal to the path to the SOAP server, as shown above.  Create an instance , called <code>$server</code> in this 
example, amd pass in an instance of the yservice functionality - in this case, <code>class MyService()</code>
which contains the <code>add</code> method.  Note: the documentation for the SoapServer class is 
[here](http://php.net/manual/en/class.soapserver.php). 


### The PHP5 SOAP Client
There has to be a soap client that calls the soap server and optionally there can be an html form that sends the parameters to the soap client. Services are platform, vendor, and language neutral. Our example clients are written in php, but they could be written in many other languages. Listing 4.7 is an example that uses the simple service of Listing 4.6. 

```
<?php
// soapclient2.php
$options = array(
    'uri' => 'http://server/namespace',
    'location' => 'http://swe.umbc.edu/~pcomitz/programs/wk4/soapserver.php',
);
$client = new SOAPClient(null, $options);
echo $client->add(10, 10);
?>
```
Listing 4.7. PHP5 SOAP Client

Remember that to execute listing 4-7 in a web page it must be prefaced with `<?php` and it must end with `?>`. As
above, the SOAP protocol functionality is contained in a php class, in this case <code>SoapClient</code>. The documentation 
is [here](http://php.net/manual/en/class.soapclient.php). 
 

The example client of Listing 4.7 is constructed in much the same way the as the server of Listing 4.6. Note the 
<code>$options</code> array. The <code>uri</code> string can be generic, as above.  The <code>location</code> string must 
contain the URL to the SOAP service. Create an instance of the SOAPClient (called <code>$client</code>) and invoke the exposed 
services method, in this case <code>add</code>. Note that a service may have multiple exposed methods. 

An example invocation of the SOAP service is [https://swe.umbc.edu/~pcomitz/programs/wk4/soapclient2.php](https://swe.umbc.edu/~pcomitz/programs/wk4/soapclient2.php). 


