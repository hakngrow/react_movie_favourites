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

We can query the OMDb using the GET request format `http://www.omdbapi.com/?apikey=[yourkey]&` followed by [parameters](http://www.omdbapi.com/#parameters) to refine the search.  In this project, we will use the `s` parameter, which queries the OMDb using movie titles. The GET request below queries the OMDb for movies with titles that match `star wars`.  Try it out in a browser or using a developer tool like [Postman](https://www.postman.com/).
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
Once we are able to query the OMDb, we can proceed to the layout of our movie app.

### Wireframing the app UI
![Layout design of the movie app](/public/images/wireframe.jpg)

The layout of the favourites movie app consists of 3 sections.
1. A text input to enter the query to search movies on the OMDb.
2. A search result section to display the list of movies returned from the OMDb search. Each movie in this section is represented by a movie poster with an overlaid button to add the movie to the favourites list. 
3. A section to display the movies selected as favourites. Each movie in this section is represented by a movie poster with an overlaid button to remove the movie from the favourites list.

### Custom React components
The custom components are in the `/src/components` directory.

- `MovieListHeading.js` : Renders a heading above a movie list.
- `MovieList.js` : Renders a list of movie posters based on the JSON results returned from OMDb. Accepts a favourites component that renders as a overlaid button on each movie poster, and the callback handler when the button is clicked.
- `SearchBox.js` : Renders a text input that user enters the search value.  
- `Add Favourites.js` : Renders an overlay button, when clicked, adds a movie to the favourites list.
- `Remove Favourites.js` :  Renders an overlay button, when clicked, removes a movie from the favourites list.

### State Management using `useState`
```
	// Listing of movies from search
	const [movies, setMovies] = useState([]);

	// Listing of movies selected as favourites
	const [favourites, setFavourites] = useState([]);

	// Value of movie search input
	const [searchValue, setSearchValue] = useState('');
```

### Side Effects using `useEffect`
1. When the search input changes, we want to query the OMDb with the new search parameter and load the new list of movies.
```
	// Get a new movie listing when the search value state variable changes
	useEffect(() => {
		getMovieRequest(searchValue);
	}, [searchValue]);
```
3. When the browser refreshes, we want to load the list of favourites movies previously selected.
