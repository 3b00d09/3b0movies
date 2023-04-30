<script>

    import {createEventDispatcher} from "svelte";
    import MovieCard from "./MovieCard.svelte";   
    import Loader from "./Loader.svelte";
    import { writable } from "svelte/store";

    // dispatcher to communicate with the parent
    const dispatch = createEventDispatcher();

    export let searchQuery, queryType;
    export let shouldCallAPI;

    // keeping this here for later use when i eventually do tv shows
    queryType = '';

    const reachedFinalPage = writable(false);

    // variable to help the child loader know that we should enable pagination 
    const enablePagination = writable(false);

    // variable to help us know when we reach final page so we set enablePagination to false
    let finalPage;

    // bunch of variables idk dont ask me if this is good practice or not
    let movies = [];
    let APIresponse = [];
    let currPage = 1;

    // keep track of what the user's most recent search was so if they press search without changing what they typed we dont call API
    let prevQuery;

    // listen to the shouldCallAPI writable and call the API when it is set to "true"
    shouldCallAPI.subscribe(()=>{
        if($shouldCallAPI === true) callAPI();
    })

    async function callAPI(){

        // reset current page otherwise if we search something then scroll a few pages and search something else weird stuff happens
        currPage = 1;

        // reset the writable for next use
        shouldCallAPI.set(false);


        // invalid searches (THIS NEEDS FIXING BECAUSE IT RESETS THE DIV IF THE USER SEARCHES WITHOUT CHANGING THE QUERY)
        if (searchQuery == undefined || prevQuery === searchQuery) {
            dispatch("moviesLoaded", {data: false})
            enablePagination.set(false);
            return;
        }

        // use encodeURIComponent not encodeURI because we are encoding part of the url not the entire thing
        searchQuery = encodeURIComponent(searchQuery)

        const response = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=${ENV_API_KEY}&language=${ENV_API_LANG}&region=${ENV_API_REGION}&query=${searchQuery}&page=1&include_adult=false`)
        APIresponse = await response.json()

        finalPage = APIresponse.total_pages
        movies = [...APIresponse.results.filter(movie => movie.poster_path)]
        
        if (movies.length === 0){
            dispatch("moviesLoaded", {data: false})
            enablePagination.set(false);
            return
        } 
        
        dispatch("moviesLoaded", {data: true});
        enablePagination.set(true);
        reachedFinalPage.set(false);
        prevQuery = searchQuery;
    }


    async function loadNextPage(){
        currPage++;

        if(currPage === finalPage){
            reachedFinalPage.set(true);
        }

        const response = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=${ENV_API_KEY}&language=${ENV_API_LANG}&region=${ENV_API_REGION}&query=${searchQuery}&page=${currPage}&include_adult=false`)
        APIresponse = await response.json()
        movies = [...movies, ...APIresponse.results.filter(movie => movie.poster_path)]
    }
</script>

<div class ="movies-display">
    {#each movies as movie}
        <MovieCard movie = {movie}/>
    {/each}
</div>

<Loader on:fetch-more={loadNextPage} {enablePagination} {reachedFinalPage}/>

<style>

.movies-display{
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
        gap: 1rem;
        width: 100%;
        margin: 1rem 0;
    }

</style>