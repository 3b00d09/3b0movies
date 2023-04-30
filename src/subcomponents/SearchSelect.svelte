<script>

    // NOT USING THIS COMPONENT AT THE MOMENT
    // for search filters when i eventually do tv shows as well 
    export let queryType = "movie";
    import { onMount } from "svelte";
    let searchOptions;

    onMount( () =>{
            const options = searchOptions.querySelectorAll(".search-option");
            attachEventListeners(options)
        })

    function attachEventListeners(options){
        options.forEach((option) =>{
            option.addEventListener("click", () =>{
                    options.forEach((option) =>{
                        option.classList.remove("active")
                    })
                    option.classList.add("active")
                    queryType = option.getAttribute("data-type");
                })
            })
    }

</script>


<div class ="search-options" bind:this={searchOptions}>
    <p class ="search-option active" data-type = "movie">Movie</p>
    <p class ="search-option" data-type = "series">TV Show</p>
</div>


<style>
    .search-options{
        display: flex;
        flex-wrap:wrap;
        gap: .5rem;
    }

    .search-option{
        background-color: white;
        color: black;
        padding: .75rem;
        border-radius: .5rem;
        cursor: pointer;
    }

    .active{
        background-color: #333;
        color: white;
    }

    .search-option:active{
        transform: translateY(-2px)
    }

</style>