<script>

    import {onMount} from "svelte";    
    import { moviesPerSlide } from "../store";
    import MovieCard from "./MovieCard.svelte"


    // a map with each page containing an array of movies (pages are what the user navigates through its more like sections)
    export let slides = new Map();

    // function to call API for more movies when the user reaches the end of the currently loaded movies
    export let fetchMore;

    // variable to know how many total pages our API returns. not the same as how many pages we are displaying cause we split 1 API page to seperate pages
    export let totalSearchPages;
    export let totalSearchResults;

    // variables to help the user know how many pages left and the page they're on, also helps us with the progress bar
    let pageIndex = 1;
    let totalPages;

    // variables to help us call the API at the appropriate times when we need more movies
    let currPage = 1;

    // progress bar stuff
    let progressPercentage = 0;
    let progressBar; 

    
    // movies is all movies and curr movies is the movies we are displaying now
    let movies = [];
    let currMovies = [];

    
    let previousArrow;
    let nextArrow; 


    
    // when the component mounts we take all pags and split them into seperate arrays 
    onMount(()=>{

        // spread all the movie arrays in the pages map onto one movie array
        movies.push(...slides.values())

        //dont ask me what is happening here but it works 
        totalPages = Math.ceil(((totalSearchResults - (totalSearchPages - 1) * 20) / moviesPerSlide) + ((totalSearchPages - 1) * 20) / moviesPerSlide)
        totalPages = Math.min(totalPages, 99);

        currMovies = movies[0]

        loadNextPage()

        previousArrow.addEventListener("click", ()=>{
            if (pageIndex === 1) return
            pageIndex--;
            navigateIndex(pageIndex);
        })

        nextArrow.addEventListener("click", ()=>{
            if (pageIndex === totalPages) return
            pageIndex++;
            navigateIndex(pageIndex);
        })
    })

    async function navigateIndex(pageIndex){

        // we call the api before we actually need to so when the user reaches there the data is already loaded
        // we determine this by taking the length of our movies and calling the api when we are half-way there
        if ((pageIndex) === Math.floor(movies.length / 2)){
            loadNextPage()
        }
        currMovies = movies[pageIndex - 1]

    }

    async function loadNextPage(){
        currPage++;
        movies = await fetchMore(movies, currPage)
    }

    $:{
        progressPercentage = ((pageIndex / totalPages) * 100).toFixed(2);
        progressPercentage = parseFloat(progressPercentage); // convert back to a number
        if(progressBar){
            progressBar.style.width = `${progressPercentage}%`;
        }
    }
    
</script>


<main>

    <div class ="navigation">
        <div class ="previous" bind:this={previousArrow}></div>
        <div class ="page-index">
            <div class ="progress-bar" bind:this ={progressBar}></div>
            <p>{`${pageIndex} / ${totalPages}`}</p>
        </div>
        <div class ="next" bind:this={nextArrow}></div>
    </div>

    <div class ="movies">
        {#each currMovies as movie}
            <MovieCard movie = {movie} />
        {/each}
    </div>

</main>


<style>


    .progress-bar{
        width: 1%;
        height: 3px;
        background-color: #262641;
        transition: width 0.7s ease;
    }

    /**i spent too long in paint 3d trying to do this so w/e we google arrow css*/

    .previous {
        width: 30px;
        height: 0;
        border-top: 30px solid transparent;
        border-bottom: 30px solid transparent;
        border-right: 30px solid #fff;
    }

    .next {
        width: 30px;
        height: 0;
        border-top: 30px solid transparent;
        border-bottom: 30px solid transparent;
        border-left: 30px solid #fff;
    }

    .previous:hover, .next:hover{
        opacity: 0.2;
        cursor: pointer;
    }


    .navigation{
        display: flex;
        justify-content: space-around;
        align-items: center;
        padding: 1rem;
        gap: 1rem;
    }

    .page-index{
        display: grid;
        place-items: center;
        width: 80%;
    }

    .movies{
        display: flex;
        gap: 1rem;
        justify-content:space-evenly;
        flex-wrap: wrap;
        margin: 1rem;
    }

</style>