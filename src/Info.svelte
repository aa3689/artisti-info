<script>
  import { Pulse } from 'svelte-loading-spinners';
  import { fade, fly } from 'svelte/transition';
  import { createEventDispatcher } from 'svelte';
  import Button from './Button.svelte';
  import artistData from './artistData.js';

  const dispatch = createEventDispatcher();
  const getVideos = () => dispatch('getVideos');
  const showExtraInfo = () => dispatch('showExtraInfo');

  let artistInput; // Tähän asetetaan käyttäjän syöttämä data
  let showSearch = true; // Käytetään hakukentän näyttämiseen / piilottamiseen
  let artistPromise; // Tähän asetetaan haettu data artistista / bändistä

  // Tuo hakukentän esiin ja piilottaa haetut videot
  function hideVideos() {
    showSearch = true;
    dispatch('hideVideos');
  }

  // Datan haku, parametrina käyttäjän syöttämä data.
  // Asetetaan haetun artistin / bändin ID storeen
  // sekä palautetaan haetun artistin / bändin data
  const getArtist = async (artist) => {
    const response = await fetch(`https://www.theaudiodb.com/api/v1/json/1/search.php?s=${artist}`);
    const data = await response.json();
    artistData.set(data.artists[0]);
    return data.artists[0];
  };
</script>

<!--
  Jos hakua ei suoritettu
  niin näytetään otsikko sekä hakukenttä
-->
<div id="container">
  {#if showSearch}
    <div in:fade={{ duration: 1000 }}>
      <h1>Artisti-info</h1>
      <!--
        Hakukenttä. Bindataan käyttäjän syöttämä data artistInput-muuttujaan
      -->
      <input type="text" placeholder="Artistin tai bändin nimi" bind:this={artistInput} />
      <!--
        Hakunappi. Painettaessa piilottaa hakukentän näkyvistä
        ja suorittaa haku-funktion
      -->
      <Button
        on:click={() => {
          showSearch = false;
          artistPromise = getArtist(artistInput.value);
        }}>Hae</Button
      >
    </div>
  {/if}

  <!--
    Näytetään hakukenttä, kunnes haku tehdään
    ('showSearch' menee falseksi)
  -->
  <div id="infoBox">
    {#if showSearch}
      <div />
    {:else}
      <!--
          Odotetaan haun valmistumista (haettu data on artistPromise-muuttujassa)
        -->
      {#await artistPromise}
        <p>Ladataan tietoja...</p>
        <div class="loadingIcon"><Pulse size="90" color="gray" unit="px" duration="1s" /></div>

        <!--
          Haun valmistumisen jälkeen näytetään artistin / bändin logo ja biografia
        -->
      {:then data}
        <div out:fly={{ y: 300, duration: 1000 }}>
          <Button on:click={hideVideos}>Tee uusi haku</Button>
        </div>
        <div in:fade={{ duration: 2000 }} out:fly={{ y: 300, duration: 1000 }}>
          <img id="logo" src={data.strArtistLogo} alt="Artistin / bändin {data.strArtist} logo" />
          <p id="biography">{data.strBiographyEN}</p>
        </div>
        <!--
          Videoiden hakunappi
        -->
        <div id="inlineButtons">
          <div out:fly={{ y: 300, duration: 1000 }}>
            <Button on:click={getVideos}>Hae videoita</Button>
          </div>

          <!--
          Lisätiedot-nappi
        -->
          <div out:fly={{ y: 300, duration: 1000 }}>
            <Button on:click={showExtraInfo}>Näytä lisätiedot</Button>
          </div>
        </div>

        <!--
          Jos haun aikana tapahtui virhe niin näytetään se
        -->
      {:catch error}
        <div out:fly={{ y: 300, duration: 1000 }}>
          <Button
            on:click={() => {
              showSearch = true;
            }}>Tee uusi haku</Button
          >
        </div>
        <div out:fly={{ y: 300, duration: 1000 }} class="error">
          <p>Artistia / bändiä ei löytynyt</p>
        </div>
        <!--
          details-elementti ja sen tyylimäärittelyt kopioitu @
          https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details
        -->
        <details out:fly={{ y: 300, duration: 1000 }}>
          <summary>Yksityiskohdat</summary>
          <p>Virhe: {error.message}</p>
        </details>
      {/await}
    {/if}
  </div>
</div>

<style>
  h1 {
    color: gray;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
  }

  #container {
    text-align: center;
    margin: 0 auto;
  }

  .loadingIcon {
    display: inline-block;
    margin-top: 25px;
  }

  #logo {
    max-width: 500px;
    height: auto;
    margin-top: 10px;
  }

  #biography {
    margin: 10px auto;
    max-width: 75%;
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    font-size: 1.2rem;
  }

  .error {
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    color: darkred;
    font-size: 2rem;
  }

  details {
    border: 1px solid #aaa;
    border-radius: 4px;
    padding: 0.5em 0.5em 0;
  }

  summary {
    font-weight: bold;
    margin: -0.5em -0.5em 0;
    padding: 0.5em;
  }

  details[open] {
    padding: 0.5em;
  }

  details[open] summary {
    border-bottom: 1px solid #aaa;
    margin-bottom: 0.5em;
  }

  #inlineButtons {
    display: flex;
    justify-content: space-between;
    max-width: 30%;
    margin: 20px auto;
  }

  @media (max-width: 810px) {
    #inlineButtons {
      max-width: 40%;
    }
    #biography {
      max-width: 90%;
    }
  }

  @media (max-width: 660px) {
    #inlineButtons {
      max-width: 50%;
    }
  }
</style>
