<script>
    // full explainatin of how this component works with InfiniteScrolling in documentation.md
    
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