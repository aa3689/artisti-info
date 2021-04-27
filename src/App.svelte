<script>
import Info from './Info.svelte';
import Videos from './Videos.svelte';
import ExtraInfo from './ExtraInfo.svelte';

let showVideos = false; // Määrittää näytetäänkö videot
$: extraInfo = false; // Määrittää näytetäänkö lisätietoja artistista / bändistä

// Näyttää videot
function toggleVideos() {
  showVideos = true;
  }

// Piilottaa videot
function hideVideos() {
  showVideos = false;
  }

// Näyttää lisätiedot
function showExtraInfo() {
  extraInfo = true;
}

// Piilottaa lisätiedot
function hideExtraInfo() {
  extraInfo = false;
}

</script>

<main>
  <Info on:getVideos={toggleVideos} on:hideVideos={hideVideos} on:showExtraInfo={showExtraInfo} />
  {#if showVideos}
    <Videos/>
  {/if}
  {#if extraInfo}
  <ExtraInfo on:hideExtraInfo={hideExtraInfo}/>
  {/if}
</main>

<style>
  main {
    display: grid;
    grid-template-columns: 800px;
    justify-content: center;
    margin: 50px auto;
  }

  @media (max-width: 810px) {
    main {
      grid-template-columns: 650px;
    }
  }

  @media (max-width: 660px) {
    main {
      grid-template-columns: 500px;
    }
  }
</style>
