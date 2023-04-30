<script>

import TopRated from "../components/pageCategories/movies/TopRated.svelte";
import NowPlaying from "../components/pageCategories/movies/NowPlaying.svelte";
import ComingSoon from "../components/pageCategories/movies/ComingSoon.svelte";

import { moviesPerSlide } from "../store";
import { onMount } from "svelte";

let cookiesAccepted;

onMount(()=>{
    cookiesAccepted = localStorage.getItem('cookiesAccepted') === 'true';
})
let totalPages;
let totalMovies;

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

{#if cookiesAccepted}

 <h1>TOP RATED</h1>

<TopRated {totalPages} {totalMovies} {fetchFirstPage}/> 

 <h1>NOW PLAYING</h1>

<NowPlaying {totalPages} {totalMovies} {fetchFirstPage}/>

<h1>COMING SOON</h1>

<ComingSoon {totalPages} {totalMovies} {fetchFirstPage}/> 

{/if}


<style>
    h1{
        font-weight: 400;
    }
</style>