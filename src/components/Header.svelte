<script>
  import About from "../pages/About.svelte";


    let hamburger;
    let menu;

    const toggleMenu = () =>{
        if (menu.classList.contains("active")){
            menu.style.display = "none";
            menu.classList.remove("active");
            hamburger.style.transform = "rotate(0deg)";
        }

        else{
            menu.style.display = "flex";
            menu.classList.add("active");
            hamburger.style.transform = 'rotate(270deg)';
        }
    }
</script>

<header>

    <div class ="logo">
        <a class = "link" href = "https://github.com/3b00d09" target="blank">3b00d_09</a>
        <a class="link tmdb-link" href="https://www.themoviedb.org" target="blank">Data provided by <img class ="tmdb-logo" src="./assets/tmdb-logo.svg" alt="tmdb logo"></a>
    </div>

    <div bind:this={hamburger} class ="hamburger" on:mousedown={() =>{
        toggleMenu()

    }}>
        <span></span>
        <span></span>
        <span></span>
    </div> 

    <ul bind:this={menu} class ="navbar">
        <li class ="navitem">
            <a href = "#/">Home</a>
        </li>
        <li class ="navitem">
            <a href = "#/movies/">Movies</a>
        </li>
        <li class ="navitem">
            <a href = "#/favorites">Favorites</a>
        </li>
        <li class ="navitem">
            <a href = "#/about">About</a>
        </li>
    </ul>

</header>

<style>

    header{
        color:white;
        background-color: #1C1C1C;
        display: flex;
        justify-content: space-between;
        align-items: center;
        flex-wrap: wrap;
        gap: 1rem;
        padding: 1rem;
        width: 100%;
    }

    .navbar{
        list-style: none;
        display: flex;
        justify-content: center;
        gap: 1rem;
        flex-wrap: wrap;
    }

    .navitem > a{
        text-decoration: none;
        color: inherit;
        padding: 1rem 0.5rem;
        display: block;
    }

    .link{
        color: inherit;
        text-decoration: none;
        width: fit-content;
    }
    
    .logo{
        font-size: 2rem;
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
    }

    .tmdb-link{
        font-size: .75rem;
        padding: 4px;
    }

    .tmdb-logo{
        width: 60px;
    }

    .navitem > a:hover, .link:hover{
        background: #777;
    }


    .hamburger{
        display: none;
        justify-content: space-between;
        flex-direction: column;
        height: 21px;
        
    }

    .hamburger > span{
        background-color: white;
        width: 30px;
        height: 3px; 
    }

    .hamburger {
        transition: transform 0.5s ease;
    }

    @media(max-width: 768px){
        .hamburger{
            display: flex;
        }

        .navbar{
            display: none;
            width: 100%;
        }
    }

    /* small screen > click menu > large screen makes menu go poof because inline "display:none" overwrites */
    
    @media(min-width: 769px){
        .navbar{
            display: flex !important;
        }
    }

</style>