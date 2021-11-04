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

We can query the OMDb using the GET request format `http://www.omdbapi.com/?apikey=[yourkey]&` followed by [parameters](http://www.omdbapi.com/#parameters) to refine the search.  The GET request below queries the OMDb for movies with titles that match `star wars`.  Try it out in a browser or using a developer tool like [Postman](https://www.postman.com/).
```
http://www.omdbapi.com/?apikey=9b5b9d51&s=star+wars
```

The OMDb API returns a list of JSON objects, each for a movie matching the search parameters.

```
{
    "Search": [
        {
            "Title": "Star Wars",
            "Year": "1977",
            "imdbID": "tt0076759",
            "Type": "movie",
            "Poster": "https://m.media-amazon.com/images/M/MV5BNzVlY2MwMjktM2E4OS00Y2Y3LWE3ZjctYzhkZGM3YzA1ZWM2XkEyXkFqcGdeQXVyNzkwMjQ5NzM@._V1_SX300.jpg"
        },
        {
            "Title": "Star Wars: Episode V - The Empire Strikes Back",
            "Year": "1980",
            "imdbID": "tt0080684",
            "Type": "movie",
            "Poster": "https://m.media-amazon.com/images/M/MV5BYmU1NDRjNDgtMzhiMi00NjZmLTg5NGItZDNiZjU5NTU4OTE0XkEyXkFqcGdeQXVyNzkwMjQ5NzM@._V1_SX300.jpg"
        },
        ...
    ],
    "totalResults": "677",
    "Response": "True"
}
```

Once you are able to query the OMDb, we can proceed to the layout design of our movie app.
