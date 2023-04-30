<script>
    import { createEventDispatcher, onMount } from "svelte";

    export let enablePagination;
    export let reachedFinalPage;
    const dispatch = createEventDispatcher()


    // listening to when the user reaches the end of screen. taking doc height instead of body height because body is 100vh so it wont work
    // scrollHeight is the height of the document 
    // scrollTop is how much we've scrolled vertically
    // clientHeight is the height of the visible portion on screen
    
    const handleScroll = () => {
        
        const scrollHeight = document.documentElement.scrollHeight;
        const scrollTop = window.pageYOffset;
        const clientHeight = window.innerHeight;
        
        if (!$enablePagination) return;
        if (scrollTop + clientHeight >= scrollHeight) {
            document.querySelector(".loading").style.display = "block";

            // SEND TO PARENT WE NEED MORE MOVIES ALOOOOOOOO
            dispatch("fetch-more");
        }  
    }

    // ok this is gonna be a banger, here is what is happening:
    // reachedFinalPage and enablePagination both start off as false. 
    // enablePagination is enabled when a movie is successfully called, and thus the scrolling begins. 
    // we have a total of x pages, when we reach the final page, we say we set reachedFinalPage to true in the loadmore function
    // when we change reachedFinalPage, we set enablePagination to false and hide the loader
    // what that does is the next time the user scrolls to the bottom, we dont call API anymore 
    // we can just disable pagination by setting enablePagination to false without needing reachedFinalPage but we wouldn't be able to hide loader

    reachedFinalPage.subscribe((value) =>{
        if (value === true){
            enablePagination.set(false);
            document.querySelector(".loading").style.display = "none";
        }
    })

    window.addEventListener('scroll', handleScroll)
</script>

<main>
    <div class ="loading"></div>
</main>

<style>

    main{
        display: grid;
        justify-content: center;
        margin: 1rem 0;
    }

    .loading {
    border: 8px solid #f3f3f3;
    border-top: 8px solid #3498db;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    animation: spin 2s linear infinite;
    }
  
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }

</style>