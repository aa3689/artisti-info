<script>
import Info from './Info.svelte';
import Videos from './Videos.svelte';

let showVideos = false; // Määrittää näytetäänkö videot

// Näyttää videot
function toggleVideos() {
  showVideos = true;
  }

// Piilottaa videot
function hideVideos() {
  showVideos = false;
  }

</script>

<main>
  <Info on:getVideos={toggleVideos} on:hideVideos={hideVideos} />
  {#if showVideos}
    <Videos/>
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
