# src/pages
All the different pages we render depending on the route visited. 

# src/components
All components live in this file. Additionally, there is a pageCategories.svelte file that contains the components that render different categories of movies, makes it a lot easier 
to add new categories to render in the future.

# src/ subcomponents
Small components that live within other components. Was planning on using this to filter between TV shows and movies for search but I am yet to impliment TV search so this is not doing anything now.

## App.svelte

We are storing a cookiesAccepted variable in local storage. In the App component we are checking if its true so we know whether we render cookie notice or not. onMount, we check if a favorites array exists in localstorage, if it doesnt, we create one. We are also defining our routes here and render them in our "content" div. The Header component is outside the Router component so our Header appears in every Route. 

## Pages

### Home.svelte

onMount, we check the state of cookiesAccepted. If its false, we don't render any component that calls the [TMDB API](https://www.themoviedb.org/documentation/api). This pattern is followed throughout the whole app to avoid calling the [TMDB API](https://www.themoviedb.org/documentation/api) before the user accepts the cookie notice. totalPages and totalMovies are going to be assigned when we call the [TMDB API](https://www.themoviedb.org/documentation/api) and it returns the total_pages and total_results values. These values are going to be passed down to the components that actually handle loading the movies in order to figure out how many pages to load. The exact way will be discussed later down when we reach the categories section. 

Successfully loaded helps us know when to render the component that displays search results. shouldCallAPI is a [Svelte writable](https://svelte.dev/tutorial/writable-stores) that is subscribed to by InfiniteScrolling.svelte, the component that displys search results. Once the search button is clicked, shouldCallAPI is set to true and InfiniteScrolling.svelte will call the [TMDB API](https://www.themoviedb.org/documentation/api) with the searchQuery and queryType variables. searchQuery is the movie searched for and queryType is movie. I can just use "movie" but I am keeping it this way if I impliment TV shows in the future. 

handleMessage is a function that waits for the InfiniteScrolling.svelte component to dispatch a message with the result of the [TMDB API](https://www.themoviedb.org/documentation/api) search. If we search successfully, we display the InfiniteScrolling component with the search results, else we just keep the categories currently loaded. This will also hide the InfiniteScrolling component if a user successfully searches then fails a search which should probably not happen but thats for another time.

fetchFirstPage is a function that is used by the children categories to fetch the first page out of however many pages the search result is. We take the page that the [TMDB API](https://www.themoviedb.org/documentation/api) returns and split it into multiple pages into a map where each page contains "moviesPerSlide" movies. Effectively what happens here is we take one page from the [TMDB API](https://www.themoviedb.org/documentation/api) and split it into multiple pages to load. This pattern is repeated throughout all components that render movie categories.

## Components

### CookieNotice.svelte

Toggles cookiesAccepted to true and refreshes page once accepted. We refresh page because most components fetch data onMount unless cookiesAccepted is false. Since we fetch data onMount its a lot easier to just refresh the page when it loads. 

### Header.svelte

Standard Header with responsiveness. Smaller screens have a hamburger menu that is togglable to view links. Oh also spinning animation :D

### InfiniteScrolling.svelte

The component that is responsibile for rendering search results. This component subscribes to the writable "shouldCallAPI" to know when to call the [TMDB API](https://www.themoviedb.org/documentation/api) with the search queries. 

reachedFinalPage and enablePagination are both [Svelte writables](https://svelte.dev/tutorial/writable-stores) that are used in Loader.svelte to help manage the state of InfiniteScrolling. More details in the Loader.svelte section.

CallAPI is where the cooking happens. First, we reset some variables then check if the user's query is undefined or if they're pressing search again without changing the value of searchQuery, if either case is true, we dispatch to Home.svelte that we failed to fetch movies. This avoids wasteful [TMDB API](https://www.themoviedb.org/documentation/api) calls.

After we manage to go through the checks and call the [TMDB API](https://www.themoviedb.org/documentation/api), we filter out the movies that don't have a poster_path in them. It just makes the section look nicer and movies that don't have a poster are probably not too relevant. If after all that work we have at least 1 movie, we dispatch a message to Home.svelte saying we successfully got the data so it can render the InfiniteScrolling component and then we set enablePagination to true because we are going to start scrolling and we set reachedFinalPage to false.


LoadNextPage is responsible for checking if we reached final page and for fetching the next page. If we reach final page, we set reachedFinalPage to true to stop the scrolling behavior and hide the loading spinner. 


### Loader.svelte

The component responsible for helping InfiniteScrolling fetch new movies and load them with a nice loading spinner. We detect when the user reaches the end of the page with some magic, if you wanna know exactly how then check the src file. Once the user hits the bottom of the screen, we check if enablePagination is true because if it isnt, then we don't want to do anything (enablePagination is true only when we have search results to display). If it is true, we display the loading spinner and dispatch a message to InfiniteScrolling that we need more movies to display.  

This component is subscribed to reachedFinalPage which is set to true by InfinteScrolling when we reach the final page to display. We use the total_pages value from the [TMDB API](https://www.themoviedb.org/documentation/api) response to help us toggle reachedFinalPage to true. Once reachedFinalPage is true, we stop listening to user scroll and hide the loader.

TLDR for how InfiniteScrolling and Loader work:

InfiniteScrolling fetches movies > enablePagination true and reachedFinalPage is false > Loader listens for user scrolling > user reaches bottom of page > Loader dispatches to InfiniteScrolling we need more movies and it displays spinner > InfiniteScrolling checks if we reach final page, if no return new movies if yes then set reachedFinalPage to true > if reachedFinalPage is true then we set enablePagination to false  > stop listening for scroll and hide spinner.

### Popular.svelte (and other categories)

The component that takes in 4 props, 