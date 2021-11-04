## Movie Favourites app using React hooks and custom components
### Features
1. Search a [movie database](http://www.omdbapi.com/) using a query string input
2. Select and add movies to a favourites list
3. Remove movies from the favourites list
4. Persist the favourites list between refreshes by storing list in browser's local storage

### Using the [Open Movie Database API](http://www.omdbapi.com/)
We will use the OMDb API, a RESTful web service, to obtain movie information and images.  

Apply for the free [API key](http://www.omdbapi.com/apikey.aspx), and check your registered email to verify your account and obtain your API key.
![Apply for free API key](/public/images/apikey.jpg)

We can query the OMDb using the GET request format `http://www.omdbapi.com/?apikey=[yourkey]&` followed by [parameters](http://www.omdbapi.com/#parameters) to refine the search.  The GET request below queries the OMDb for movies with titles that match `star wars a new hope` 
```
http://www.omdbapi.com/?apikey=9b5b9d51&s=star+wars+a+new+hope
```

The OMDb API with return a list of JSON objects, each for a movie matching the search parameters.
