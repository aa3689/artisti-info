<script>
  import Info from './Info.svelte';
  import Videos from './Videos.svelte';

  let showVideos = false;

  function toggleVideos() {
    showVideos = true;
    //console.log(showVideos)
  }

  function hideVideos() {
    showVideos = false;
    //console.log("hideVideos-funktio: " + showVideos)
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

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
