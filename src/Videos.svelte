<script>
  import { Pulse } from 'svelte-loading-spinners';
  import { fade, fly } from 'svelte/transition';
  import Carousel from '@beyonk/svelte-carousel';
	import { ChevronLeftIcon, ChevronRightIcon } from 'svelte-feather-icons';
  import artistID from './artistID.js';

  let artistNumber = $artistID; // Asetetaan artistin ID artistNumber-muuttujaan
  let videoPromise; // Tähän asetetaan haettu data videoista

  // Datan haku, haetaan haeutun artistin / bändin videoita YouTubesta
  const getVideos = async (artistNumber) => {
    //console.log(artistNumber);
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

<div id="videoContainer">
<!--
  Odotetaan videoiden hakua
-->
{#await videoPromise}
<div id="awaitingVideos">
  <p>Ladataan tietoja...</p>
  <div class="loadingIcon"><Pulse size="90" color="gray" unit="px" duration="1s" /></div>
</div>
  <!--
    Esitetään jokainen haettu video omassa laatikossaan
    PAITSI ETTEI ESITETÄKÄÄN KOSKA TURVALLISUUSRAJOITUKSET
    Niinpä esitetään vain thumbnail kyseisestä biisistä ja linkki videoon.
    Jos thumbnailia ei ole, esitetään placeholder-kuva
  -->
{:then videoData}
{#if videoData == null}
<div id="noVideos">
<p class="error">Videoita ei löytynyt</p>
</div>
{:else}
<div id="messageAboutVideosBox">
  <p id="messageAboutVideos">Tässä oli tarkoitus näyttää haetut videot, mutta turvallisuussyistä johtuen se ei ole mahdollista.
    Sveltelle on toki olemassa muutama node-moduuli, jotka toteuttaisivat tuon, mutta en halunnut ottaa niitä käyttöön.
    Näytetään siis linkki videoon ja datan mukana tullut kuva ko. biisille (jos sellainen oli).
  </p>
</div>
  <div id="videoArea" in:fade={{ duration: 2000 }} out:fly={{ y: 300, duration: 1000 }}>

    <Carousel perPage={3}>
      <span class="control" slot="left-control">
        <ChevronLeftIcon />
      </span>
      {#each videoData as video}
      <div id="videoBox" class="slide-content">
        <a href={video.strMusicVid} target="_blank">
        {#if video.strTrackThumb == null}
          <img class="albumPhoto" src="/images/img_not_found.jpg" title="{video.strTrack}" alt="Placeholder for album pic if one not found" />
        {:else}
          <img class="albumPhoto" src={video.strTrackThumb} title="{video.strTrack}" alt="Pictures" />
        {/if}
      </a>
        <p id="songName">{video.strTrack}</p>
      </div>
    {/each}
      <span class="control" slot="right-control">
        <ChevronRightIcon />
      </span>
    </Carousel>

  </div>
  <!--
    Jos haun aikana tapahtui virhe niin näytetään se
  -->
  {/if}
{:catch error}
  <div out:fly={{ y: 300, duration: 1000 }} class="error">
    Virhe: {error.message}
  </div>
{/await}
</div>

<style>
  #awaitingVideos {
    text-align: center;
  }

  .loadingIcon {
    display: inline-block;
    margin-top: 25px;
  }

  .error {
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    color: darkred;
    font-size: 1.2rem;
  }

  #videoArea {
    margin: 10px;
    width: 800px;
  }

  #videoBox {
    margin: 5px;
    text-align: center;
  }

  .albumPhoto {
    max-width: 100%;
    height: auto;
  }

  #songName {
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
  }

  #messageAboutVideosBox {
    border: 2px red dotted;
    padding: 10px;
    margin: 5px;
    text-align: justify;
  }

  #messageAboutVideos {
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    font-size: 1.2rem;
    color: darkred;
  }

  #noVideos {
    margin: 0 auto;
    text-align: center;
  }

  .control :global(svg) {
		width: 100%;
		height: 100%;
		color: black;
		border: 2px solid #fff;
		border-radius: 32px;
    background-color: lightgray;
	}
</style>
