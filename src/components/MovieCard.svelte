<script>
    
    export let movie;

    import { fly, fade, slide} from 'svelte/transition';
    import { onMount } from 'svelte';

    let favorites;
    let exists;
    let div;

    onMount(()=>{
        favorites = JSON.parse(localStorage.getItem('favorites'));
        exists = favorites.some(favorite => JSON.stringify(favorite) === JSON.stringify(movie));
        div.addEventListener("click", addToFavorites)
    })

    // check if exists every time movies changes (have to add && favorites otherwise this will run before onMount and we get reference error)
    // we do this because if we favorite the 3rd movie, then loading next slide kept the third movie favorited 
    $:{
        if(movie && favorites){
            exists = favorites.some(favorite => JSON.stringify(favorite) === JSON.stringify(movie));
        }
    }

    // might use this later idk
    const encodedTitle = encodeURIComponent(movie.title);

    // get year from date as we dont wanna display full release date
    const date = new Date(movie.release_date)
    const year = date.getFullYear()

    function addToFavorites(){
        favorites = JSON.parse(localStorage.getItem('favorites'));
        console.log(exists)

        if(!exists){
            favorites.push(movie)
            localStorage.setItem("favorites", JSON.stringify(favorites))
            exists = true;
        }

        else{
            favorites = favorites.filter(element => JSON.stringify(element) !== JSON.stringify(movie))
            localStorage.setItem("favorites", JSON.stringify(favorites))
            exists = false;
        }
    }

    //in:slide ={{duration: 400, y: 20}} out:slide ={{duration: 600, y: -20}}

</script>

<div class ="movie">
    <div class = "card">
        <div style = "position: relative;">
            <a href="#/movie/{movie.id}"><img class ="poster" src = "https://image.tmdb.org/t/p/original/{movie.poster_path}" alt ="movie poster" on:error={(event)=>{
                event.target.src="/assets/errorImage.png"
            }}></a>
            <h3 class ="title">{movie.title}</h3>
            <div class ="{exists ? "favorited" : "default"}" bind:this={div}>{`${exists ? "Favorited" : "Add to Favorites"}`}</div>
            <h3 class ="year">{year}</h3>
        </div>
    </div>
</div>

<style>
    .movie{
        display: grid;
        justify-content: center;
        height: 100%;
    }

    .poster, .title{
        width: 150px;
        height: auto;
    }

    .poster{
        transition: opacity 0.2s ease-in-out;
    }
    
    .card{
        position: relative;
    }

    /** shared css between the two classes */
    /** this really did just start as 2 classes idk how its like 20 now*/
    .year, .default, .title, .favorited{
        display: none;
        position: absolute;
        padding: 0.25rem;
        font-weight: 600;
    }

    .year{
        bottom: 4px;
        right: 0;
        background-color: #4C3A32;
        border-top-left-radius: 10px ;
    }

    .default, .favorited{
        bottom: 4px;
        left: 0;
        background-color: #4C3A32;
        border-top-right-radius: 10px ;
    }

    .title{
        text-align: center;
        word-wrap: break-word;
        padding: 1rem;
        top: 0;
        /** background-color: #3E3930; **/
        /* background-image: url("/assets/wood/wood-crop.png");
        background-size: cover;
        background-repeat: no-repeat; */
    }


    .title:hover, .default, .favorited{
        cursor: pointer
    }

    h3{
        font-weight: 400;
    }

    a{
        color: inherit;
        font-weight: inherit;
        text-decoration: none;
    }

    .card:hover .year,.card:hover .default, .card:hover .title{
        display: block;
    }

    .card:hover .poster{
        opacity: 0.2;
    }

    .favorited{
        display: block;
    }

    @keyframes fadeIn{
        0% {opacity: 0;}
        100%{opacity: 1;}
    }

    @media (max-width: 480px){
        .poster, .title{
        width: 126px;
        height: auto;
    }
    
    }

    @media (max-width: 768px){
        .poster, .title{
        width: 156px;
        height: auto;
    }
    
    }
</style>