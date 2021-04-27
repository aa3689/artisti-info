<script>
  import Modal from './Modal.svelte';
  import Button from './Button.svelte';
  import artistData from './artistData.js';
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();
  const hideExtraInfo = () => dispatch('hideExtraInfo');
</script>

<Modal>
  <div id="extraInfoBox">
    <p>
      Perustamisvuosi:
      {#if $artistData.intFormedYear.length < 1}
        Ei tiedossa
      {:else}
        {$artistData.intFormedYear}
      {/if}
    </p>
    <p>
      Genre:
      {#if $artistData.strGenre.length < 1}
        Ei tiedossa
      {:else}
        {$artistData.strGenre}
      {/if}
    </p>
    <p>
      Jäseniä:
      {#if $artistData.intMembers.length < 1}
        Ei tiedossa
      {:else}
        {$artistData.intMembers}
      {/if}
    </p>
    <p>
      Kotoisin:
      {#if $artistData.strCountry.length < 1}
        Ei tiedossa
      {:else}
        {$artistData.strCountry}
      {/if}
    </p>
  </div>

  <div id="closeModal">
    <Button on:click={hideExtraInfo}>Sulje</Button>
  </div>
</Modal>

<style>
  #extraInfoBox {
    font-family: 'Bookman Old Times', 'Times New Roman', serif;
    font-size: 1.2rem;
  }

  #closeModal {
    text-align: center;
  }
</style>
