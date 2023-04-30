<script>
	// SPA router to redirect between pages imported from ./pages
	import Router from "svelte-spa-router";

	import Home from "./pages/Home.svelte";
	import About from "./pages/About.svelte";
  	import NotFound from "./pages/NotFound.svelte";
	import Favorites from "./pages/Favorites.svelte";
	import Movie from './pages/Movie.svelte'
	import Movies from "./pages/Movies.svelte";

	import Header from "./components/Header.svelte";
  	import Footer from "./components/Footer.svelte";
	import CookieNotice from "./components/CookieNotice.svelte";

	import { onMount } from "svelte";


	let favorites = [];

	let cookiesAccepted = localStorage.getItem('cookiesAccepted') === 'true';

	onMount(()=>{
		const body = document.querySelector('body');
		if(!localStorage.getItem("favorites")){
			localStorage.setItem('favorites', JSON.stringify(favorites));
		}

		if (!cookiesAccepted) body.style.overflow = "hidden";
	})

	// Routes in the url bar
	const routes = {
		"/": Home,
		"/about":About,
		"/favorites":Favorites,
		"/movie/:id": Movie,
		"/movies": Movies,

		// star means anything not specified from the above urls
		"*": NotFound
	}
</script>

<main>
	<Header />

	{#if !cookiesAccepted}
  		<CookieNotice />
	{/if}
	<div class ="content">
		<Router {routes} />
	</div>

	<!-- nothing really to put in footer for now-->
	<!-- <Footer /> -->
</main>

<style>
	.content{

		background-color: #0F0F0F;

		/* background-image: url(../assets/theater.png); */

		/* centers the bg image and makes it not scroll with content */
		/* background-repeat: no-repeat;
		background-attachment: fixed;
		background-position: center; */

		/* take up remaining height of the parent div */
		flex-grow: 1;
		
		padding: 1rem;

		/* position: absolute;
		top: 75px;
		bottom: 0;
		width: 100%; */
	}

	main{
		/* turn main into a flexbox to make the 'main content' child div take up the remaining space */
		display: flex;
		flex-flow: column;
		height: 100%;
	}
</style>