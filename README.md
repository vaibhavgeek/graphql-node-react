We will build a client-sided GraphQL application which will combine React with GraphQL. The application would allow us to gather facebook profile information 

The following tutorial will be split into four sections. 

- What is GraphQL and how is it different from REST API.
- What is Reactjs and Why should you use it?
- Creating a GraphQL backend in nodejs and express. 
- Connecting Apollo Client to a react application 

  
## What is GraphQL and how is it different from REST API? 
One might ask themselves that they already understand the REST APIs very well and if there is a true need of GraphQL at all. GraphQL is based on objects instead of strings (comparing to SQL) and thus  reduces complexity of the application. 

All the requests in GraphQL are handled by a single a end-point ``/graphql``. Let that sink in for a while. 

Why does this matter? The actual transmission of requests and data is abstracted away. We no longer have to worry about things like response codes and planning out our urls like
``/articles/id/comments/user_id/comment_id/delete ``. 
In the HTTP API structure, we had to create 4 API to interact with our single object in model which were largely, 

 - post
 - get
 - delete
 - put
 
whereas GraphQL would have handler functions which would handle things for us.  

To summarize it we have following advantages of GraphQL. 
**GraphQL is declarative**: Query responses are decided by the client rather than the server. A GraphQL query returns exactly what a client asks for and no more.

**GraphQL is compositional**: A GraphQL query itself is a hierarchical set of fields. The query is shaped just like the data it returns. It is a natural way for product engineers to describe data requirements.

**GraphQL is strongly-typed**: A GraphQL query can be ensured to be valid within a GraphQL type system at development time allowing the server to make guarantees about the response. This makes it easier to build high-quality client tools
  
GraphQL fits in very well with abstraction based concept on which facebook devoloper tools function. The idea that abstraction reduces complexity is common across React, GraphQL and other devoloper tools that facebook has created and thus it workds seamlessly with ReactJS. 

## What is Reactjs and Why should you use it with GraphQL?


The number of front-end libraries built on top javascript has clearly surpassed a guessable number. This has led it to be the butt of many jokes. There is a popular drinking joke amongst developers where a person names a noun and if there exists a js library with such a name, you are supposed to drink

A new library called ReactJS somehow has emerged as the uncrowned champion amidst this bubble. It has gained massive popularity amongst management and developers alike. The best part about react js is that to get started you only need a basic understanding of HTML and Javascript. Not only is it easy to learn but also easy to migrate as it is not a framework but a library with small compatibility layers In this article, we will talk about why you should migrate to ReactJS and list few of the tech companies that have done the same.

1.  SuperSonic Load  
ReactJs uses Virtual Dynamic Object Model (V-DOM) which is built with a diff [algorithm](https://reactjs.org/docs/reconciliation.html) (A tree of elements hierarchy is created, it is then calculated as to what is the fastest way to change the tree in order to get desired results). The basic idea behind V-DOM is that the changes are made in virtual DOM with pure javascript as compared to live DOM. Only the minimal changes that are needed to be made to page happen. So both the data bindings and changes to page content is minimalistic.  
      
    
2.  Easy to Build  
As mentioned in the first paragraph, developers are already familiar with Javascript so it is easy for them to get hands-on coding with ReactJS. This combined with the fact that the complete ReactJS framework is built on components thus making it easier to do A/B testing. For example, we can have an A/B test that compares 9 different design variations in the UI, which could mean maintaining code for 10 views for the duration of the test. This modular aspect makes it easy to test and reuse the same components in multiple places across devices.  
      

3.  Security  
With technologies coming and going so haphazardly, the need for a stable technology is what organizations are looking for. And, ReactJS is just that, since it is backed by big names like Facebook, Instagram and millions of developers globally. Even, with peer competition increasing, ReactJS has been able to sustain its popularity level and in fact, has been soaring high still. Hence, developers find security and stability in opting for React.  
      
    
4.  Great Runtime Performance  
To build the best visually-rich user experience for the webapp, efficient UI rendering is critical. While there are fewer hardware constraints on desktops/laptops (compared to other devices), expensive operations can still compromise UI responsiveness. In particular, DOM manipulations that result in reflows and repaints are especially detrimental to user experience. ReactJS comes with great runtime experience for the user.  
      
    
5.  Isomorphic Javascript  
React enables developers to build JavaScript UI code that can be executed in both server (e.g. Node.js) and client contexts. React allows the basic back-end to load whereas the client context can be loaded over time without the live DOM, thus reducing the loading time by a huge margin. Furthermore, the UI code written using the React library that is responsible for generating the markup could be shared with the client to handle cases where re-rendering was necessary.

## Creating a GraphQL backend in nodejs and express. 

We will first create a node directory that contains our backend. We will initialize using the command `` $ npm init `` in our terminal and initialize the npm repository.  Furthermore create a new server.js file in the project directory. That will be the file where the code required to implement the Node.js GraphQL server will be inserted in the next section:

`$ touch server.js`

Finally make sure that NPM packages  _graphql_,  _express_ and  _express-graphql_  are added to the project:

`$ npm install graphql express express-graphql --save`

Having installed these packages successfully we’re now ready to implement a first GraphQL server.

Then we will create our server.js file and copy the following code. 
```
var express = require('express');
var express_graphql = require('express-graphql');
var {buildSchema} = require('graphql');
// we require all dependencies
// GraphQL Schema
// We are passing the GraphQL Interface Definition Language which is used to define the schema 
// It defines how the schema of our object is going to look like. 
var schema = buildSchema(`
    type Query {
        message: String
    }
`);

// Root resolver
// Root resolver has mapping to actions to functions. This is also called as handler functions.
var root = {
    message: () => 'Hello Wold!'
};

// Create an expres server and a GraphQL endpoint, notice that there is only one end point as we stated before
var app = express();
app.use('/graphql', express_graphql({
    schema: schema,
    rootValue: root,
    graphiql: true
}));

app.listen(4000, () => console.log('Express GraphQL Server Now Running On localhost:4000/graphql'));
```
The code has been heavily commented so that you can easily understand what each and every line means. There are two main components here to focus on

 - GraphQL Schema 
 - Root Resolver 

**GraphQL schema** 
It is used to describe the complete APIs type system. It includes the complete set of data and defines how a client can access that data. Each time the client makes an API call, the call is validated against the schema. Only if the validation is successful the action is executed. Otherwise an error is returned.

**Root Resolver**
Resolver contains the mapping of actions to functions. In our example from above the root resolver contains only one action: message. To keep things easy the assigned functions just returns the string _Hello World!_. Later on you’ll learn how to include multiple actions and assign different resolver functions.

At the end of code we create and express end-point where these queries can be consumed. 

Next we will move on to our actual code and a more sophisticated example. You can create a new file and and copy paste the following code in it. 
```
var express = require('express');
var express_graphql = require('express-graphql');
var {buildSchema} = require('graphql');

// GraphQL Schema
var schema = buildSchema(`
    type Query {
        course(id: Int!): Course
        courses(topic: String): [Course]
    }
    type Mutation {
        updateCourseTopic(id: Int!, topic: String!): Course
    }
    type Course {
        id: Int
        title: String
        author: String
        description: String
        topic: String
        url: String
    }
`);

var coursesData = [
    {
        id: 1,
        title: 'The Complete Node.js Developer Course',
        author: 'Andrew Mead, Rob Percival',
        description: 'Learn Node.js by building real-world applications with Node, Express, MongoDB, Mocha, and more!',
        topic: 'Node.js',
        url: 'https://codingthesmartway.com/courses/nodejs/'
    },
    {
        id: 2,
        title: 'Node.js, Express & MongoDB Dev to Deployment',
        author: 'Brad Traversy',
        description: 'Learn by example building & deploying real-world Node.js applications from absolute scratch',
        topic: 'Node.js',
        url: 'https://codingthesmartway.com/courses/nodejs-express-mongodb/'
    },
    {
        id: 3,
        title: 'JavaScript: Understanding The Weird Parts',
        author: 'Anthony Alicea',
        description: 'An advanced JavaScript course for everyone! Scope, closures, prototypes, this, build your own framework, and more.',
        topic: 'JavaScript',
        url: 'https://codingthesmartway.com/courses/understand-javascript/'
    }
]

var getCourse = function(args) {
    var id = args.id;
    return coursesData.filter(course => {
        return course.id == id;
    })[0];
}

var getCourses = function(args) {
    if (args.topic) {
        var topic = args.topic;
        return coursesData.filter(course => course.topic === topic);
    } else {
        return coursesData;
    }
}

var updateCourseTopic = function({id, topic}) {
    coursesData.map(course => {
        if (course.id === id) {
            course.topic = topic;
            return course;
        }
    });
    return coursesData.filter(course => course.id === id)[0];
}

// Root resolver
var root = {
    course: getCourse,
    courses: getCourses,
    updateCourseTopic: updateCourseTopic
};

// Create an expres server and a GraphQL endpoint
var app = express();
app.use('/graphql', express_graphql({
    schema: schema,
    rootValue: root,
    graphiql: true
}));

app.listen(4000, () => console.log('Express GraphQL Server Now Running On localhost:4000/graphql'));
```
 We write GraphQL query to fetch us the required results. You can explore this using the ``http://localhost:4000/graphql`` server. This code helps you to get a course, get courses and update a course Topic. 

## Connecting Apollo Client to a react application 

We use the create-react-app to get us started with our project. 
```
$ npx create-react-app react-graphql-frontend 
$ cd react-graphql  
$ npm start
```
The following packages needs to be installed:

-   _apollo-boost_: Package containing everything you need to set up Apollo Client
-   _react-apollo_: View layer integration for React
-   _graphql-tag_: Necessary for parsing your GraphQL queries
-   _graphql_: Also parses your GraphQL queries

The installation of these dependencies can be done by using the following NPM command:

`$ npm install apollo-boost react-apollo graphql-tag graphql`

The main entry point of the React application can be found in _index.js_. The code contained in this file is making sure that _App_ component is rendered out to the DOM element with ID root.Here is how the file should look. 
### index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';


ReactDOM.render(<App />, document.getElementById('root'));
```
### App.js
The main logic of our code is present in App.js. We need to do the following tasks. 

 - Creating An Instance Of ApolloClient 
 - Connecting ApolloClient To Your React App
 
**Creating An Instance of ApolloClient**
First  _ApolloClient_  is imported from the  _apollo-boost_  library. A new instance of  _ApolloClient_  is created and stored in  _client_. To create a new instance of  _ApolloClient_  you need to pass a configuration object to the constructor. This object must contain the  _uri_  property. The value of this property must be replaced which the URI of the GraphQL endpoint which should be accessed.
```
import ApolloClient from "apollo-boost";
const client = new ApolloClient({  
  uri: "[Insert URI of GraphQL endpoint]"  
});
```

**Connecting ApolloClient To your react App**
Having established a connection to the GraphQL endpoint by using  _ApolloClient_  we now need to connect the instance of  _ApolloClient_  to the React app. To do so, please make sure to add the following lines of code in  _App.js_:
```
import { ApolloProvider } from "react-apollo";...const App = () => (  
 <ApolloProvider client={client}>
    <div className="container">
      <nav className="navbar navbar-dark bg-primary">
        <a className="navbar-brand" href="#">React and GraphQL - Sample Application</a>
      </nav>
      <div>
        <Courses />
      </div>
    </div>
  </ApolloProvider>
);
```
Fist _ApolloProvide_ is imported from the _react-apollo_ library. The `<ApolloProvider>` element is then used in the component's JSX code and is containing the template code which is used to render the component. This is what the final version of App.js would look like. 

App.js
```
import React, { Component } from 'react';
import './App.css';
import Courses from './Courses';

import ApolloClient from "apollo-boost";
import { ApolloProvider } from "react-apollo";


const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
});


const App = () => (
  <ApolloProvider client={client}>
    <div className="container">
      <nav className="navbar navbar-dark bg-primary">
        <a className="navbar-brand" href="#">React and GraphQL - Sample Application</a>
      </nav>
      <div>
        <Courses />
      </div>
    </div>
  </ApolloProvider>
);
export default App;
```

### Course.js
So far the output of courses is done within the  _Courses_  component. In the next step we’re going to introduce a new component to our project:  _Course_. This component should then contain the code which is needed to output a single course. Once this component is available in can be used in  _Courses_  component.

First let’s add a new file  _Course.js_  to the project and insert the following lines of code:.
```
import React from 'react';
const Course = (props) => (
    <div className="card" style={{'width': '100%', 'marginTop': '10px'}}>
        <div className="card-body">
        <h5 className="card-title">{props.course.title}</h5>
        <h6 className="card-subtitle mb-2 text-muted">by {props.course.author}</h6>
        <p className="card-text">{props.course.description}</p>
        <a href={props.course.url} className="card-link">Go to course ...</a>
        </div>
    </div>
);
export default Course;
```
The implementation of this component is quite simple. The current course is handed over to  _Course_  component as a property and is available via  _props.course_.
### courses.js
Next we create a new courses component and copy paste the following in it. 
courses.js

```
import React from 'react';
import { Query } from "react-apollo";
import Course from './Course';
import gql from "graphql-tag";


// The Query component makes it extremely easy to embed the GraphQL query directly in the JSX code of the component. 
// Furthermore the Query component contains a callback method which is invoked once the GraphQL query is executed.

const Courses = () => (
  <Query
    query={gql`
      {
        allCourses {
          id
          title
          author
          description
          topic
          url
        }
      }
    `}
  >
    {({ loading, error, data }) => {
      if (loading) return <p>Loading...</p>;
      if (error) return <p>Error :(</p>;
    
        return data.allCourses.map((currentCourse) => (
            <Course course={currentCourse} />
        ));
    }}
  </Query>
);

export default Courses;
```
This is the implementation of the Courses component. To retrieve data from the GraphQL endpoint this component makes use of another component from the React Apollo library:  _Query_. The  _Query_ component makes it extremely easy to embed the GraphQL query directly in the JSX code of the component. Furthermore the  _Query_ component contains a callback method which is invoked once the GraphQL query is executed.

Here we’re using the JavaScript map method to generate the HTML output for every single course record which is available in  _data.allCourse_.

## Conclusion 
Hey! All this is cool but how does one decide which tech stack to use. The question has been answered several times and by several ingenious folks and there is no correct answer. It truly depends on use-case, complexity and scalability requirements for the application. Often at time you would have to evolve and change multiple things for your application. It is important to note that these stacks work perfectly well in tandem with each other. So, you can very well have Rails backend serving you a GraphQL along with React front-end. We know Rails is very good at handling back-end logic and we also know the React's ability to handle front-end which gives us an amazing combination. 
