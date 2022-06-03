<script lang="ts">
  const generateIndex = (): number => Math.floor(Math.random() * 10);
  const isOutOfBounds = (n: number): boolean => n < 0 || n > 9;
  const generateFoodIndex = (n: number): number => {
    let index: number;
    do {
      index = generateIndex();
    } while (index === n);
    return index;
  };

  const handleKeydown = (
    e: KeyboardEvent & {
      currentTarget: EventTarget & Window;
    }
  ) => {
    switch (e.code) {
      case 'ArrowUp':
        direction = [-1, 0];
        break;
      case 'ArrowDown':
        direction = [1, 0];
        break;
      case 'ArrowLeft':
        direction = [0, -1];
        break;
      case 'ArrowRight':
        direction = [0, 1];
        break;
      default:
        break;
    }
  };
  const different = (
    arr: [number, number],
    foodI: number,
    foodJ: number
  ): boolean => {
    return arr[0] !== foodI && arr[1] !== foodJ;
  };

  type Cell = 0 | 1 | 2;
  let board: Cell[][] = [...Array(10)].map(() => [...Array(10)].map(() => 0));
  let direction = [0, 1];
  let score = 0;

  let snakeCoordinates: Array<[number, number]> = [[3, 2]];
  board[3][2] = 1;
  let lost = false;

  let speed = Math.floor(Math.max(400, snakeCoordinates.length * 100));
  let foodI: number = generateFoodIndex(3);
  let foodJ: number = generateFoodIndex(2);
  board[foodI][foodJ] = 2;
  const wrapper = (ms: number) => {
    let clear: NodeJS.Timer = setInterval(() => {
      // 0 is always the head's coordinate
      const [x, y] = snakeCoordinates[0];
      const [xStep, yStep] = direction;
      const newHead = [x + xStep, y + yStep] as [number, number];
      if (isOutOfBounds(newHead[0]) || isOutOfBounds(newHead[1])) {
        lost = true;
        return;
      }
      if (board[newHead[0]][newHead[1]] === 2) {
        snakeCoordinates = [newHead, ...snakeCoordinates];
        let repeat = false;
        do {
          repeat = false;
          foodI = generateIndex();
          foodJ = generateIndex();
          for (let i = 0; i < snakeCoordinates.length; i++) {
            if (!different(snakeCoordinates[i], foodI, foodJ)) {
              repeat = true;
            }
          }
        } while (repeat);
        board[foodI][foodJ] = 2;
      } else {
        for (let i = snakeCoordinates.length - 1; i >= 1; i--) {
          snakeCoordinates[i] = snakeCoordinates[i - 1];
        }
        snakeCoordinates[0] = newHead;
      }
    }, ms);
    if (lost) {
      console.log('user lost, clearing interval...');
      clearInterval(clear);
    }
  };

  wrapper(speed);

  $: {
    for (let i = 0; i < 10; i++) {
      for (let j = 0; j < 10; j++) {
        if (board[i][j] === 1) {
          board[i][j] = 0;
        }
      }
    }
    snakeCoordinates.forEach(([x, y]) => {
      board[x][y] = 1;
    });
    speed = Math.floor(Math.max(400, snakeCoordinates.length * 100));
    console.log('speed is: ', speed);
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
  {:else}
    <h1>Score: {score}</h1>
  {/if}
</div>

<style>
  h1 {
    position: absolute;
    bottom: 50px;
    text-decoration-line: underline;
  }
  .wrapper {
    height: 80%;
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
