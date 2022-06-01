<script lang="ts">
  const handleClick = (
    e: MouseEvent & {
      currentTarget: EventTarget & HTMLDivElement;
    }
  ) => {
    let index = parseInt(e.currentTarget.classList[1]);
    let row = Math.floor(index / 10);
    let col = index % 10;
    board[row][col] = 1;
  };

  let board: number[][] = [];
  for (let i = 0; i < 10; i++) {
    let temp: number[] = [];
    for (let j = 0; j < 10; j++) {
      temp.push(0);
    }
    board.push(temp);
  }

  $: {
    console.log(board);
  }
</script>

<div class="wrapper">
  <div class="board">
    {#each board as row, i}
      {#each row as _, j}
        {#if board[i][j] === 1}
          <div
            class={`cell ${i * 10 + j}`}
            style="background-color: red;"
            on:click={handleClick}
          />
        {:else}
          <div class={`cell ${i * 10 + j}`} on:click={handleClick} />
        {/if}
      {/each}
    {/each}
  </div>
  <!-- <div class="board">
    <div on:click={(e) => handleClick(e)} class="cell 1" />
    <div on:click={(e) => handleClick(e)} class="cell 2" />
    <div on:click={(e) => handleClick(e)} class="cell 3" />
    <div on:click={(e) => handleClick(e)} class="cell 4" />
    <div on:click={(e) => handleClick(e)} class="cell 5" />
    <div on:click={(e) => handleClick(e)} class="cell 6" />
    <div on:click={(e) => handleClick(e)} class="cell 7" />
    <div on:click={(e) => handleClick(e)} class="cell 8" />
    <div on:click={(e) => handleClick(e)} class="cell 9" />
    <div on:click={(e) => handleClick(e)} class="cell 10" />
    <div on:click={(e) => handleClick(e)} class="cell 11" />
    <div on:click={(e) => handleClick(e)} class="cell 12" />
    <div on:click={(e) => handleClick(e)} class="cell 13" />
    <div on:click={(e) => handleClick(e)} class="cell 14" />
    <div on:click={(e) => handleClick(e)} class="cell 15" />
    <div on:click={(e) => handleClick(e)} class="cell 16" />
    <div on:click={(e) => handleClick(e)} class="cell 17" />
    <div on:click={(e) => handleClick(e)} class="cell 18" />
    <div on:click={(e) => handleClick(e)} class="cell 19" />
    <div on:click={(e) => handleClick(e)} class="cell 20" />
    <div on:click={(e) => handleClick(e)} class="cell 21" />
    <div on:click={(e) => handleClick(e)} class="cell 22" />
    <div on:click={(e) => handleClick(e)} class="cell 23" />
    <div on:click={(e) => handleClick(e)} class="cell 24" />
    <div on:click={(e) => handleClick(e)} class="cell 25" />
    <div on:click={(e) => handleClick(e)} class="cell 26" />
    <div on:click={(e) => handleClick(e)} class="cell 27" />
    <div on:click={(e) => handleClick(e)} class="cell 28" />
    <div on:click={(e) => handleClick(e)} class="cell 29" />
    <div on:click={(e) => handleClick(e)} class="cell 30" />
    <div on:click={(e) => handleClick(e)} class="cell 31" />
    <div on:click={(e) => handleClick(e)} class="cell 32" />
    <div on:click={(e) => handleClick(e)} class="cell 33" />
    <div on:click={(e) => handleClick(e)} class="cell 34" />
    <div on:click={(e) => handleClick(e)} class="cell 35" />
    <div on:click={(e) => handleClick(e)} class="cell 36" />
    <div on:click={(e) => handleClick(e)} class="cell 37" />
    <div on:click={(e) => handleClick(e)} class="cell 38" />
    <div on:click={(e) => handleClick(e)} class="cell 39" />
    <div on:click={(e) => handleClick(e)} class="cell 40" />
    <div on:click={(e) => handleClick(e)} class="cell 41" />
    <div on:click={(e) => handleClick(e)} class="cell 42" />
    <div on:click={(e) => handleClick(e)} class="cell 43" />
    <div on:click={(e) => handleClick(e)} class="cell 44" />
    <div on:click={(e) => handleClick(e)} class="cell 45" />
    <div on:click={(e) => handleClick(e)} class="cell 46" />
    <div on:click={(e) => handleClick(e)} class="cell 47" />
    <div on:click={(e) => handleClick(e)} class="cell 48" />
    <div on:click={(e) => handleClick(e)} class="cell 49" />
    <div on:click={(e) => handleClick(e)} class="cell 50" />
    <div on:click={(e) => handleClick(e)} class="cell 51" />
    <div on:click={(e) => handleClick(e)} class="cell 52" />
    <div on:click={(e) => handleClick(e)} class="cell 53" />
    <div on:click={(e) => handleClick(e)} class="cell 54" />
    <div on:click={(e) => handleClick(e)} class="cell 55" />
    <div on:click={(e) => handleClick(e)} class="cell 56" />
    <div on:click={(e) => handleClick(e)} class="cell 57" />
    <div on:click={(e) => handleClick(e)} class="cell 58" />
    <div on:click={(e) => handleClick(e)} class="cell 59" />
    <div on:click={(e) => handleClick(e)} class="cell 60" />
    <div on:click={(e) => handleClick(e)} class="cell 61" />
    <div on:click={(e) => handleClick(e)} class="cell 62" />
    <div on:click={(e) => handleClick(e)} class="cell 63" />
    <div on:click={(e) => handleClick(e)} class="cell 64" />
    <div on:click={(e) => handleClick(e)} class="cell 65" />
    <div on:click={(e) => handleClick(e)} class="cell 66" />
    <div on:click={(e) => handleClick(e)} class="cell 67" />
    <div on:click={(e) => handleClick(e)} class="cell 68" />
    <div on:click={(e) => handleClick(e)} class="cell 69" />
    <div on:click={(e) => handleClick(e)} class="cell 70" />
    <div on:click={(e) => handleClick(e)} class="cell 71" />
    <div on:click={(e) => handleClick(e)} class="cell 72" />
    <div on:click={(e) => handleClick(e)} class="cell 73" />
    <div on:click={(e) => handleClick(e)} class="cell 74" />
    <div on:click={(e) => handleClick(e)} class="cell 75" />
    <div on:click={(e) => handleClick(e)} class="cell 76" />
    <div on:click={(e) => handleClick(e)} class="cell 77" />
    <div on:click={(e) => handleClick(e)} class="cell 78" />
    <div on:click={(e) => handleClick(e)} class="cell 79" />
    <div on:click={(e) => handleClick(e)} class="cell 80" />
    <div on:click={(e) => handleClick(e)} class="cell 81" />
    <div on:click={(e) => handleClick(e)} class="cell 82" />
    <div on:click={(e) => handleClick(e)} class="cell 83" />
    <div on:click={(e) => handleClick(e)} class="cell 84" />
    <div on:click={(e) => handleClick(e)} class="cell 85" />
    <div on:click={(e) => handleClick(e)} class="cell 86" />
    <div on:click={(e) => handleClick(e)} class="cell 87" />
    <div on:click={(e) => handleClick(e)} class="cell 88" />
    <div on:click={(e) => handleClick(e)} class="cell 89" />
    <div on:click={(e) => handleClick(e)} class="cell 90" />
    <div on:click={(e) => handleClick(e)} class="cell 91" />
    <div on:click={(e) => handleClick(e)} class="cell 92" />
    <div on:click={(e) => handleClick(e)} class="cell 93" />
    <div on:click={(e) => handleClick(e)} class="cell 94" />
    <div on:click={(e) => handleClick(e)} class="cell 95" />
    <div on:click={(e) => handleClick(e)} class="cell 96" />
    <div on:click={(e) => handleClick(e)} class="cell 97" />
    <div on:click={(e) => handleClick(e)} class="cell 98" />
    <div on:click={(e) => handleClick(e)} class="cell 99" />
    <div on:click={(e) => handleClick(e)} class="cell 100" />
  </div> -->
</div>

<style>
  .wrapper {
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .board {
    display: grid;
    grid-template-columns: repeat(10, 50px);
    grid-template-rows: repeat(10, 50px);
  }
  .cell {
    /* aspect-ratio: 1; */
    margin: 1px;
    border: 1px solid black;
  }
</style>
