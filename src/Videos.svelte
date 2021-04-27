<script>
  import { Pulse } from 'svelte-loading-spinners';
  import { fade, fly } from 'svelte/transition';
  import Carousel from '@beyonk/svelte-carousel';
  import { ChevronLeftIcon, ChevronRightIcon } from 'svelte-feather-icons';
  import { onMount } from 'svelte';
  import artistData from './artistData.js';

  let artistID = $artistData.idArtist; // Asetetaan artistin ID-numero tähän
  let videoPromise; // Asetetaan haettu data videoista tähän

  // Datan haku, parametrina haetun artistin / bändin ID-numero
  // Haetaan ko. artistin / bändin videoita YouTubesta
  const getVideos = async (artistID) => {
    const response = await fetch(`https://theaudiodb.com/api/v1/json/1/mvid.php?i=${artistID}`);
    const data = await response.json();
    videoPromise = data.mvids;
    return videoPromise;
  };

  onMount(() => {
    videoPromise = getVideos(artistID);
  });
</script>

<!--
  Odotetaan videoiden hakua
-->
<div id="videoContainer">
  {#await videoPromise}
    <div id="awaitingVideos">
      <p>Ladataan tietoja...</p>
      <div class="loadingIcon"><Pulse size="90" color="gray" unit="px" duration="1s" /></div>
    </div>
  {:then videoData}
    <!--
  Jos palvelu ei löytänyt videoita
  niin esitetään viesti siitä
-->
    {#if videoData == null}
      <div id="noVideos">
        <p class="error">Videoita ei löytynyt</p>
      </div>

      <!--
  Muussa tapauksessa esitetään viesti
  koskien videoita sekä itse "videot"
-->
    {:else}
      <div id="messageAboutVideosBox">
        <p id="messageAboutVideos">
          Tässä oli tarkoitus näyttää haetut videot, mutta turvallisuusrajoitteista johtuen se ei vaikuttanut kovin
          helpolta toteuttaa (avainsana mahd. <a
            href="https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#cross-origin_network_access"
            target="_blank">Cross-origin network access</a
          >). Sveltelle on toki olemassa muutama node-moduuli, jotka toteuttaisivat tuon, mutta en halunnut ottaa niitä
          käyttöön. Näytetään siis linkki videoon ja datan mukana tullut kuva ko. biisille (jos sellainen oli).
        </p>
      </div>

      <!--
  Karuselli, jossa esitetään haetut "videot"
-->
      <div id="videoArea" in:fade={{ duration: 2000 }} out:fly={{ y: 300, duration: 1000 }}>
        <Carousel perPage={{ 800: 3, 650: 2 }}>
          <span class="control" slot="left-control">
            <ChevronLeftIcon />
          </span>
          {#each videoData as video}
            <div id="videoBox" class="slide-content">
              <a href={video.strMusicVid} target="_blank">
                {#if video.strTrackThumb == null}
                  <img
                    class="albumPhoto"
                    src="/images/img_not_found.jpg"
                    title={video.strTrack}
                    alt="Varakuva jos biisin kuvaa ei saatavilla"
                  />
                {:else}
                  <img class="albumPhoto" src={video.strTrackThumb} title={video.strTrack} alt="Biisin kuva" />
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
    {/if}
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

  #videoBox {
    margin: 5px;
    text-align: center;
  }

  #videoArea {
    max-width: 95%;
    margin: 0 auto;
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
