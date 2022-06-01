<script lang="ts">
  enum Step {
    NONE = 'none',
    INCREMENT = 'increment',
    DECREMENT = 'decrement',
  }

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
  board[3][2] = 1;
  let i = 3;
  let j = 2;
  let jState = Step.INCREMENT;
  let iState: Step;

  let clear: NodeJS.Timeout = setInterval(() => {
    switch (jState) {
      case 'increment':
        j++;
        board[i][j - 1] = 0;
        break;
      case 'decrement':
        j--;
        board[i][j + 1] = 0;
      default:
        break;
    }

    switch (iState) {
      case 'increment':
        i++;
        board[i - 1][j] = 0;
        break;
      case 'decrement':
        i--;
        board[i + 1][j] = 0;
      default:
        break;
    }

    console.log('ticking');
  }, 200);

  $: {
    if (i >= 9 || j >= 9 || i <= 0 || j < 0) {
      clearInterval(clear);
    }
    board[i][j] = 1;
  }
</script>

<svelte:window
  on:keydown={(e) => {
    switch (e.code) {
      case 'ArrowUp':
        iState = Step.DECREMENT;
        jState = Step.NONE;
        break;
      case 'ArrowDown':
        iState = Step.INCREMENT;
        jState = Step.NONE;
        break;
      case 'ArrowLeft':
        iState = Step.NONE;
        jState = Step.DECREMENT;
        break;
      case 'ArrowRight':
        iState = Step.NONE;
        jState = Step.INCREMENT;
        break;
      default:
        break;
    }
  }}
/>

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
    margin: 1px;
    border: 1px solid black;
  }
</style>
