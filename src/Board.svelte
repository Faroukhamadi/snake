<script lang="ts">
  enum Step {
    NONE = 'none',
    INCREMENT = 'increment',
    DECREMENT = 'decrement',
  }

  const generateIndex = () => Math.floor(Math.random() * 10);

  const handleKeydown = (
    e: KeyboardEvent & {
      currentTarget: EventTarget & Window;
    }
  ) => {
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
  };

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
  let i = 3;
  let j = 2;
  board[i][j] = 1;
  let lost = false;

  let jState = Step.INCREMENT;
  let iState: Step;
  let foodI: number = 5;
  let foodJ: number = 5;
  board[foodI][foodJ] = 2;

  let clear: NodeJS.Timeout = setInterval(() => {
    switch (jState) {
      // Right
      case 'increment':
        j++;
        console.log('jState1: ', jState);
        console.log('iState1: ', iState);
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodI] = 2;
          }
          board[i][j - 1] = 0;
        }
        break;
      // Left
      case 'decrement':
        j--;
        console.log('jState2: ', jState);
        console.log('iState2: ', iState);
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodI] = 2;
          }
          board[i][j + 1] = 0;
        }
        break;
      default:
        break;
    }

    switch (iState) {
      case 'increment':
        // Down
        i++;
        console.log('iState1: ', iState);
        console.log('jState1: ', jState);
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
          }
          board[i - 1][j] = 0;
        }
        break;
      case 'decrement':
        // Up
        i--;
        console.log('iState2: ', iState);
        console.log('jState2: ', jState);
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
          }
          board[i + 1][j] = 0;
        }

        break;
      default:
        break;
    }
  }, 100);

  $: {
    if (i > 9 || i < 0 || j > 9 || j < 0) {
      clearInterval(clear);
      lost = true;
    } else {
      board[i][j] = 1;
    }
  }
</script>

<svelte:window on:keydown={(e) => handleKeydown(e)} />

<div class="wrapper">
  <div class="board">
    {#each board as row, i}
      {#each row as _, j}
        {#if board[i][j] === 1}
          <div class={`cell ${i * 10 + j}`} style="background-color: red;" />
        {:else if board[i][j] === 2}
          <div class={`cell ${i * 10 + j}`} style="background-color: grey;" />
        {:else}
          <div class={`cell ${i * 10 + j}`} />
        {/if}
      {/each}
    {/each}
  </div>
  {#if lost}
    <h1>You lost! Better luck next time</h1>
  {/if}
</div>

<style>
  h1 {
    position: absolute;
    bottom: 50px;
  }
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
