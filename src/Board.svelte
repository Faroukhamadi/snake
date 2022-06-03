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
  let score = 0;
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
          let changed = false;
          if (board[i][j] === 2) {
            score += snakeLength;
            snakeLength++;
            changed = true;
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
          }
          if (changed) {
            let lastIndex = snakeCoordinates.length - 1;
            if (snakeLength <= 2) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  snakeCoordinates[lastIndex][0],
                  snakeCoordinates[lastIndex][1],
                ],
              ];
            } else {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  snakeCoordinates[lastIndex][0],
                  snakeCoordinates[lastIndex][1] + 1,
                ],
              ];
            }
          } else {
            if (snakeLength <= 2) {
              for (let row = 1; row < snakeLength; row++) {
                snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
              }
            } else {
              for (let row = 1; row < snakeLength; row++) {
                if (row === 1) {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
                } else {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1] - 1;
                }
              }
            }
          }
        }
        break;

      // Left

      case 'decrement':
        j--;
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          let changed = false;
          if (board[i][j] === 2) {
            score += snakeLength;
            snakeLength++;
            changed = true;
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
          }
          if (changed) {
            let lastIndex = snakeCoordinates.length - 1;
            if (snakeLength <= 2) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  // the row should remain the same because we're staying at the same line
                  snakeCoordinates[lastIndex][0],
                  snakeCoordinates[lastIndex][1],
                ],
              ];
            } else {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  snakeCoordinates[lastIndex][0],
                  snakeCoordinates[lastIndex][1] - 1,
                ],
              ];
            }
          } else {
            if (snakeLength <= 2) {
              for (let row = 1; row < snakeLength; row++) {
                snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
              }
            } else {
              for (let row = 1; row < snakeLength; row++) {
                if (row === 1) {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
                } else {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1] + 1;
                }
              }
            }
          }
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
          let changed = false;
          if (board[i][j] === 2) {
            score += snakeLength;
            snakeLength++;
            changed = true;
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
          }
          if (changed) {
            let lastIndex = snakeCoordinates.length - 1;
            if (snakeLength <= 2) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  // the row should remain the same because we're staying at the same line
                  snakeCoordinates[lastIndex][0],
                  snakeCoordinates[lastIndex][1],
                ],
              ];
            } else {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  snakeCoordinates[lastIndex][0] + 1,
                  snakeCoordinates[lastIndex][1],
                ],
              ];
            }
          } else {
            if (snakeLength <= 2) {
              for (let row = 1; row < snakeLength; row++) {
                snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
              }
            } else {
              for (let row = 1; row < snakeLength; row++) {
                if (row === 1) {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
                } else {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0] - 1;
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
                }
              }
            }
          }
        }
        break;

      case 'decrement':
        // Up
        i--;
        if (!(i > 9 || i < 0 || j > 9 || j < 0)) {
          let changed = false;
          if (board[i][j] === 2) {
            score += snakeLength;
            snakeLength++;
            changed = true;
            do {
              foodI = generateIndex();
              foodJ = generateIndex();
            } while (foodI === i || foodJ === j || foodI === j || foodJ === i);
            board[foodI][foodJ] = 2;
          }
          if (changed) {
            let lastIndex = snakeCoordinates.length - 1;
            if (snakeLength <= 2) {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  // the row should remain the same because we're staying at the same line
                  snakeCoordinates[lastIndex][0],
                  snakeCoordinates[lastIndex][1],
                ],
              ];
            } else {
              snakeCoordinates = [
                ...snakeCoordinates,
                [
                  snakeCoordinates[lastIndex][0] - 1,
                  snakeCoordinates[lastIndex][1],
                ],
              ];
            }
          } else {
            if (snakeLength <= 2) {
              for (let row = 1; row < snakeLength; row++) {
                snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                // Go back here
                snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
              }
            } else {
              for (let row = 1; row < snakeLength; row++) {
                if (row === 1) {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0];
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
                } else {
                  snakeCoordinates[row][0] = snakeCoordinates[row - 1][0] + 1;
                  snakeCoordinates[row][1] = snakeCoordinates[row - 1][1];
                }
              }
            }
          }
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
      snakeCoordinates[0][0] = i;
      snakeCoordinates[0][1] = j;
      snakeCoordinates = snakeCoordinates;
      // reinitialize matrix to zero
      for (let i = 0; i < 10; i++) {
        for (let j = 0; j < 10; j++) {
          if (board[i][j] !== 2) {
            board[i][j] = 0;
          }
        }
      }
      console.log('clean up');
      board = board;
      snakeCoordinates.forEach((coordinate) => {
        console.log(coordinate);
        board[coordinate[0]][coordinate[1]] = 1;
      });
    }
  }
</script>

<svelte:window on:keydown={(e) => handleKeydown(e)} />

<h2>Score: {score}</h2>
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
  h2 {
    text-align: center;
    text-decoration-line: underline;
    margin-bottom: 0;
    padding-bottom: 0;
  }
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
