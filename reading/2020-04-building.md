# Buidling

_Excerpt from ["**Design and Build Great Web APIs**"](http://g.mamund.com/greatwebapis) by Mike Amundsen for Pragmatic Publishers_

In the last two chapters, we covered the work of converting our API design in the form of our ALPS document into sketches and prototypes. All of this preliminary work was done to explore possibilities and lay the groundwork _before_ we actually committed ourselves to the expensive act (both in time and money)  of _writing code_. The good news is that we're done with the preliminaries. It is now time to crack open our code editor and write some NodeJS that results in a fully-functional working API!

The role of Gehry's sketches, which we looked at in [xxx](#chp.sketching.gehry), is to test out ideas quickly. The role of Borglum's prototyping, which we saw in [xxx](#chp.prototyping.borglum), is to more fully explore the ideas you think are valuable to see just what your API might look like once you actually build it. Now, having taken the time to do a bit of exploration, we should have enough feedback from our potential users and enough evidence from our prototyping work to know which design we want to use and how we want to approach it. This feedback and evidence will guide us as we finally work through the process of writing our code and get the API up and running.

[aside info We All Get Here Eventually]
<p>
I suspect some of my readers have been patiently reading along in hopes of reaching this point in the &lquot;API story.&rquot; To all of them, I say your reward is at hand (grin)! I also suspect at least a few of you have skipped over some of the earlier model, design, describe, sketch, and prototype exercises in order to hasten your arrival to this critical point. That's okay. However, I still think you should (eventually) go back to make sure you get the most out of the experience in the design and build phases, too. 
</p>
[/aside]

## Defining the API Build Process
For the purposes of this book, the "build process" is the work of translating all of our design work into source code that actually does the work we set out to do. Sometimes the word "build" is reserved for that portion of coding that _starts_ with checking in written code and involves things like compiling, testing, and deploying the code on a server somewhere. I'll cover all that in the next and final section of the book (The Release Phase). For now, we'll focus on writing code to match our design. 

This is essentially the work Borglum did when he had his team convert his 1/12th-scale Mount Rushmore prototype into a full-fledged monument on the mountain itself. This is a big step. In Borglum's case, that meant using explosives to blow up chunks of the mountain and then months of painstaking work with heavy equipment to carve the details in the mountainside. Once you start on this journey, you're pretty much committed to completing it unless something unexpected happens. 

So our build process will be the work of turning the prototype we created in the previous chapter into our ultimate monument: the Onboarding API for BigCo, Inc.

## Relying on a Repeatable Process {#chp.building.repeatable}
If you only build one API in your life, it's not too important just _how_ you do it---as long as you get it done. However, for those who need to build lots of APIs---those who's job it is to consistently convert designs into functional code---it is valuable to have a consistent, repeatable process for getting the work done.  Just as we covered processes for modeling, designing, describing, sketching, and prototyping APIs, we need to come up with a reliable process for the act of building the actual API.

The main benefit of a repeatable process is that it is something you can show and teach others. A good process is also easily monitored. For example, in the design process, we can easily ask teams things like, "Have you completed your Model yet?" or "Can you show me your ALPS description for this API?" These processes become a kind of shared understanding everyone can rely on when talking about their work. This is especially handy when you have multiple teams in various locations around the world. Even when speaking the same language can be a challenge, the ability to share API design and build processes can make working together easier and more efficient.

With all this in mind, the build process I want to share with you is one that I've been sharing for quite a while. It's a process that covers the main portions of code every API needs deal with. For this book, I've even created a small framework in NodeJS based on the same process. That should make it easier to show you the main concepts and enable us to get our code up and running quickly.

Following is a short walk-through of the build process I use that I call DARRT, and how we'll use it to build working APIs from your design documents with NodeJS.


[aside info What Is DARRT?]
<p>
DARRT is a process for building code from design documents. It stands for Data, Actions, Resources, Representations, and Transitions. I created the DARRT framework as a teaching tool and as a quick way to start lightweight microservice and API projects. The version you are using here is a downsized edition of the one I use in my own projects. This version is not a production-ready implementation. I took lots of shortcuts and left out complicated error-handling code to make sure the routines are easy to read and modify for these lessons. Even though this framework is not recommend for your production use, you can take the lessons you learn here and apply them to whatever framework or programming language you and your team are using.
</p>
[/aside]

## Coding APIs with NodeJS and DARRT {#chp.building.coding}
By relying on a consistent process for building APIs, we can get things done quickly and easily and focus on the work of accurately translating the design into code. And, by using the same process more than once, we can get more skilled at the work and even learn how to modify the process to better fit your own (and your team's) needs in the future. 

The acronym DARRT stands for **D**ata, **A**ctions, **R**esources, **R**epresentations, and **T**ransitions. The first three terms probably look familiar. We've discussed the role of data properties and actions early in the design process, and I pointed out the importance of resources as we worked through our sketches and prototyping. I have not discussed the last two terms (Representations and Transitions) yet, but they are also very important for implementing a successful API. I'll talk about those in detail as we work through the process.

Over the next several pages we'll do the following:

 * Set up our NodeJS DARRT project
 * Define the API's data properties using the `data.js` file
 * Use the `action.js` file to hold the actions our API needs to handle
 * Create the `resource.js` file to expose our HTTP resources and map them to actions
 * Update the `representation.js` file to control how our resource responses are displayed
 * Build up the `transition.js` file to define how API clients can complete desired actions
 
If some of this doesn't quite make sense yet, that's okay. Hopefully, as we work through each step in the process, it will become clear and you'll be able to see the advantage of using the DARRT process for your APIs.

When we're done, we'll have a fully-functional API that we can then test, secure, and deploy in the next section of the book.
 
### Starting a DARRT Project {#chp.building.start}
In this first step, we will initialize the DARRT library in our API project. The DARRT library holds several internal modules that handle the "behind-the-scenes" work along with a set of five key modules we'll use to define and implement the desired API.

First, we need to copy the `/darrt/` folder from the source code[^fn-code] available for this chapter and place it in the root folder of the `onboarding` project we've been working on throughout the book. The DARRT folder (and its contents) looks like this:

[^fn-code]:<https://pragprog.com/titles/maapis/source_code>

<embed file="code/building/darrt-folder-tree.txt" />

We'll be editing only the top-level files in that collection. Everything in the `lib` and `representors` folders are just support routines.

After copying that folder into the root of your project, you also need to initialize the `index.js` file from the `starting-folder` and add that to the root of your project. This will be the file that pulls things all together and launches the NodeJS project at runtime. That file looks like this:

<embed file="code/building/starting-folder/index.js" />

Finally, we need to update the project with supporting packages using the `npm` tool. (See [xxx](#chp.modeling) for more on `npm`.)  Call up a command window, move to the root folder of your project, and run the following commands to update your project packages:

~~~~
npm install -s body-parser
npm install -s cors
npm install -s ejs
npm install -s express
~~~~

These commands will update your `package.js` file with proper information and ensure the project will run as expected.

Now that you have the DARRT framework set up, it's time to modify the existing files to reflect our HTTP API design.

### Data {#chp.building.data}
The first letter in the DARRT framework acronym stands for _data_. The `data.js` file holds all the information about the properties (or "state") the service needs to deal with. In many API implementations, this is handled by a database. But APIs don't really understand data storage; they just understand passing properties back and forth. So, for our implementation, we'll just focus on the state we need to pass back and forth for each request.  We'll rely on the DARRT framework to actually deal with the storage and retrieval of that state.

The `data.js` file includes four small sections:

 * properties
 * requireds
 * enums
 * defaults

 Let's look at properties first.

#### Properties {#chp.building.properties}
The properties collection is a list of state values this API needs to keep track of throughout the lifetime of the service. It's the data we want to pass back and forth. 

For our `onboarding` service, we need to track all the state values needed to support the creation and completion of the work-in-progress (WIP) records we defined in the prototyping stage in [xxx](#chp.prototyping).

Following is the list of properties our `onboardingAPI` will be tracking:

{:language="javascript"}
~~~~
// this service's message properties
exports.props = [
  'id','status','dateCreated','dateUpdated',  
  
  'companyId','companyName','streetAddress','city','stateProvince',
  'postalCode','country','telephone','email',
  
  'accountId','division','spendingLimit','discountPercentage',
  
  'activityId','activityType','dateScheduled','notes'
];
~~~~

I've grouped the state values into related collections. The first group contains values _this_ API will be tracking (unique id, status, and edit dates). The next three property groups relate to the `company`, `account`, and `activity` APIs.

#### Requireds {#chp.building.requireds}
Along with the names of the state values to track, some of them need to be treated as _required_ elements when writing an onboarding record. For this API, the only required values are the `id` and `status` properties:

{:language="javascript"}
~~~~
// required properties
exports.reqd = ['id','status'];
~~~~

Note that each of the related APIs (`company`, `account`, and `activity`) have their own list of required fields. We don't need to worry about them here. That will be handled when we finally resolve the WIP records in the `approve` step.

#### Enums {#chp.building.enums}
A few properties in our API have a limited set of valid values. For example the `status` field should only be set to "pending," "active," "suspended," and "closed." For the `onboarding` API, we have three of these enumerated properties: `status`, `division`, and `activityType`. Following is the snippet of code to add to the `data.js` file that defines this API's set of enumerations:

{:language="javascript"}
~~~~
// enumerated properties
exports.enums = [
  {status:
    ['pending','active','suspended','closed']
  },
  {division:
    ['DryGoods','Hardware','Software','Grocery','Pharmacy','Military']
  },
  {activityType:
    ['email','inperson','phone','letter']
  }
];
~~~~

#### Defaults {#chp.building.defaults}
The last bit of set up we need for our `data.js` file is the list of default values. It's a good idea to implement a set of default values for some of your properties. Doing this makes it easier to create a valid record in the system and offers developers using your API some guidance on typical values for your API properties. 

Following is the list of properties and their defaults for the `onboarding` API:

{:language="javascript"}
~~~~
exports.defs = [
  {name:"spendingLimit", value:"10000"},
  {name:"discountPercentage", value:"10"},
  {name:"activityType", value:"email"},
  {name:"status",value:"pending"}
];
~~~~

That's the contents of the `data.js` file for our `onboarding` API. With that work done (including saving the file to disk and updating the project repository w/ `git`), it is time to move to the next element in our DARRT framework: the `actions.js` module.


### Actions {#chp.building.actions}
The next letter in the DARRT framework acronym stands for _actions_. The `action.js` file in the DARRT framework contains all the actions we defined in our API design. These are the internal functions of our service, not the external endpoints of the API. We documented these actions for our `onboarding` in the initial WSD diagram we created in the previous chapter (see [xxx](#chp.prototyping.translate) and <xref linkend="fig.building.onboarding-http">the figure</xref> as a reminder of what it looked like). Now, we need to take the actions in that diagram and create them in NodeJS code within our project.

Each action is represented by a single function. For example, following is the `listOnboardings` action from our diagram, rendered in NodeJS:

<embed file="code/building/action-listWIP.js" />

As you can see, the function (`listWIP`) executes a call to the `component` module to get a `list` of `onboarding` records.  All `action.js` functions return a NodeJS `Promise` object.

A more interesting example action is the one that allows API clients to write `company` data to the WIP record:

<embed file="code/building/action-writeCompany.js" />

You can see from this code that the function `writeCompany` collects the `id` value from the URL (`id = req.params.id`) and the associated parameters in the message body (`body = req.body`) and, if both are valid, uses the DARRT `component` service to request an update to the WIP record using the declared property settings we created in the `data.js` file (`data.props`, `data.reqd`, and `data.enums`). 

One more action worth viewing is the one for the `readStatus` operation:

<embed file="code/building/action-readStatus.js" />

Notice that, in this function, a new argument called `fields` is used to tell the `component` library which properties to return in the response. This makes it possible to customize the contents of HTTP responses for your API clients.

Check out the project file source code to see the additional functions in the `actions.js` file.[^fn-code2]

[^fn-code2]:<https://pragprog.com/titles/maapis/source_code>


With the `data.js` and `actions.js` files, we have created the _internal_ elements of our API service. Now it is time to focus on the _external_ elements: the exposed resources, response formats, and the request transitions. 

### Resources {#chp.building.resources}
Now it is time to implement all the externally addressable API endpoints for this service. We do that in the `resources.js` file of the DARRT framework. The reference diagram for our external endpoints is the one we created in [xxx](#chp.prototyping). To refresh your memory, it looks like this:

<figure id="fig.building.onboarding-http">
  <imagedata fileref="images/prototyping/onboarding-http-wsd.png" width="100%" />
</figure>

The key elements on this diagram (the "blue pills") are the external endpoints of our HTTP API. This diagram also tells us which HTTP methods are supported for each resource. That's what we need to implement in our `resources.js` file. Not just the endpoint, but also the _methods_ to be supported.

Following is a quick rendering of the `resource.js` file with all the endpoints and methods "stubbed in." There is no code in any of these yet---we'll add it shortly. For now, scan the list of operations and note how they match up to the diagram:

<embed file="code/building/resource-list.js" />

The `resources.js` module replies on a popular NodeJS HTTP library called Express.js.[^fn-building-express] We initialized that library earlier in our `index.js` file (see [xxx](#chp.building.start)) with the lines:

{:language="javascript"}
~~~~
var express = require('express');
var app = express();
var resources = require('./darrt/resources');
...
app.use('/',resources); 
~~~~

[^fn-building-express]:<https://expressjs.com>

The Express library allows us to define functions that respond to a combination of the HTTP method (`router.get`) and the HTTP URL (`wip/:id/status`) that client apps use when making API requests. The list you see in the previous code represents all the resources and all the methods we defined in our diagram in the last chapter. 

Each of these functions gets called when the associated URL and HTTP method are used by a client app. The role of each function is to (1) accept the request parameters (contained in the `req` input argument), (2) process those parameters, and (3) formulate an HTTP response and return it to the called (using the `res` input argument).

One of the functions in the `resources.js` file is shown here:

<embed file="code/building/filter-resource.js" />

This function calls just one internal DARRT method (`respond(args)`). That method takes several values worth reviewing here. The first two (`args.request` and `args.response`) represent the HTTP request and response objects mentioned earlier. The next argument (`args.action = actions.filterWIP`) is the internal action function to be executed for this endpoint. That's the function used to formulate a response to this request. The following string argument (`args.type = "onboarding"`) is used to indicate the type of storage objects that are being dealt with. 

The last parameter is a JavaScript object (`args.config = {...}`) that refers to the contents that will be used in creating the intended HTTP response. This object includes the `metadata` object, which holds some shared data properties; the `templates` object, which holds definitions for the responses; the `forms` collection, which holds a set of links and forms to be included in any responses; and finally, a `filter` object, which helps select the proper forms and templates.

If these last few items are not quite clear, don't worry. We'll get a chance to dig deeper into the contents of this last structured object.

Each of the functions in the `resources.js` file look pretty much the same. You can check out the complete `resources.js` file in the source code associated with this chapter for a complete example.

The three DARRT elements described in the previous sections---Data, Actions, and Resources---represent the complete "loop" of requesting a resource and executing an associated action. The next two DARRT elements (Representations  and Transitions) pertain to the details of how responses will be formulated---how to populate the response and which format to use when returning that response. We'll deal with those two elements next. 

### Representation {#chp.building.representation}
The second 'R' in the DARRT framework acronym stands for _representations_. This refers to the format of the HTTP API responses. HTTP APIs can use a wide variety of formats to represent HTTP responses. The most common response for APIs today is Plain JSON (`application/json`), but there are a number of other formats including those shown in [xxx](#chp.apifirst). 

In the DARRT framework, you can vary the response representations by simply loading one of the preset formats and referencing them when implementing the API. For this book, I've collected a small set of formats. Each of them were implemented using the EJS Templates[^fn-building-ejs] templating engine for NodeJS. You can review these templates in the `darrt/representors/` folder of the book's source code.[^fn-code3] And, if you wish, you can design additional representors and add them to any existing DARRT API.

[^fn-building-ejs]:<https://ejs.co>

[^fn-code3]:<https://pragprog.com/titles/maapis/source_code>


Following is the EJS template for plain JSON responses:

<embed file="code/building/app-json-template.js" />

The version of DARRT used for the project in this book includes EJS templates to support the following response formats:

 * `text/csv`
 * `application/json`
 * `application/links+json`
 * `application/forms+json`
 * `application/prag+json`

You can inspect the EJS templates in the code folder for this chapter.

For the project in this book, you won't need to modify the `representation.js` DARRT file but, for completeness, following is the one included in the project:

<embed file="code/building/load-representors.js" />

You'll see where the EJS templates are loaded and then collected into an array for templates and an array for format names. Both of these arrays are used by the DARRT framework at runtime.

The `representation.js` file controls which formats are available for API responses. The last file in our implementation work---`transitions.js`---defines which operations are available for each resource representation.
  
### Transitions {#chp.building.transitions}
The last item in our DARRT framework setup is the `transitions.js` file. This file holds a set of declarations for _forms_ and _links_ related to the API. An example of a link transition in HTML is the anchor tag:

{:language="html"}
~~~~
<a href="/list/" rel="collection list company">Company List</a>
~~~~

An example of a form transition in HTML is the form tag:

{:language="html"}
~~~~
<form action="/filter/" method="get">
  <input name="search" value="" />
  <input type="submit" />
</form>
~~~~
 
The HTML format has lots of transition elements---elements that describe a way to "transition" from one view to the next. Several API formats support link and form transitions, too. We saw some of them early in the book in [xxx](#chp.apifirst). And, in [xxx](#chp.http), we discussed the role HTML plays in the common practice of the Web. Since one of our goals in creating APIs for this book is to learn from and emulate features of the Web, the DARRT framework was designed to allow developers to declare and use transitions in API responses.

Just as in HTML, adding transitions to API responses informs the API caller of the next possible actions in the API workflow. And, like HTML, well-designed APIs don't have just one possible, fixed sequential set of actions. Instead, users (humans for HTML, machines for APIs) can make their own choices on what steps to take next. 

Following is what a transition looks like in the `transitions.js` file of the DARRT framework (it is similar to the HTML anchor tag we saw earlier):

<embed file="code/building/list-transition.js" />


*Note*---The `{fullhost}` macro in the code snippet gets filled in at runtime with the full host name (`http://onboarding.example.org/wip`). DARRT supports a handful of these types of macros.

Another more interesting transition is the one that describes the details of adding `account` information to a work-in-progress (WIP) record for the onboarding API:

<embed file="code/building/add-account-transition.js" />

The `transition.js` file for the `onboardingAPI` project contains about a dozen transitions. You can view them in detail by checking out the source code associated with this chapter.

[aside info Don't Skip the Transitions]
<p>
Many API developers shortcut their API work by skipping the task of defining transitions for their responses. This happens, in part, because most API libraries make including transitions difficult. If you're using plain JSON or CSV files as your response representation, it is easy to produce an API without any forms or links. But that API is harder to use and more complicated to maintain. 
</p>
<p>
By adopting transition-friendly formats like HAL, Siren, Collection+JSON, and others shown throughout this book, you'll be producing APIs that are easier to use, harder to be misused, and generally more stable over time. Even if your team does not currently use tooling that makes supporting transitions easy, you can help things along by taking lessons learned from the DARRT framework and incorporating them into the tooling you and your team use today.
</p>
[/aside]

Now that we have reviewed all the individual parts of the DARRT framework (Data, Actions, Resources, Representations, and Transitions), it is time to test out our onboarding API to see how it all comes together.
 
## Putting It All Together {#chp.building.together}
Now that we've seen how the DARRT framework is put together, we can run some simple queries against our instance of the `onboardingAPI` to see how it behaves. To do that we need to start up our local instance of the `onboardingAPI` and then use `curl` to execute some simple tests.

First, to start the local instance of the `onboadingAPI`, open a command window and move to the `/building/all-together/` folder in the source code download associated with this chapter and run the following command:

~~~~
npm run dev
~~~~

This command will fire up the `onboardingAPI` service and wait for requests. Note that this command assumes you already have a NodeJS utility called `nodemon`[^fn-nodemon] installed on your machine. If you don't have this handy tool installed, you can execute the following command: `npm install -g nodemon`.

[^fn-nodemon]:<https://nodemon.io>

The results of this one command should look something like this:

<embed file="code/building/test-nodemon.txt" />

Now, to run some queries against our `onboardingAPI`, you  need to open another command window. This is where we'll use `curl` to send some requests to the API to inspect its responses.

To start, let's check out the root of the API service and see what is returned. Type `curl localhost:8080` at the command line and view the response:

<embed file="code/building/test-onboarding-01.txt" />

This is the root resource that is pointing us to the `list` resource. Note the format is returned in plain JSON---the default for this API. We can alter the response format by passing the HTTP `accept` header like this:

<embed file="code/building/test-onboarding-02.txt" />

The `application/forms+json` representation contains additional information including not just `href` values, but methods, descriptions, and when appropriate, request parameters (using the `properties[]` collection). I like programming against these types of responses since I get lots of important information without having to read through extensive documentation. The good news is, when using a framework that supports varying representations and transitions (like DARRT does), developers can decide for themselves which response format they prefer to work with.

Now that we've seen the basics of how the `onboardingAPI` behaves, we can put together a set of simple tests to exercise the API and explore some options.

The original workflow we put together in the design phase of our API work (see [xxx](#chp.design.resources))  looked something like this:

<author>TK: update to *companyData* not *customerData*</author>

<figure id="building.wsd-resource-complete">
 <imagedata fileref="images/designing/wsd-resource-complete_new.png" width="100%" />
</figure>

And that ended up as the HTTP implementation workflow we saw earlier in this chapter in [xxx](#chp.building.resources).

That last implementation diagram is what we used to code our `onboardingAPI` here in this chapter. So, it follows that we could do a simple test of our interface by running a set of HTTP requests using the `curl` tool we looked at in [xxx](#chp.apifirst.exploring). 

I usually put together a short command-line script that tests what I call the "happy path" of the API. If all goes well and the API is used exactly as intended, I should be happy with the results. So, here's a peek at part of the script I put together to test my `onboardingAPI` "happy path":

<embed file="code/building/happy-script-01.txt" />

This snippet shows the command to pull the list of WIP records and the command that creates a new WIP onboarding record. Notice that I'm using two utilities in this script: the `curl` utility we talked about earlier in the book and the `jq` utility. The `jq` utility is just a nice way to format JSON output and is not required. If you don't have `jq` installed, you can modify the script included with this book.

[aside info Are We Testing Now?]
<p>
  This &lquot;happy path&rquot; script is a super simple way to validate the API. But it does not really qualify as testing the API. We'll go over a much more extensive approach to API testing in the next chapter. <!--(<ref linkend="chp.testing" />)-->
</p>
[/aside]

Another thing you might notice in the script snippet is my use of variables (`$acc` and `$wid`). I have a full set of script variables for the server connection as well as all the test data to be written to the API. Here's what that looks like in the script:

<embed file="code/building/happy-script-02.txt" />

I won't spend more time on this script here. You can check it out in the source code folder for this book (`code/building/happy-path/happy-path.sh`) as well as some sample output (`code/building/happy-path/2020-02-03-output.txt`) from a run of the script. 

## What's Next? {#chp.building.next}
In this chapter, we focused on translating the final API prototype into an actual working implementation of the `onboardingAPI`. To do that, we used a simple API library called DARRT that was built with NodeJS and the Express web framework and the EJS template library. 

The DARRT library leads developers to translate designs into five parts: Data, Actions, Resources, Representations, and Transitions. The first three parts are common in all Web API approaches; the last two parts are added to improve the quality of the implementation and especially to support the use of links and forms. And links and forms make up the distinguishing elements of APIs built using the principles of Web development we looked at in [xxx](#chp.apifirst).

This chapter wraps the Build Phase of the book (Sketch, Prototype, and Build). Starting with the next chapter, we move into the Release Phase. There we'll cover testing, securing, and deploying your working API. We'll also take a look at _modifying_ your API after it has been released into production.

## Chapter Exercise {#chp.building.exercise}
For this chapter's exercise, you get to finally _code your API_. That means you'll be using the design assets from previous chapters along with NodeJS and the DARRT library to implement a working version of the `credit-check` API.

Hopefully, the task list for implementing your API will look familiar.

First, you will copy the DARRT library into your NodeJS project. Next, you can use information from your ALPS document and WSD diagram to update the DARRT library files (data, actions, resources, representations, and transitions). And, finally, you can fire up the completed `credit-check` service and run your validator script to ensure the service at least supports your intended "happy path" workflow.

It might seem like quite a bit to accomplish, but if all goes well, you should be able to complete this exercise in less than thirty minutes. 

### Install the DARRT Library
For this exercise, the `/before/` folder has the DARRT library already installed. I did that so you could have some files already configured instead of having to start from zero. However, you will need to use `npm` to update dependencies in the project and then run the API service in "monitor" mode. We covered that earlier in the chapter.

[aside info Using DARRT in the Future]
<p>
For future projects, you'll find a fresh copy the DARRT library in the <inlinecode>code/building/starting-folder</inlinecode> section of the source code package associated with the book. You can copy that (along with the <inlinecode>index.js</inlinecode> file in that same folder) to the root of your NodeJS API projects whenever you want to get a quick start to your API implementation.
</p>
[/aside]

### Implement Your API
You'll need to use design documents from your project's `assets` folder as guides as you implement your API. I usually rely on my resource diagram (`credit-check-http.png`) and my ALPS profile (`credit-check-alps.xml` or `credit-check-alps.json`) to help me fill in the `data.js`, `actions.js`, `resources.js`, and the `transitions.js` files. 

For this exercise, you won't need to update the `representations.js` since it has already been set up for all the output formats needed for this project. You'll also notice that the `data.js` file has been set up with the common basic data properties (`id`, `status`, and `dateCreated`, `dateUpdated`). You'll only need to add the fields defined by your `credit-check` design. The `actions.js`, `resources.js`, and `transitions.js` files will need to be updated to match your design specs.

### Validate Your API's Workflow 
Finally, after updating your DARRT files, your API should be ready for validation. For this exercise, you can run the `validate.sh` script in the `tests` folder of the project on disk.  

See Appendix 2 (<ref linkend="ax-solutions-building" />) for the solution.

