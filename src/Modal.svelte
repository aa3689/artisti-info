<div class="backdrop" on:click />

<div class="modal">
  <slot />
</div>

<style>
  .backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: rgba(95, 90, 90, 0.75);
    z-index: 10;
  }

  .modal {
    padding: 1rem;
    position: fixed;
    top: 50%;
    left: 50%;
    margin-top: -125px; /* Negative half of height. Source:                                                                    */
    margin-left: -200px; /* Negative half of width. https://stackoverflow.com/questions/2005954/center-a-positionfixed-element */
    width: 400px;
    height: 250px;
    background: rgb(255, 255, 255);
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    overflow: hidden;
  }
</style>
