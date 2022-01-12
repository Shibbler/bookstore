3005 Final Project
Members: Lucas Colwell #101102212, Owen Allen #101119431, Vivek Chand #101119792

Lucas Colwell designed the web aspects, and assisted Owen Allen with SQL views and functions.

1.1 Conceptual Design
Diagram can also be seen in the project GitHub under /instructions/ER_Diagram.jpg

Assumptions:
A book can have multiple publishers.
A book can have multiple authors
A publisher can have zero to many books
A author can have zero to many books
Front-end stores cart/basket
Default quantity for book is 0
Publisher are given their cut when books are sold, not stocked
Multiple books of the same title are allowed (title is not a superkey for book)

List of Entities: 
user_account
	Upon creating an account, a user’s data is stored in user_account.  The primary key for user_account is the attribute user_id which is unique for all users.  The user_account contains the user’s name (name), default billing information (billing_info), default shipping address (shipping_info) and whether or not the user is the owner (isadmin). user_account has a partial participation with user_order.It has a cardinality of 0 to many with the user_order relation

order_object
	The order_object a specific book for an order.  The attributes are the ISBN of the book, the quantity of that book ordered, and the order_id of the order.  The order_id and the ISBN make the primary key for the relation, since a user can order multiple books in a single order. Order_object has a total participation with user_order and has a total participation with the book_order. It has a cardinality of 1 with the user_order relation and it has a cardinality of 1 with the book_order relation. This is a weak entity set as it does not exist without information from the book entity. 

book
	The book contains all of the information that describes a particular book. The primary key for book is the ISBN. The attributes for book includes the book’s ISBN, title, genre, price, number of pages (num_pages), amount of copies (stock), and the publisher’s cut of the profit (pub_cut). Book has total participation with published, total participation with wrote, and it has a partial participation with book_order. It has a cardinality of 0 to many with the book_order relation and a 1 to many cardinality with the published and wrote relations. 

author
The author contains the information about the author of a book. The primary key for author is the author_id. The author contains the author name (author_name). Author has a partial participation with wrote. It has a cardinality of 0 to many with the wrote relation. 

publisher
	The publisher contains the information about the publisher of the book. The primary key for publisher is the publisher_id. The publisher contains the publisher’s name and bank account (bank_account). Publisher has a partial participation with published. It has a cardinality of 0 to many with the published relation.

List of Relations: 
user_order
	Once a user places an order, part of that order data needed for the order is stored in user_order.  The primary key for user_order is the order_id.  The order_id is also used as a foreign key in order_object, which will contain the books requested in the order.  The other attributes are the user who placed the order (user_id), the billing info that they chose to use (billing_info), the address they want their order to be shipped to (shipping_info), the current location of the order (order_location) and the date of the order (order_date).

book _order
	Is the relationship between the book object and the order_object. This is a weak entity relation.  It is not implemented in the code, as it is weak, and therefore order_object simply gets the primary key of book, which is ISBN, to identify the relationship here.

published
	The published relation contains data from the publisher and book entities to identify each book with a publisher. This relation uses the ISBN from the book entity and the publisher_id from the publisher entity to relate with one another.
 
wrote
	The wrote relation contains data from the author and book entities to identify each book with an author or multiple authors. This relation uses the ISBN from the book entity and the author_id from the author entity to relate with one another.

List of Views:
	author_sales
The author_sales view contains the number of books sold by the author
	genre_sales
		The genre_sales view contains the number of books sold by genre
	sales_by_date
		The sales_by_date view contain the number of books sold for each day
that an order was placed in the database.
	sales_by_date_book
The sales_by_date_book view contains the number of EACH book that was sold for each day
1.2 Reduction to Relation Schemas
Schemas:
publisher(publisher_id, name, bank_account)

published(publisher_id, isbn)

author(author_id, name)

wrote(author_id, isbn)

book(isbn, title, genre, price, stock, num_pages, pub_cut)

order_object(order_id, isbn, quantity)

user_order(order_id, user_id, order_date, current_house_number, current_street, current_city, current_province, current_postal_code, shipping_house_number, shipping_street, shipping_city, shipping_province, shipping_postal_code, billing_house_number, billing_street, billing_city, billing_province, billing_postal_code)

user_account(user_id, name, isadmin,shipping_house_number, shipping_street, shipping_city, shipping_province, shipping_postal_code, billing_house_number, billing_street, billing_city, billing_province, billing_postal_code)

1.3 Normalization of Relation Schemas

Functional Dependencies
Proof of BCNF:
BCNF: A relation schema R is in BCNF if with a set F of functional dependencies, for all functional dependencies 𝛼→ꞵ in F+, where 𝛼 is a subset of R and ꞵ is a subset of R, IFF at least one of the following holds:
𝛼→ꞵ is trivial (ꞵ is a subset of 𝛼)
𝛼 is a superkey for R.
All of our Relation Schemas are in BCNF. In conversation with a TA, we were told that cross-table checking was not required/desired, and that we just needed to prove BCNF/3NF for all of our individual schemas. All of our Functional Dependencies pass BCNF on rule 2, though we will prove using a mixture of both methods.

for publisher:
publisher_id->name,publisher_id,bank_account
	publisher_id is a superkey for the relation, so it passes on rule 2.

for published:
publisher_id,isbn->publisher_id,isbn
	This is a trivial functional dependency, so it passes on rule 1.

for author:
author_id->name,author_id
author_id is a superkey for the relation, so it passes on rule 2.

for wrote:
author_id,isbn->author_id,isbn
This is a trivial functional dependency. author_id and isbn form the superkey, a book is allowed to have multiple authors, so it passes on rule 1.

(books can be similar, two books may be similar but with a different isbn)
for book:
isbn->title,genre,price,stock,num_pages,pub_cut,isbn
isbn is a superkey for the relation, so it passes on rule 2.

(both are needed to imply ONE specific object)
for order_object:
order_id,isbn->quantity,order_id,isbn
order_id and isbn form the superkey for the relation, so it passes on rule 2.

user_order:
order_id->current_house_number,current_street,current_city, current_province,current_postal_code,shipping_house_number, shipping_street,shipping_city,shipping_province,shipping_postal_code,billing_house_number,billing_street,billing_city,billing_province,billing_postal_code, user_id,order_id,order_date

Since a user can place multiple orders, the user_id is not a superkey for the relation.  order_id is the superkey for the relation, so it passes on rule 2.

user_account:
user_id->name,isadmin,shipping_house_number,shipping_street,shipping_city,shipping_province,shipping_postal_code,billing_house_number,billing_street,billing_city,billing_province,billing_postal_code,user_id
user_id is the superkey for the relation, so it passes on rule 2.

All pass, so our schemas are all in BCNF.

1.4 Database Schema
Relation_Schema:

Diagram can also be seen in the project GitHub under /instructions/Relation_Schema.png

1.5 Implementation
For our implementation we used JavaScript, Express and Pug for the frontend and backend connection to the database.  The website is implemented using heroku.  Within heroku the Heroku Postgres resource is used to host the database in the cloud.  For local development and testing pgAdmin was used. Our architecture is simple, and uses the client-server module, where the server can also receive information from the Postgresql database.


The implementation uses an express server to handle all the requests it receives from the front end, which is a mixture of HTML get requests, XMLHTTPrequests and HTML form post requests. 
All the HTML requests are simply to serve the HTML of a new page, and are named accordingly, as serveWhateverPage. 

All the XMLHTTPrequests are in the client.js file, and simply get the contents of the book search page parameters, and send it to the server. 

All other inputs (user registration, login, specifying report parameters, etc) all use HTML forms, and send information to the server as post requests. 

Sometimes the user asks for a webpage without specifying any parameters themselves, but a query still must be performed. 

When the server gets information to specify a query, it is extracted out of either the body of the request or the parameters of the URL, and is then substituted into a Postgresql query, that normally looks something like this (on next page):

As you can see above, the code gets information from the body of the request that was sent, with information filled out by the user. The information can then be put into an sql query.

You will notice that to change the webpage we are using render, which takes a pug page (a template engine for an html page), and gives it data, which is normally data received from an SQL query. This information can be accessed in PUG like so:

This is the information that gets passed from the author report page, which is viewed here:


The information from an SQL query is stored in queryResult, and passed into HTML for the user to view. All pages follow this same logic (and all functions are documented in the server.js file).

To check if the user is an admin (and therefore a book owner), their access to links is restricted by navigation and what is shown to them. This is done by getting this information when they login from an SQL query using their user_id (as shown above), and restricting it in the HTML.

To store the login information (and allow for differences between admin logins and user logins), express-sessions is used. We only added one admin to our db, but because we get it from user_account isadmin, you are able to define as many owners (admins) as you would like, we just deleted a few to make our testing for you more straightforward.

If there are any other questions or something is not clear, please ask us to demo, we would be willing to, to ensure we can show you that everything works as intended for your testing. If there are any confusions over what is “proper input”, we are very willing to show you an “Einstein level” user run of our site.

In accordance with the Prof’s answer here, we built our code without error checking. This means wrong input can crash the server, so please follow proper input, the types will be provided (I.E, postal code must be A1B2C3 NOT A1B 2C3, authors and publishers for a book must exist when adding a book, etc)

1.6 Bonus Features
Our bonus feature is being a functional website hosted on Heroku, rather than just having a local server. As such, our code is easier to test, as you just need to go to our heroku hosted website. For more specific information about how we MADE the heroku site, check the for_devs folders in the github, as this was our way of documenting steps and checking logs.
	Heroku is a cloud platform that lets people build, deliver, and monitor apps. 
Our project is hosted on a Heroku domain at: 
https://boppa-bat-bookstore.herokuapp.com/

Our web-server is set up to deploy with every Github commit, so we deploy like this (though you can deploy independently of Github commit).
To view our Database (which we connected to Heroku), click on the resources page:

Now, click on Heroku Postgres:

This page shows us some useful information about our Database.
Click on Dataclips

Here are some dataclips we made, to help ourselves access data from the DB that was hosted on the server. To make your own query on our hosted db (to prevent yourself having to run our SQL, though of course that would work as well, though this works with our website as opposed to a local server), click on Create Dataclip.

Enter a title, and a query you would like to make. Notice the Schema is listed on the right side of the page, for ease of remembering table names and values. Once you have written your query, hit Save and Run. An example is shown below (note that this access is read only, no adding or injecting is possible).

We assume that admin has access to these dataclips, and thus is able to view everyone’s information. Of course, they can view information on the website, but this provides a way for the admin to directly view ALL information.

1.7 GitHub Repository

The Original Project GitHub repository can be found at https://github.com/Owen-Allen/-bookstore

The instructions for running the psql queries can be found in /instructions/README.md

More specific instructions for setting up the db with regard to Heroku can be found in /instructions/for_devs/deployment.md

1.8 Appendix I
Dates available: 
Any time Monday December 20th between 12 - 3 pm




