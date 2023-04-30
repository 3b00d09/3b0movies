<script>

    import { writable } from "svelte/store";   

    import InfiniteScrolling from "../components/InfiniteScrolling.svelte"
    import Action from "../components/pageCategories/movies/Action.svelte";
    import Popular from "../components/pageCategories/movies/Popular.svelte";
    import { moviesPerSlide } from "../store";
    import { onMount } from "svelte";

    let cookiesAccepted;
    onMount(()=>{
        cookiesAccepted = localStorage.getItem('cookiesAccepted') === 'true';
    })
    

    let totalPages;
    let totalMovies;

    // not using queryType now but will use it in future for tv shows.
    let searchQuery, queryType;

    queryType = "movie"

    // bool to help us toggle between search results and the categories displayed on homepage
    let successfullyLoaded = false;

    // a store that is "true" when the search button is clicked, it lets the child know to call the API
    const shouldCallAPI = writable(false);

    // when the child replies with a success message, we display the search results and "toggle off" the categories, else we hide search and display back categories
    // this is a safeguard for when the user searches successfully once then fails the next time

    function handleMessage(event){
        if (event.detail.data === true){
            successfullyLoaded = true;
            document.querySelector(".temp").style.display = "block";
        }
        else{
            successfullyLoaded = false;
            document.querySelector(".temp").style.display = "none";
        }
    }

    async function fetchFirstPage(url){
        let pages = new Map ()
        
        const response = await fetch(url)
        const APIreturn = await response.json()

        totalPages = APIreturn.total_pages;
        totalMovies = APIreturn.total_results;

        let tempArray =  [...APIreturn.results]

        let i = 1;
        while(tempArray.length > 0){
            let temp = "page" + i
            pages.set(temp, tempArray.splice(0,moviesPerSlide))
            i++;
        }
        return pages;
}
</script>


<main>

        <div class ="search">
            <input bind:value = {searchQuery} class= "search-input" type ="text" placeholder="Search for movie...">
                <button class="btn" on:click={()=>{
                    shouldCallAPI.set(true);
                }}>
                Search
                </button>
        </div>

</main>

<!-- keep component display none until we successfully find movies-->
<div class = "temp" style ="display: none;">
    <h1>Search Results</h1>
    <InfiniteScrolling {searchQuery} {queryType} {shouldCallAPI} on:moviesLoaded= {handleMessage}/>
</div>

{#if !successfullyLoaded && cookiesAccepted}
    <h1>Popular Titles</h1>
    <Popular {totalPages} {totalMovies} {fetchFirstPage}/>
    <h1>Action Movies</h1>
    <Action {totalPages} {totalMovies} {fetchFirstPage}/>
{/if}

<style>

    h1{
        font-weight: 400;
    }


    main{
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
        width: 100%;
    }


    .search-input{
        border-radius: 15px;
        font-size: inherit;
        text-align: center;
        padding: 2rem;
    }

    .btn{
        font-size: inherit;
        border-radius: 15px;
        padding: 12px;
        cursor: pointer;
    }

    .btn:active {
    transform: translateY(2px);
    } 

    .search{
        display: flex;
        flex-wrap: wrap;
        place-items: center;
        gap: 0.5rem;
        margin-top: 1rem;
    }

    @media(max-width: 360px){
        .btn, .search-input{
            width: 100%;
        }
    }

    /* @media(min-width: 769px){
        .movies-display{grid-template-columns: repeat(2, 1fr);}
    }

    @media(min-width: 1025px){
        .movies-display{grid-template-columns: repeat(3, 1fr);}
    } */
</style>