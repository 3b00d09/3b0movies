<script>

    import { onMount } from "svelte";
    export let params = {};
    const ID = params.id;

    let movie = {};
    let loadedMovie = false;
    let cast;

    let cookiesAccepted;

     onMount(async () =>{
        cookiesAccepted = localStorage.getItem('cookiesAccepted') === 'true';
        if (!cookiesAccepted) return;
        const responseMovie = await fetch(`https://api.themoviedb.org/3/movie/${ID}?api_key=${ENV_API_KEY}&language=${ENV_API_LANG}&region=${ENV_API_REGION}`)
        if (responseMovie.status === 404){
            movie = undefined;
            return;
        }
        movie = await responseMovie.json()

        const responseCast = await fetch(`https://api.themoviedb.org/3/movie/${ID}/credits?api_key=${ENV_API_KEY}&language=${ENV_API_LANG}&region=${ENV_API_REGION}`)
        let allCast = await responseCast.json()
        allCast = allCast.cast

        // we use math.min to get either the length of the array or the number 12. basically cut the first 12 elements, if there is less than 12, cut all of them out
        const numberofCast = 12;
        const numberOfElements = Math.min(numberofCast, allCast.length)
        cast = allCast.splice(0, numberOfElements)


        loadedMovie = true;
    })



</script>



    {#if loadedMovie}
        <div class ="movie">
            <img class ="poster" src = "https://image.tmdb.org/t/p/w342/{movie.poster_path}" alt ="movie poster" />
            <div class ="details">
                <h1>Title</h1>
                <div class ="title">{movie.title}</div>
                <h1>Release Date</h1>
                <div class="date">{movie.release_date}</div>
                <h1>Genres</h1>
                {#each movie.genres as genre}
                    {genre.name + " "}
                {/each}
                <h1>Runtime</h1>
                {movie.runtime + " min "}
                <h1>Plot</h1>
                <div>{movie.overview}</div>
            </div>
        </div>

        <h1>Cast</h1>
            <div class="cast">
                {#each cast as cast}
                    <div class="cast-member">
                        <img src = "https://image.tmdb.org/t/p/w185/{cast.profile_path}" alt="cast profile pic" on:error={(event)=>{
                            event.target.src="/assets/errorImage.png"
                        }}>
                        <p class="name">{`"${cast.name}" playing "${cast.character}"`}</p>
                    </div>
                {/each}
            </div>

    {:else}
        <div>MOVIE NOT FOUND</div>
    {/if}


<style>
    .cast{
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
        gap: 1rem;
        width: 100%;
        margin: 1rem 0;
    }

    .cast-member{
        display: grid;
        justify-content: center;
        height: 100%;
        grid-gap: 0;
    }

    .cast-member > img{
        width: 160px;
        height: auto;
        border-radius: 30px;
        justify-self: center;
    }

    .name{
        text-align: center;
        word-wrap: break-word;
        padding: 1rem;
        font-size: 1rem;
        margin: 0;
        max-width: 160px;
    }

    .movie{
        display: flex;
        justify-content: space-evenly;
        align-items: center;
        flex-wrap: wrap;
        gap: .75rem;
    }

    .details{
        background-color: #232024;
        padding: 1rem;
        flex-basis: 50%;
        border-radius: 15px;
    }

    @media (max-width: 1090px){
        
        .movie{
            justify-content: space-between;
            margin: 0 1rem 0 .25rem;
            gap: 0.25rem;
        }
    }

    @media(max-width:770px){
        .cast{
            grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
            gap: 1.25rem;
        }

        
        .cast-member > img{
            width: 120px;
            justify-self: center;
        }

        .details{
            flex-basis: 100%;
        }

        /* spread the extra space as margin left and right effectively centering it */
        .poster{
            margin: 0 auto;
        }
    }

    @media(max-width: 470px){
        .cast{
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 1.25rem;
        }
        .poster{
            width: 200px;
        }

        .cast-member > img{
            width: 90px;
        }
    }

</style>