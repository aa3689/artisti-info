<script>
  import { Pulse } from 'svelte-loading-spinners';
  import { fade, fly } from 'svelte/transition';
  import { flip } from 'svelte/animate';
  import Button from './Button.svelte';

  let visible = true;
  let artistInput;
  let showInstructions = true;
  let promise;

  const getArtist = async (artist) => {
    const response = await fetch(`https://www.theaudiodb.com/api/v1/json/1/search.php?s=${artist}`);
    if (!response.ok) {
      throw new Error('Haku epäonnistui');
    }
    const data = await response.json();
    return data.artists[0];
  };
</script>

<main>
  {#if showInstructions}
    <div in:fade={{ duration: 1000 }}>
      <h1>Artisti-info</h1>
      <input type="text" placeholder="Artistin tai bändin nimi" bind:this={artistInput} />
      <Button
        on:click={() => {
          showInstructions = false;
          promise = getArtist(artistInput.value);
        }}>Hae</Button
      >
    </div>
  {/if}

  <div id="infoBox">
    {#if showInstructions}
      <div />
    {:else}
      {#await promise}
        <div>Ladataan tietoja...</div>
        <div class="loadingIcon"><Pulse size="90" color="gray" unit="px" duration="1s" /></div>
      {:then data}
        {#if visible}
          <div out:fly={{ y: 300, duration: 1000 }}>
            <Button
              on:click={() => {
                showInstructions = true;
              }}>Tee uusi haku</Button
            >
          </div>
          <div in:fade={{ duration: 2000 }} out:fly={{ y: 300, duration: 1000 }}>
            <img id="logo" src={data.strArtistLogo} alt="Logo for band / artist: {data.strArtist}" />
            <div id="biography">{data.strBiographyEN}</div>
          </div>
        {/if}
      {:catch error}
        <div out:fly={{ y: 300, duration: 1000 }}>
          <Button
            on:click={() => {
              showInstructions = true;
            }}>Tee uusi haku</Button
          >
        </div>
        <div out:fly={{ y: 300, duration: 1000 }} class="error">
          Virhe: {error.message}
        </div>
      {/await}
    {/if}
  </div>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 50px auto;
  }

  h1 {
    color: gray;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
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
    margin: 0 auto;
    max-width: 75%;
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    font-size: 1.2rem;
    margin-top: 10px;
  }

  .error {
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    color: darkred;
    font-size: 2rem;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
