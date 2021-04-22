<script>
  import { Pulse } from 'svelte-loading-spinners';
  import { fly } from 'svelte/transition';
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
  <div id="videoArea">
    {#each videoData as video}
      <div id="videoBox">
        {#if video.strTrackThumb == null}
          <div>EI KUVAA</div>
        {:else}
          <img src={video.strTrackThumb} alt="Pictures" width="400" height="400" />
        {/if}
        <div>{video.strMusicVid}</div>
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
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    margin: 10px;
  }

  #videoBox {
    border: 3px solid gray;
    margin: 5px;
  }
</style>
