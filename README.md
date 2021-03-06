# Issue-Tracking
<b>Tracking issues</b> <br>

We’ll build a React app that looks up a repository by its name and displays information about it including issues . 




<h4> STEP 1: Generate an access token.</h4>
<ul>
<li>Open https://github.com/settings/tokens </li>
<li>Then select the public-repo scope</li>
<li>Copy somewhere safe because we will need it later</li>
</ul>

<h4> STEP 2: Create a new React App </h4>
<ul>
<li> we will use react to build our application</li>
<li>- run this command in command line</li>
<li><b>npm init react-app react-graphql-client.</b></li>
</ul>

<h5> DEPENDENCIES </h5>
<ul>
<li> <i>@material-ui</i> as a component library</li>
<li><i>apollo-boost,apollo/react-hooks,graphiql -for GraphQL communication:</li>

<li><i><b>npm i--save @material-ui/core @material-ui/icons apollo-boost @apollo/react-hooks graphql</b> <i> </li>
</ul>
<h4> STEP 3: Secure the access token.</h4>
<ul>
<li>Create an <i>.env file </i>in the project root and put this line in it:</li>
<li>REACT_APP_GITHUB_KEY= your key goes here</li>
<li>This will make the REACT_APP_GITHUB_KEY be injected as an environment variable into the app
available as<b> process.env.REACT_APP_GITHUB_KEY</b></li>
<li>-Make sure you don't commit this file </li>
</ul>
<h4> STEP4: Download the Schema </h4>
<ul>
<li>The advantages of GraphQL is syntax highlighting and strict typing.</li>
<li>To enable syntax highlighting in your IDE create a file called  .<i>config</i> and put the content below:</li>
</ul>
<code>


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

</code>
<ul>
<li>To make use of this file you will need to install an extension.
<li>If you are using Webstorm use <b>JS GraphQL</b></li>
<li>-For Visual Studio code use <b>GraphQL</b>
<br>After installation you will be prompt to download the Schema defined in the . graphqlconfig</li>
<li> Important Note: you will need your API token to access the Schema which means you should not commit .graphqlconfig to VCS as well.</li>
</ul>

<h4> STEP 5:Create a search bar </h4>

- Our app will have a title, a search bar,to search for repos and a list of repos and a funtionality where clicking a repo expands, it shows you the last 20 open issues.



<code>
import React from 'react';

	import {TextField, InputAdornment, makeStyles} from "@material-ui/core";

	import SearchIcon from '@material-ui/icons/Search';

	

	const useStyles = makeStyles({
	  input: {
	    width: '100%'
	  }
	});
	

	const SearchBar = ({value, onChange}) => {
	  const classes = useStyles();
	

	  return (
	    <TextField
	      className={classes.input}
	      label='Search for repos...'
	      type='search'
	      variant='outlined'
	      InputProps={{
	        endAdornment: (
	          <InputAdornment position='end'>
	            <SearchIcon />
	          </InputAdornment>
	        ),
	      }}
	      value={value}
	      onChange={e => onChange(e.target.value)}
	    />
	  );
	};
	

	export default SearchBar;
</code>

-In this component, we render a simple text input with a search icon and some styling.


<h5> STEP 6: Creating a repository list.</h5>

- We have a search bar let's create a repository list.

<code>
import React from 'react';
	import {Typography, makeStyles} from "@material-ui/core";
	

	const useStyles = makeStyles({
	  noRepoNote: {
	    marginTop: '1rem',
	    textAlign: 'center',
	  }
	});
	

	const RepositoryList = ({searchTerm}) => {
	  const classes = useStyles();
	

	  return (
	    <Typography 
	      variant={'overline'} 
	      className={classes.noRepoNote} 
	      component={'div'}
	    >
	      No repositories yet.
	    </Typography>
	  );
	};
	

	export default RepositoryList;

</code>
<br>- We don't have the actual data yet, we let the use know there are no repositories at the moment.
<br>- Now open the app.js and edit it like this :


<code>

import React from 'react';

	import {Typography, Container, makeStyles} from "@material-ui/core";
	import RepositoryList from "./RepositoryList";
	import SearchBar from "./SearchBar";
	

	const useStyles = makeStyles({
	  title: {
	    marginTop: '1rem',
	    marginBottom: '1rem',
	    textAlign: 'center'
	  }
	});
	

	const App = () => {
	  const classes = useStyles();
	  return (
	    <Container maxWidth={'sm'}>
	      <Typography variant={'h3'} className={classes.title}>GraphQL Github Client</Typography>
	      <SearchBar/>
	      <RepositoryList/>
	    </Container>
	  );
	};
	

	export default App;

</code>


- We just render a title and our two components. To open the app, run npm start from the terminal in the project root.



<h5> STEP 7: Set up Apollo</h5>

- Apollo is a client library for GraphQL which provides bindings for React.
We will create a file called client.js below:


<code>
import ApolloClient from 'apollo-boost';
	

	const client = new ApolloClient({
	  uri: 'https://api.github.com/graphql',
	  headers: {
	    Authorization: `bearer ${process.env.REACT_APP_GITHUB_KEY}`
	  }
	});
	

	export default client;

</code>

-The code will create an Apollo client that connects to Github API. We used the environmental variable to pull the API token . Because of that, this file is 100% safe to commit to VCS.
<br>- We will now use this client with a provider in App.js:


<code>
import React from 'react';
	import { ApolloProvider } from '@apollo/react-hooks';
	import client from './client';
	import {Typography, Container, makeStyles} from "@material-ui/core";
	import RepositoryList from "./RepositoryList";
	import SearchBar from "./SearchBar";
	

	const useStyles = makeStyles({
	  title: {
	    marginTop: '1rem',
	    marginBottom: '1rem',
	    textAlign: 'center'
	  }
	});
	

	const App = () => {
	  const classes = useStyles();
	  return (
	    <ApolloProvider client={client}>
	      <Container maxWidth={'sm'}>
	        <Typography variant={'h3'} className={classes.title}>GraphQL Github Client</Typography>
	        <SearchBar />
	        <RepositoryList />
	      </Container>
	    </ApolloProvider>
	  );
	};
	

	export default App;

</code>

<h5> STEP 8: Query Repositories </h5>

To do this we need a new dependency <b> use-debounce </b>.
- This solves the problem of debouncing, making sure you do not call the API on every keystroke, which would be inefficient, but limiting it to some timespan.

<br>To install it, run this command in the project root:
<label>npm i --save use-debounce</label>

<br>- We need to acess the search term from the SearchBox and pass it to the RepositoryList. This is done in App.js:



<code>
import React, {useState} from 'react';

	import { ApolloProvider } from '@apollo/react-hooks';
	import client from './client';
	import {Typography, Container, makeStyles} from "@material-ui/core";
	import RepositoryList from "./RepositoryList";
	import SearchBar from "./SearchBar";
	

	const useStyles = makeStyles({
	  title: {
	    marginTop: '1rem',
	    marginBottom: '1rem',
	    textAlign: 'center'
	  }
	});
	

	const App = () => {
	  const classes = useStyles();
	  const [searchTerm, setSearchTerm] = useState('');
	  return (
	    <ApolloProvider client={client}>
	      <Container maxWidth={'sm'}>
	        <Typography variant={'h3'} className={classes.title}>GraphQL Github Client</Typography>
	        <SearchBar value={searchTerm} onChange={setSearchTerm}/>
	        <RepositoryList searchTerm={searchTerm}/>
	      </Container>
	    </ApolloProvider>
	  );
	};
	

	export default App;


</code>


- Using the accessed term, the RepositoryList can perform queries. We need to update RepositoryList.js like this:




<code>
import {useQuery} from "@apollo/react-hooks";

	import React from 'react';
	import {Typography, makeStyles, CircularProgress} from "@material-ui/core";
	import {useDebounce} from "use-debounce";
	import {SEARCH_FOR_REPOS} from "./queries";
	

	const useStyles = makeStyles({
	  note: {
	    marginTop: '1rem',
	    textAlign: 'center',
	  },
	  spinnerContainer: {
	    display: 'flex',
	    justifyContent: 'space-around',
	    marginTop: '1rem'
	  }
	});
	

	const RepositoryList = ({searchTerm}) => {
	  const classes = useStyles();
	  const [debouncedSearchTerm] = useDebounce(searchTerm, 1000);
	  const {data, loading, error} = useQuery(SEARCH_FOR_REPOS,
	    {variables: {search_term: debouncedSearchTerm}}
	    );
	

	  if (loading) {
	    return (
	      <div className={classes.spinnerContainer}>
	        <CircularProgress />
	      </div>
	    );
	  }
	

	  if (error) {
	    return (
	      <Typography
	        variant={'overline'}
	        className={classes.note}
	        component={'div'}
	        color={'error'}
	      >
	        {error}
	      </Typography>
	    )
	  }
	

	  if (!data.search.repositoryCount) {
	    return (
	      <Typography
	        variant={'overline'}
	        className={classes.note}
	        component={'div'}
	      >
	        There are no such repositories!
	      </Typography>
	    )
	  }
	

	  return (
	    <Typography
	      variant={'overline'}
	      className={classes.note}
	      component={'div'}
	    >
	      Some repositories!
	    </Typography>
	  );
	};
	

	export default RepositoryList;



</code>
- As you can see the the use of <b>useDebounce</b> 
    <br>- It accepts two arguments: the value we want to debounce and the time interval. 
<br>It returns a value that will be updated at most every one second.

<br>- We use the <b>useQuery<b> hook to get data from the Github API. It accepts our SEARCH_FOR_REPOS query, defined in step four, and an object with options.
<br>- In this case, we need to pass arguments to the query, the <b>search_term.</b> 
<br>- <b>useQuery<b> will re-run the query when these change, so we do not have to worry about it.

<b>useQuery</b> returns an object with many fields, but we’re interested in data, loading, and error. 

<br>-We can explore the data structure by logging data to the console, using the <b>Github API Explorer</b>, or installing the <b>Apollo DevTools extension</b>.




<h5> STEP 9: Rendering repository information. </h5>


- Now we have the data and are able to render it. For each repository, we want to show its name, owner, and description. To do so, we will need a new component, <b>Repository.</b> Create it in <b>Repository.js</b>:



<code>

import React from 'react';

	import {
	  ExpansionPanel,
	  ExpansionPanelSummary,
	  ExpansionPanelDetails,
	  Typography,
	  Chip,
	  makeStyles
	} from "@material-ui/core";
	import StarIcon from '@material-ui/icons/Star';
	import PeopleIcon from '@material-ui/icons/People';
	

	const useStyles = makeStyles({
	  root: {
	    marginTop: '1rem'
	  },
	  summaryContainer: {
	    flexDirection: 'column',
	  },
	  summaryHeader: {
	    display: 'flex',
	    flexDirection: 'row',
	    justifyContent: 'space-between',
	    marginBottom: '1rem'
	  },
	  chip: {
	    marginLeft: '0.5rem'
	  }
	});
	

	const Repository = ({repo, expanded, onToggled}) => {
	  const {node: {name, descriptionHTML, owner: {login}, stargazers: {totalCount: totalStarCount}}} = repo;
	  const classes = useStyles();
	  return (
	    <ExpansionPanel
	      expanded={expanded}
	      onChange={onToggled}
	      className={classes.root}
	    >
	      <ExpansionPanelSummary classes={{content: classes.summaryContainer}}>
	        <div className={classes.summaryHeader}>
	          <Typography variant={'h6'}>{name}</Typography>
	          <div>
	            <Chip label={`by ${login}`} avatar={<PeopleIcon/>} className={classes.chip}/>
	            <Chip label={totalStarCount} avatar={<StarIcon/>} className={classes.chip}/>
	          </div>
	        </div>
	        <Typography
	          variant={'caption'}
	          dangerouslySetInnerHTML={{__html: descriptionHTML}}
	          component={'div'}
	        />
	      </ExpansionPanelSummary>
	      <ExpansionPanelDetails>
	        Issues here!
	      </ExpansionPanelDetails>
	    </ExpansionPanel>
	  );
	};
	

	export default Repository;


</code>


This component accepts props repo, expanded and onToggled. repo is the object with repository data, expanded is true if this component is expanded (to view issues), and onToggled is a callback that will be fired on expanding or shrinking the component.

<br>-you can see how repo is unpacked into <i>name</i>, descriptionHTML, login, and totalStarCount. These are later used to render the info. Since <b>descriptionHTML</b> is an HTML string, we use dangerouslySetInnerHTML to render it.

<br>- To display the Repository component, we need to make some adjustments to the RepositoryList in RepositoryList.js:





<code>

import React, {useState, useEffect} from 'react';
	import {useQuery} from "@apollo/react-hooks";
	import {Typography, makeStyles, CircularProgress} from "@material-ui/core";
	import {useDebounce} from "use-debounce";
	import {SEARCH_FOR_REPOS} from "./queries";
	import Repository from "./Repository";
	

	const useStyles = makeStyles({
	  note: {
	    marginTop: '1rem',
	    textAlign: 'center',
	  },
	  spinnerContainer: {
	    display: 'flex',
	    justifyContent: 'space-around',
	    marginTop: '1rem'
	  }
	});
	

	const RepositoryList = ({searchTerm}) => {
	  const classes = useStyles();
	  const [expandedRepo, setExpandedRepo] = useState(null);
	  const [debouncedSearchTerm] = useDebounce(searchTerm, 1000);
	  const {data, loading, error} = useQuery(SEARCH_FOR_REPOS,
	    {variables: {search_term: debouncedSearchTerm}}
	    );
	

	  useEffect(() => {
	    setExpandedRepo(null);
	  }, [data]);
	

	  if (loading) {
	    return (
	      <div className={classes.spinnerContainer}>
	        <CircularProgress />
	      </div>
	    );
	  }
	

	  if (error) {
	    return (
	      <Typography
	        variant={'overline'}
	        className={classes.note}
	        component={'div'}
	        color={'error'}
	      >
	        {error}
	      </Typography>
	    )
	  }
	

	  if (!data.search.repositoryCount) {
	    return (
	      <Typography
	        variant={'overline'}
	        className={classes.note}
	        component={'div'}
	      >
	        There are no such repositories!
	      </Typography>
	    )
	  }
	

	  return (
	    <div>
	      {data.search.edges.map((repo, i) => (
	        <Repository
	          repo={repo}
	          expanded={expandedRepo === i}
	          onToggled={() => setExpandedRepo(i)}
	          key={i}
	        />
	      ))}
	    </div>
	  );
	};
	

	export default RepositoryList;


</code>





Now we keep track which repo is opened, using expandedRepo and setExpandedRepo . The effect  resets it every time the data changes, to make sure the repos shrink when we make a new search.<br>
  - we iterate through the data.search.edges, which contains an array of repositories. For every repository, a Repository component is rendered with all the required information.






<h5> STEP 10: render issues </h5>


<br>- This is very similar to how we render repositories. To get issues we need to make another API call, the <i>GET_REPO_ISSUES </i>from step four. Then, display them in a list and show description on click. 
<br>- Create files <i>IssueList.js</i> and <i>Issue.js</i> with this code:



<code>
import React, {useState} from 'react';
	import {Dialog, DialogContent, ListItem, ListItemText} from '@material-ui/core'
	

	const Issue = ({title, bodyHTML}) => {
	  const [dialogOpened, setDialogOpened] = useState(false);
	  return (
	    <>
	      <ListItem button onClick={() => setDialogOpened(true)}>
	        <ListItemText>{title}</ListItemText>
	      </ListItem>
	      <Dialog maxWidth={'xl'} open={dialogOpened} onClose={() => setDialogOpened(false)}>
	        <DialogContent>
	          <div dangerouslySetInnerHTML={{__html: bodyHTML}}/>
	        </DialogContent>
	      </Dialog>
	    </>
	  );
	};
	

	export default Issue;








import {useQuery} from '@apollo/react-hooks';
	import {CircularProgress, List, Typography, makeStyles} from '@material-ui/core';
	import React from 'react';
	import Issue from "./Issue";
	import {GET_REPO_ISSUES} from './queries';
	

	const useStyles = makeStyles({
	  root: {
	    flexDirection: 'column'
	  },
	  spinnerContainer: {
	    display: 'flex',
	    justifyContent: 'space-around'
	  }
	});
	

	const IssueList = ({repoName, repoOwner}) => {
	  const classes = useStyles();
	  const {data, loading, error} = useQuery(GET_REPO_ISSUES,
	    {variables: {
	        name: repoName,
	        owner: repoOwner
	      }});
	

	  if (loading) {
	    return (
	      <div className={classes.spinnerContainer}>
	        <CircularProgress />
	      </div>
	    );
	  }
	

	  if (error) {
	    return (
	      <Typography
	        variant={'overline'}
	        component={'div'}
	        color={'error'}
	      >
	        {error}
	      </Typography>
	    )
	  }
	

	  if (!data.repository.issues.nodes.length) {
	    return (
	      <Typography
	        variant={'overline'}
	        component={'div'}
	      >
	        There are no issues!
	      </Typography>
	    )
	  }
	

	  return (
	    <div className={classes.root}>
	      <Typography variant={'h5'}>Latest issues: </Typography>
	      <List>
	        {data.repository.issues.nodes.map(issue => (
	          <Issue title={issue.title} bodyHTML={issue.bodyHTML} />
	        ))}
	      </List>
	    </div>
	  );
	};
	

	export default IssueList;


</code>



<br>We see the familiar call to <i>useQuery</i>, just with a new query and variables. Note that we do not need debouncing here as we are not dealing with user input. Once data is fetched, we check that there are no errors and data is not empty. If everything is fine, iterate over<i> data.repository.issues.nodes</i> and render an <i>Issue component </i>for every issue.

<br>In the Issue component, a <u>ListItem</u> and a <b>Dialog</b> are rendered. ListItem has the title of the issue and it opens the dialog on click. The dialog has the HTML code of the issue body, the same as we could see on Github.

<br>- Now we just need to render IssueList in Repository.js:




<code>

import React from 'react';
	import IssueList from './IssueList';
	import {
	  ExpansionPanel,
	  ExpansionPanelSummary,
	  ExpansionPanelDetails,
	  Typography,
	  Chip,
	  makeStyles
	} from '@material-ui/core';
	import StarIcon from '@material-ui/icons/Star';
	import PeopleIcon from '@material-ui/icons/People';
	

	const useStyles = makeStyles({
	  root: {
	    marginTop: '1rem'
	  },
	  summaryContainer: {
	    flexDirection: 'column',
	  },
	  summaryHeader: {
	    display: 'flex',
	    flexDirection: 'row',
	    justifyContent: 'space-between',
	    marginBottom: '1rem'
	  },
	  chip: {
	    marginLeft: '0.5rem'
	  }
	});
	

	const Repository = ({repo, expanded, onToggled}) => {
	  const {node: {name, descriptionHTML, owner: {login}, stargazers: {totalCount: totalStarCount}}} = repo;
	  const classes = useStyles();
	  return (
	    <ExpansionPanel
	      expanded={expanded}
	      onChange={onToggled}
	      className={classes.root}
	    >
	      <ExpansionPanelSummary classes={{content: classes.summaryContainer}}>
	        <div className={classes.summaryHeader}>
	          <Typography variant={'h6'}>{name}</Typography>
	          <div>
	            <Chip label={`by ${login}`} avatar={<PeopleIcon/>} className={classes.chip}/>
	            <Chip label={totalStarCount} avatar={<StarIcon/>} className={classes.chip}/>
	          </div>
	        </div>
	        <Typography
	          variant={'caption'}
	          dangerouslySetInnerHTML={{__html: descriptionHTML}}
	          component={'div'}
	        />
	      </ExpansionPanelSummary>
	      <ExpansionPanelDetails>
	        {expanded && (<IssueList repoName={name} repoOwner={login}/>)}
	      </ExpansionPanelDetails>
	    </ExpansionPanel>
	  );
	};
	

	export default Repository;



</code>


- Note the conditional rendering of IssueList . This is done for optimization so that the API request is only made when the user opens the repo.



<h5> Final step: run the app </h5>
