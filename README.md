# Dad-Jokes-Chrome-Extension-master

Dad-Jokes-Chrome-Extension You click on this google chrome extension to get a random Joke every time 😂

I thought it would be worth trying a simpler experiment first, as we have often got so far with Microsoft 365 development only to find we don’t have all the required permissions. In fact, the practice example in the Microsoft documentation required me to create a resource group in Azure, which I don’t have permission to do. However, I knew of a very simple Dad Jokes API that we used in my CodeClan course, and thought it would be a fun way to do a basic test of the concept.

The API serves a random Dad Joke from the address https://icanhazdadjoke.com, in either text or JSON format depending on the HTTP headers used in the GET request. Here is their description:

Endpoints
Fetch a random dad joke
GET https://icanhazdadjoke.com/ fetch a random dad joke.

Fetching a random joke as JSON:
$ curl -H "Accept: application/json" https://icanhazdadjoke.com/
{
"id": "R7UfaahVfFd",
"joke": "My dog used to chase people on a bike a lot. It got so bad I had to take his bike away.",
"status": 200
}

Fetching a random joke as text:
$ curl -H "Accept: text/plain" https://icanhazdadjoke.com/
My dog used to chase people on a bike a lot. It got so bad I had to take his bike away.

Back to the Index

Using Postman
If there is a Swagger file available for the REST API, it is possible to create a Power Apps custom connector by importing that as OpenAPI.

Without that, it is also possible to create a custom connector by importing a collection exported from Postman. This process is described in Microsoft’s documentation at Create a custom connector from a Postman collection.

Screenshot of my very small Postman collection, consisting of 3 versions of one API end point
A very small Postman collection

So I created a very small ‘collection’ of three versions of the basic API GET endpoint, with headers for accepting text, JSON and HTML, and tested them in Postman. (If you’re following along, only use the JSON version, as the custom connector seemed to only take the first version and treat the others as the same request). This process is described in the Microsoft documentation at Create a Postman collection for a custom connector.

Here’s the text version:
Screenshot of the GET request in Postman returning the plain text of the joke.LONG DESCRIPTION
Plain text: The joke says, “Hey, dad, did you get a haircut?” “No, I got them all cut.”
My husband has been telling a version of this joke for 25 years…

Here’s the JSON version:
Screenshot of the Dad Jokes API returning JSON.LONG DESCRIPTION
The joke says, “I thought about going on an all almond diet. But that’s just nuts.”
This is definitely more fun than OAuth2 authentication with Azure and Learn.

The export command is in a drop down menu accessed via the three dots next to the collection name:
Exporting a Postman collection: the menu option is next to the collection name.
The Export menu option can be found next to the collection name.

One tricky part of this process was that the current version of Postman only exports versions 2 and 2.1, while Power Platform custom connectors can only import version 1.

Reading the Postman documentation, I found a way to convert a version 2 package to version 1 using a utility for Node in a command line window. This part of the process is described in reverse at Postman: Importing and exporting data: Converting Postman collections from v1 to v2. The command npm -v in a terminal window will let you know if you have Node available.

If you haven’t got Node, you could try the alternative method of installing an older version of Postman, as described in this Medium article: How to FIX: Postman Collection Format v1 is no longer supported.

So I exported the collection from Postman using version 2.0 format:
Exporting the Postman collection in version 2 format
Exporting in version 2 in the hopes I can convert it to version 1.
It might not sound like progress, but it works 🙂

Next, I ran a command line utility in a Unix emulator window to convert the exported collection from version 2 to version 1.

This is the command line instruction to install the Postman Collection Transformer utility:

npm install -g postman-collection-transformer

This is the command line instruction for converting to the earlier Postman format:

postman-collection-transformer convert -i Dad-Jokes.postman_collection-v2.json -o Dad-Jokes.postman_collection-v1.json -j 2.0.0 -p 1.0.0 -P

Back to the Index

Creating a Custom Connector in Power Apps
Next, I imported the collection to create a Custom Connector in my Power Apps environment.

Going into Power Apps, I chose ‘Custom Connectors’ from the Data menu options in the left hand menu, then ‘New custom connector’. ‘Import a Postman collection’ is one of the options in the drop down list that appears:
Menu option to import a Postman collection
Ignore the fact it’s already there… 🙂

I entered a name for my connector, and then selected the exported version 1 Postman collection. Although the file I selected was a zip file, what appears in the dialogue box is generatedApiDefinition.swagger.json.

The next stage takes us to an editing screen for the connector, with five pages that you can step between:

General:
Screenshot of page 1 of the Power Apps custom connector editor
I definitely should have given this a better icon


The host and base URLs have been extracted from the file, and there is a text box to enter a description and the option to add a better icon for your connector, which I should have done. This would have to be a PNG or JPG, and less than 1MB in file size.
 
Security:
Screenshot of the security page. Authentication type says No authentication.
Good thing this isn’t a nuclear plant


The next step is security, which in this case is none, as there is no authentication required to use the Dad Jokes API.
 
Definition:
This is a longer page and I can’t get a good screenshot of the whole thing, so I’ve broken it down. First we have text boxes for the name and description, and the name you’ll be using to call it from your Power Apps editor:
Text boxes to enter identifying information for the connector
The third box is for the name you’ll be using to call your request in Power Apps

I left ‘visibility’ as the default setting and that was fine.

The next part of the Definition screen is for Request parameters. We can see that the Accept header has been included, and there appear to be possibilities for adding path variables and query parameters too, not that we need them for this app:
Request parameters in the custom connector definition screen
Good to see that Accept header listed…

Clicking on ‘Edit’ to look at the ‘Accept’ parameter in more detail allows us to check that the correct value of ‘application/json’ has been imported from Postman:
The accept header details show the request is correctly defined to receive the response in JSON format
The request is correctly defined to receive the response in JSON format, which allows us to do more with the jokes as variables

Collecting the response in JSON format will give each joke an identifier, a response status code, and a string variable containing the text of the actual joke.

The last section of ‘Definitions’ details the request response and possible status codes, and shows that a validation test has succeeded:
The response and validation sections of the Definition screen
Validation succeeded: that looks promising


 
Code (Preview):
The next step allows for adding custom C# code to act on the request and response payloads. Thankfully, that isn’t required now, but it’s an option that’s there:
The Code Preview screen in Power Apps Custom Connectors
See how much more we could complicate this

Test:
Finally, the Test screen allows us to create a connection using the connector, test it and see the results in two output text boxes. You have to create a connection and click on ‘Update Connector’ first, then click on ‘Test operation’ to test it:
Custom Connectors: the Test screen
The Connector is used to make the Connection. Then you test the operation of the connection.

Here’s a sample output obtained using the custom connector:

Sample output: a joke has been fetched using the connection.
“Why was the broom late for the meeting? He overswept.”


 
This is the sample JSON that was fetched in the response body:
{
"id": "szItcaFQnjb",
"joke": "Why was the broom late for the meeting? He overswept.",
"status": 200
}

I was also surprised to discover that the request goes through Azure anyway, and uses their authentication for that, even though the API itself doesn’t require any authentication at all:
Test screenshot showing the request URL going through Azure
Ugh, Azure: The blue wall of impenetrable Microsoft hell.


 
The Swagger Editor:
Finally, out of curiosity, this is what the Swagger Editor looks like, though it isn’t required for this task:
The custom connector's swagger editor
The Swagger Editor: another place where the custom connector could be customised

Back to the Index

Using the Custom Connector in Power Apps
The next step was to add the custom connector to a Power App and get it working. To try this out, I went back and adapted the original Microsoft instructions with the ‘Sentiment Analysis’ example that I didn’t have permission to add to Azure. This can be found here: Use a custom connector from a Power Apps app.

I created a new blank app in the default Tablet / Landscape format.

Next, I added a Button to fetch the joke, an HTMLText box to display the results, and added my data connection from the left hand menu, by choosing Data > Add data, then selecting my custom connector from the list of available connectors.

It was worrying to see that actually using custom connectors in Power Apps is a Premium feature, and I didn’t know if we had the kind of plan they require or not:

Adding the custom connector in Power Apps
Adding the custom connector in Power Apps – but will it work..?


 
This was a potential problem, but I decided to see if it worked, first in my developer environment and then in the main university environment.

The plan would be similar to the Microsoft example: clicking the button would activate the function in its OnSelect property to call the REST API through the custom connector, fetch the JSON object containing the joke and store it in a collection. The HTMLText box would then display the text string from the first object in the collection’s ‘joke’ data field.

As a great comparison that makes anything else look like progress, here are those first two fields, doing nothing:
A button that says 'Button', and some text that says 'Show your HTML text here.'
Let’s see if we can improve on this… 🙂

Working from the Microsoft example, I went to the OnSelect property of the button and entered the name of my connector (DadJokes), followed by a full stop (‘.‘), which brought up the name of my GET request function (FetchARandomDadJokeJson) as an autocomplete option. As in the Microsoft example, I used the ClearCollect function to create and / or empty a collection called dadJokesCollection and add the result of the GET request to it. Error messages indicated that it was expecting me to add a parameter in brackets as well, so I tried 0.

On attempting to run this, it gave me the error message that the response data was invalid and it was expecting a valid JSON object:
First attempt at fetching a joke: error messages
Response data should be a valid JSON object… I agree, so why isn’t it?

I discovered a very useful monitor in the App checker, which lists details of the operations carried out while running the app:
The app checker monitor
This looks very promising…

It showed me that my request was returning the HTML for the API’s home page instead of the JSON data:
Monitor screenshot showing the response body data is HTML instead of JSON
The home page HTML: Did not expect that!

This got me wondering if the parameter it was expecting might be the Accept header value ("application/json"), so I tried that and it worked:
Screenshot of the app fetching its first joke.LONG DESCRIPTION
It works! That’s taught me a valuable lesson about assumptions.

The button OnSelect code is now
ClearCollect(dadJokesCollection, DadJokes.FetchARandomDadJokeJson("application/json"))

and the HtmlText box contents is
First(dadJokesCollection).joke

The correct response format in the monitor looks like this:
Monitor screenshot showing correctly formatted JSON in the response body
I like the monitor even better when it works

This could be because I had saved three versions of the GET request with different headers in my Postman collection.
