<script>
  import { Pulse } from 'svelte-loading-spinners';
  import { fade, fly } from 'svelte/transition';
  import Button from './Button.svelte';
  import { createEventDispatcher } from 'svelte';
  import artistID from './artistID.js';

  const dispatch = createEventDispatcher();

  const fetchVideos = () => dispatch('getVideos');

  //let visible = true;
  let artistInput; // Tähän asetetaan käyttäjän syöttämä data
  let showSearch = true; // Käytetään hakukentän näyttämiseen / piilottamiseen
  let artistPromise; // Tähän asetetaan haettu data artistista / bändistä
  let artistNumber; // Tähän asetetaan haetun artistin ID myöhäisempiä hakuja varten

  // Datan haku, parametrina käyttäjän syöttämä data
  const getArtist = async (artist) => {
    const response = await fetch(`https://www.theaudiodb.com/api/v1/json/1/search.php?s=${artist}`);
    if (!response.ok) {
      throw new Error('Haku epäonnistui');
    }
    const data = await response.json();
    const temp = data.artists[0];
    artistNumber = temp.idArtist;
    artistID.set(artistNumber);
    //console.log(artistNumber);
    return data.artists[0];
  };
</script>

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
          Odotetaan haun valmistumista (haettu data on promise-muuttujassa)
        -->
      {#await artistPromise}
        <div>Ladataan tietoja...</div>
        <div class="loadingIcon"><Pulse size="90" color="gray" unit="px" duration="1s" /></div>
        <!--
          Haun valmistumisen jälkeen näytetään artistin / bändin logo ja biografia
        -->
      {:then data}
        <!-- {#if visible} -->
        <div out:fly={{ y: 300, duration: 1000 }}>
          <Button
            on:click={() => {
              showSearch = true;
            }}>Tee uusi haku</Button
          >
        </div>
        <div in:fade={{ duration: 2000 }} out:fly={{ y: 300, duration: 1000 }}>
          <img id="logo" src={data.strArtistLogo} alt="Logo for band / artist: {data.strArtist}" />
          <div id="biography">{data.strBiographyEN}</div>
        </div>
        <div out:fly={{ y: 300, duration: 1000 }}>
          <Button on:click={fetchVideos}>Hae videoita</Button>
        </div>
        <!-- {/if} -->
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
          Virhe: {error.message}
        </div>
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
    max-width: 850px;
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
    max-width: 80%;
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
</style>
