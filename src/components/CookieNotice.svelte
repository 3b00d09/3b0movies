<script>
    export let visible = true;
  
    function acceptCookies() {
      localStorage.setItem('cookiesAccepted', 'true');
      visible = false;
      document.querySelector("body").style.overflow = "auto";
      location.reload();
    }
  </script>
  
  {#if visible}
  <div class="cookie-notice">
    <p>This product uses the TMDB API but is not endorsed or certified by TMDB. TMDB API may use your cookies to collect information about how the product(s) is used. Please visit the <a class ="link" href="https://www.themoviedb.org/privacy-policy?language=en-GB">TMDB Privacy Policy </a> page for more information and to set your cookie settings. By using this website, you consent to the possible use of strictly necessary cookies.</p>
    <button on:click={acceptCookies}>Accept</button>
    <a href="https://www.themoviedb.org/privacy-policy?language=en-GB" target="_blank" rel="noopener"><button>Go to TMDB</button></a>
  </div>

  <div class ="overlay"></div>
  {/if}
  
  <style>

  .cookie-notice {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    padding: 20px;
    background-color: #333;
    border-radius: 5px;
    text-align: center;
    width: 80%;
    max-width: 500px;
    z-index: 999;
  }

  .cookie-notice button {
    margin-top: 20px;
    padding: 10px 20px;
    color: black;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .overlay {
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 998;
    }

    .link{
        color: #80b2b5 ;
        text-decoration: none;
    }
</style>

  