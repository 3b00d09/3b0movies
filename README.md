# About

This is a SPA (single-page-app) hobby project build with [Svelte](https://svelte.dev) that uses [TMDB API](https://www.themoviedb.org/documentation/api) to allow for users to browse movies. 
The API is regularly updated so this project can be used for relatively accurate data. The API itself provides many more features but for the time being, movie browsing is generally what this project does.
The documentation file will be used to explain my approach to impliment some of the functionality here.

# How it works

The API requires the cookie notice to be accepted before being called anywhere in the page. Once the cookie notice is accepted, the API is automatically called to fetch data
for the Home page. If you open the site for the first time from a URL that's not "/", it will just refresh the page after accepting cookies and the endpoint concerned with that 
page is called.

## Movie Cards

Movies are displayed as cards that show the poster by default. Once hovered on, the title and release date are displayed, as well as a button that you can click on to favorite a movie.

## Home Page

When this page loads, the API is called to fetch different categories of data. For example, currently it fetches data for the most popular movies as well as action movies. Once 
a movie is successfully searched, the loaded categories disappear and instead we display all searched movies in an infinite scrolling manner (well not really infinite just until 
all search results are displayed).

## Movies Page

This page renders categories of movies that you can browse through by clicking the arrows for next and previous page. There is a progress bar that shows you how far 
you've scrolled but there's also a page index number.

## Movie Page

When clicking on any movie card, you are redirected to the movie page that renders out some information about the movie, as well as the most prominent cast members. Planning
on adding a feature to display platforms where the movie can be watched (Netflix, Prime Video, etc..).

## Favorites Page

A page that simply renders out all movies that have been favorited. You can add or remove movies from favorites by hovering over them and clicking on the bottom left button. 
Browser local storage is used to store the favorited movies and no API calls are associated with this page.

## Not Found

A page that is rendered out whenever an invalid route is visited.
