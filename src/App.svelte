<script lang="ts">
  import Board from './Board.svelte';

  let unique = [{}];

  const restart = () => {
    unique = [{}];
  };
</script>

<main>
  {#each unique as key (key)}
    <Board />
  {/each}
</main>

<svelte:window
  on:keydown={(e) => {
    if (e.code === 'Enter') {
      restart();
    }
  }}
/>

<style>
  main {
    height: 100%;
  }
</style>
