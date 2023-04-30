<script>


    import {onMount } from "svelte";
    import MovieSlider from "../../MovieSlider.svelte";
    import { moviesPerSlide } from "../../../store";
    
    export let fetchFirstPage;
    export let totalPages;
    export let totalMovies;

    // this is rly weird but i have to assign totalpages to a variable thats local to this component otherwise it will get over-written by other component's totalPages value
    let totalSearchPages;
    let totalSearchResults;
    
    let slides = new Map();

    // keep track of what page we are on in our API calls. THIS IS NOT THE SAME AS PAGES DISPLAYED (we take one API page and split it into <moviesPerSlide> display pages)
    let currPage = 1;
    
    // variable that is changed to true once we have our pages map fully loaded with data (couldnt use the map in child component otherwise)
    let pagesLoaded = false;
    
    
    onMount(async() =>{
    
        const url = `https://api.themoviedb.org/3/movie/now_playing?api_key=${ENV_API_KEY}&language=${ENV_API_LANG}&region=${ENV_API_REGION}&page=${currPage}`

        slides = await fetchFirstPage(url)

        totalSearchPages = totalPages;
        totalSearchResults = totalMovies;

        // we are loaded now and can render the child component :thumbsup:
        pagesLoaded = true;
    
    })

        // function that is exported to child so that the child can fetch more movies when needed
        async function fetchMore(movies, currPage){

            const url = `https://api.themoviedb.org/3/movie/now_playing?api_key=${ENV_API_KEY}&language=${ENV_API_LANG}&region=${ENV_API_REGION}&page=${currPage}`

            let Newslides = new Map();
    
            const response = await fetch(url)
            const APIreturn = await response.json()

            let tempArray =  [...APIreturn.results]


            let i = currPage + 4;
            while(tempArray.length > 0){
                let temp = "page" + i
                Newslides.set(temp, tempArray.splice(0,moviesPerSlide))
                i++;
            }

            Newslides.forEach((key) =>{
                movies.push(key)
            })

            return movies;
        }  
    

</script>


{#if pagesLoaded}
<MovieSlider {slides} {totalSearchPages} {totalSearchResults} {fetchMore}/>
{/if}