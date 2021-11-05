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
http://www.omdbapi.com/?apikey=[yourkey]&s=star+wars
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
- `MovieList.js` : Renders a list of movie posters based on the JSON results returned from OMDb. Accepts 2 properties, a favourites component that renders as an overlaid button on each movie poster, and the callback handler when the button is clicked.
- `SearchBox.js` : Renders a text input that user enters the search value.  
- `Add Favourites.js` : Renders an overlay button, when clicked, adds a movie to the favourites list. The new list is saved on the browser's local storage.
- `RemoveFavourites.js` :  Renders an overlay button, when clicked, removes a movie from the favourites list.
- `App.js` : Does the heavy lifting in terms of state management, side effects and callback handling.

The `AddFavourites` component, together with it's callback function `addFavouriteMovie` is passed as properties to the search results `MovieList` component.  Clicking the `AddFavourites` component, invokes the callback function that adds the selected movie to the favourites list, and updates the state variable `favourites` with the new list.
```
	// Adds a new movie to favourites list in state and browser local storage
	const addFavouriteMovie = (movie) => {
		const newFavouriteList = [...favourites, movie];
		setFavourites(newFavouriteList);
		saveToLocalStorage(newFavouriteList);
	};
```
To persist the favourites list across refreshes, the callback function saves the list to the browser's local storage.
```
	// Save list of favourite movies in browser local storage
	const saveToLocalStorage = (items) => {
		localStorage.setItem('react-movie-app-favourites', JSON.stringify(items));
	};
```

The `RemoveFavourites` component, together with it's callback function `removeFavouriteMovie` is passed as properties to the favourites `MovieList` component.  Clicking the `RemoveFavourites` component, invokes the callback function that removes the selected movie from the favourites list, updates the state variable `favourites` and saves the new list to the browser's local storage.
```
	// Remove a movie from favourites list in state and browser local storage
	const removeFavouriteMovie = (movie) => {
		const newFavouriteList = favourites.filter(
			(favourite) => favourite.imdbID !== movie.imdbID
		);

		setFavourites(newFavouriteList);
		saveToLocalStorage(newFavouriteList);
	};
```

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
1. When the search input changes, we want to query the OMDb with the new search parameter and load the new list of movies.  The movie list changes according to the return results from the `searchValue`, hence we include the state variable `searchValue` in the dependency array.
```
	// Get a new movie listing when the search value state variable changes
	useEffect(() => {
		getMovieRequest(searchValue);
	}, [searchValue]);
```

We query the OMDb with the new `searchValue` using the `getMovieRequest` function and update the `movies` state variable with the returned JSON.
```
	// Query the Open Movie DB based on search value
	const getMovieRequest = async (searchValue) => {
		const url = `http://www.omdbapi.com/?s=${searchValue}&apikey=[yourkey]`;

		const response = await fetch(url);
		const responseJson = await response.json();

		if (responseJson.Search) {
			setMovies(responseJson.Search);
		}
	};
```


2. When the browser refreshes, we want to load the list of favourites movies previously selected (which are stored in the browser's local storage).  This is only done once when the main `App` component mounts, hence the empty dependency array. 
```
 	// Retreives the list of favourite movies stored in the browser local storage.
	// Runs only once when App component mounts (i.e. empty dependency array).
	useEffect(() => {
		const movieFavourites = JSON.parse(
			localStorage.getItem('react-movie-app-favourites')
		);

		if (movieFavourites) {
			setFavourites(movieFavourites);
		}
	}, []);
```

### Setup & run

`npm install` dependencies

`npm start`
