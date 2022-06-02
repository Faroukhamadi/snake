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
  let snakeCoordinates: number[][] = [[3, 2]];
  // console.log('snake coordinates: ', snakeCoordinates);
  let snakeLength = 1;
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
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodI] = 2;
            snakeLength++;
          }
          if (snakeLength > 1) {
            for (let row = 1; row < snakeLength; row++) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [snakeCoordinates[row - 1][0], snakeCoordinates[row - 1][1]],
              ];
            }
          }
          if (snakeLength > 1) {
            for (let row = 1; row < snakeLength; row++) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [snakeCoordinates[row - 1][0], snakeCoordinates[row - 1][1]],
              ];
            }
          }

          board[i][j - snakeLength] = 0;
        }
        break;
      // Left
      case 'decrement':
        j--;
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          let changed = false;
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodI] = 2;
            snakeLength++;
            console.log('increasing snake length');
            changed = true;
            // If we eat: we add a coordinate and shift the tail
            // If we don't we just shift the tail
          }
          if (changed) {
            for (let row = 1; row < snakeLength; row++) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [snakeCoordinates[row - 1][0], snakeCoordinates[row - 1][1]],
              ];
            }
          } else {
            for (let row = 1; row < snakeLength; row++) {
              snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
              snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
            }
          }
          console.log(snakeCoordinates);
        }
        break;
      default:
        break;
    }

    switch (iState) {
      case 'increment':
        // Down
        i++;
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
            snakeLength++;
          }
          board[i - snakeLength][j] = 0;
        }
        break;

      case 'decrement':
        // Up
        i--;
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          if (board[i][j] === 2) {
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
            snakeLength++;
          }
          if (snakeLength > 1) {
            for (let row = 1; row < snakeLength; row++) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [snakeCoordinates[row - 1][0], snakeCoordinates[row - 1][1]],
              ];
            }
          }
          board[i + snakeLength][j] = 0;
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
      // board[i][j] = 1;
      snakeCoordinates[0][0] = i;
      snakeCoordinates[0][1] = j;
      snakeCoordinates = snakeCoordinates;
      // TODO: reinitialize matrix to zero
      for (let i = 0; i < 10; i++) {
        for (let j = 0; j < 10; j++) {
          if (board[i][j] !== 2) {
            board[i][j] = 0;
          }
        }
      }
      board = board;
      // console.log('board before');
      // board.forEach((element) => {
      //   console.log(element);
      // });
      snakeCoordinates.forEach((coordinate) => {
        // console.log('snake coordinate: ', coordinate);
        board[coordinate[0]][coordinate[1]] = 1;
      });
      // console.log('board after');
      // board.forEach((element) => {
      //   console.log(element);
      // });
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
