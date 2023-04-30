<script>

    import MovieCard from "../components/MovieCard.svelte";
    import { onMount } from "svelte";

    let movies;

    let loaded = false;

    onMount(()=>{
        movies = JSON.parse(localStorage.getItem('favorites'));
        if (movies.length === 0) return
        loaded = true;
    })

</script>

{#if loaded}
    <div class ="movies-display">
        {#each movies as movie}
            <MovieCard movie = {movie}/>
        {/each}
    </div>
{/if}


<style>

.movies-display{
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
        gap: 1rem;
        width: 100%;
        margin: 1rem 0;
    }

</style>