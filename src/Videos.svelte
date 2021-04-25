<script>
  import { Pulse } from 'svelte-loading-spinners';
  import { fade, fly } from 'svelte/transition';
  import artistID from './artistID.js';

  let artistNumber = $artistID; // Asetetaan artistin ID artistNumber-muuttujaan
  //console.log(artistNumber);
  let videoPromise; // Tähän asetetaan haettu data videoista

  // Datan haku, haetaan haeutun artistin / bändin videoita YouTubesta
  const getVideos = async (artistNumber) => {
    console.log(artistNumber);
    const response = await fetch(`https://theaudiodb.com/api/v1/json/1/mvid.php?i=${artistNumber}`);
    if (!response.ok) {
      throw new Error('Haku epäonnistui');
    }
    const data = await response.json();
    videoPromise = data.mvids;
    return videoPromise;
  };

  videoPromise = getVideos(artistNumber); // Suorittaa haun kun moduuli ladataan.
  // TEE TÄMÄ UUSIKSI!
</script>

<!--
  Odotetaan videoiden hakua
-->
{#await videoPromise}
  <div>Ladataan tietoja...</div>
  <div class="loadingIcon"><Pulse size="90" color="gray" unit="px" duration="1s" /></div>
  <!--
    Esitetään jokainen haettu video omassa laatikossaan
    PAITSI ETTEI ESITETÄKÄÄN KOSKA TURVALLISUUSRAJOITUKSET
    Niinpä esitetään vain thumbnail kyseisestä biisistä ja linkki videoon.
    Jos thumbnailia ei ole, esitetään placeholder-kuva
  -->
{:then videoData}
  <div id="videoArea" in:fade={{ duration: 2000 }} out:fly={{ y: 300, duration: 1000 }}>
    {#each videoData as video}
      <div id="videoBox">
        {#if video.strTrackThumb == null}
          <img class="albumPhoto" src="/images/img_not_found.jpg" alt="Placeholder for album pic if one not found" />
        {:else}
          <img class="albumPhoto" src={video.strTrackThumb} alt="Pictures" />
        {/if}
        <a href={video.strMusicVid} id="videolink" target="_blank">{video.strMusicVid}</a>
      </div>
    {/each}
  </div>
  <!--
    Jos haun aikana tapahtui virhe niin näytetään se
  -->
{:catch error}
  <div out:fly={{ y: 300, duration: 1000 }} class="error">
    Virhe: {error.message}
  </div>
{/await}

<style>
  .error {
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    color: darkred;
    font-size: 1.2rem;
  }

  #videoArea {
    margin: 10px;
    display: grid;
    grid-template-columns: 45% 45%;
    align-items: center;
  }

  #videoBox {
    border: 3px solid gray;
    margin: 5px;
    text-align: center;
  }

  .albumPhoto {
    max-width: 100%;
    height: auto;
  }

  #videolink {
    font-size: 0.8em;
  }
</style>
