# Issue-Tracking
Tracking issues

STEP 1: Generate an access token.
-Open https://github.com/settings/tokens
-Then select the public-repo scope
-Copy somewhere safe because we will need it later


STEP 2: Create a new React App
- we will use react to build our application
- run this command in command line
- npm init react-app react-graphql-client.


DEPENDENCIES
- @material-ui as a component library
- apollo-boost,apollo/react-hooks, graphiql- for GraphQL communication:

npm i--save @material-ui/core @material-ui/icons apollo-boost @apollo/react-hooks graphql

STEP 3: Secure the access token.
- Create an .env file in the project root and put this line in it:
REACT_APP_GITHUB_KEY= your key goes here
-This will make the REACT_APP_GITHUB_KEY be injected as an environment variable into the app
available as process.env.REACT_APP_GITHUB_KEY
-Make sure you don't commit this file 

STEP4: Download the Schema

-The advantages of GraphQL is syntax highlighting and strict typing.
-To enable syntax highlighting in your IDE create a file called . config and put the content below:

{
"name":"Github Schema",
"schemaPath":"./schema.graphql",
"extentions":{
"endpoints":{
"Github GraphQl Endpoint":{
"url":"https://api.github.com/graphql",
"headers":{
"Authorization":"bearer YOUR TOKEN HERE"
}
"introspect":true
}
}
}
}

-To make use of this file you will need to install an extension.
-If you are using Webstorm use JS GraphQL
-For Visual Studio code use GraphQL
After installation you will be prompt to download the Schema defined in the . graphqlconfig
- Important Note: you will need your API token to access the Schema which means you should not commit .graphqlconfig to VCS as well.
