<script lang="ts">
  import { onMount } from 'svelte';

  onMount(() => {
    highScore = parseInt(localStorage.getItem('highScore'));
  });

  const generateIndex = (): number => Math.floor(Math.random() * 20);
  const isOutOfBounds = (n: number): boolean => n < 0 || n > 19;
  const generateFoodIndex = (n: number): number => {
    let index: number;
    do {
      index = generateIndex();
    } while (index === n);
    return index;
  };
  const restart = () => {
    highScore = parseInt(localStorage.getItem('highScore')) ?? 0;
    board = [...Array(20)].map(() => [...Array(20)].map(() => 0));
    direction = [0, 1] as [number, number];
    score = 0;

    snakeCoordinates = [[3, 2]];
    board[3][2] = 1;
    lost = false;

    speed = Math.floor(Math.max(100, speedHelper));
    foodI = generateFoodIndex(3);
    foodJ = generateFoodIndex(2);

    board[foodI][foodJ] = 2;
    speed = Math.floor(Math.max(100, speedHelper));
    setTimeout(wrapper, speed);
  };
  const setHighScore = (): number => {
    const highScore = localStorage.getItem('highScore');
    if (highScore === null) {
      console.log('it is null');
      localStorage.setItem('highScore', score.toString());
      return score;
    } else {
      if (score > parseInt(highScore)) {
        localStorage.setItem('highScore', score.toString());
        return score;
      }
    }
  };
  type Direction = 'up' | 'down' | 'left' | 'right';
  const determineDirection = (direction: [number, number]): Direction => {
    let [x, y] = direction;
    if (x === -1 && y === 0) {
      return 'up';
    } else if (x === 1 && y === 0) {
      return 'down';
    } else if (x === 0 && y === -1) {
      return 'left';
    } else if (x === 0 && y === 1) {
      return 'right';
    }
  };
  const handleKeydown = (
    e: KeyboardEvent & {
      currentTarget: EventTarget & Window;
    }
  ) => {
    switch (e.code) {
      case 'ArrowUp':
        if (determineDirection(direction) === 'down') {
          break;
        }
        direction = [-1, 0];
        break;
      case 'ArrowDown':
        if (determineDirection(direction) === 'up') {
          break;
        }
        direction = [1, 0];
        break;
      case 'ArrowLeft':
        if (determineDirection(direction) === 'right') {
          break;
        }
        direction = [0, -1];
        break;
      case 'ArrowRight':
        if (determineDirection(direction) === 'left') {
          break;
        }
        direction = [0, 1];
        break;
      case 'Enter':
        restart();
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
  let board: Cell[][] = [...Array(20)].map(() => [...Array(20)].map(() => 0));
  let direction = [0, 1] as [number, number];
  let score = 0;
  let highScore = setHighScore() ?? 0;

  let snakeCoordinates: Array<[number, number]> = [[3, 2]];
  board[3][2] = 1;
  let lost: boolean = false;
  let speedHelper = 400;
  let speed: number = Math.floor(Math.max(100, speedHelper));
  let foodI: number = generateFoodIndex(3);
  let foodJ: number = generateFoodIndex(2);

  board[foodI][foodJ] = 2;

  const wrapper = (ms: number) => {
    setTimeout(() => {
      // 0 is always the head's coordinate
      const [x, y] = snakeCoordinates[0];
      const [xStep, yStep] = direction;
      const newHead = [x + xStep, y + yStep] as [number, number];
      if (isOutOfBounds(newHead[0]) || isOutOfBounds(newHead[1])) {
        lost = true;
        return;
      }
      snakeCoordinates.forEach(([x, y], index) => {
        if (index !== 0) {
          if (newHead[0] === x && newHead[1] === y) {
            lost = true;
            return;
          }
        }
      });
      if (lost) {
        return;
      }
      if (board[newHead[0]][newHead[1]] === 2) {
        score++;
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
        speedHelper -= 100;
        speed = Math.floor(Math.max(100, speedHelper));
      } else {
        for (let i = snakeCoordinates.length - 1; i >= 0; i--) {
          snakeCoordinates[i] = snakeCoordinates[i - 1];
        }
        snakeCoordinates[0] = newHead;
      }
    }, ms);
    if (lost) {
      highScore = setHighScore();
      return;
    } else {
      setTimeout(wrapper, speed);
    }
  };

  setTimeout(wrapper, speed);

  $: {
    for (let i = 0; i < 20; i++) {
      for (let j = 0; j < 20; j++) {
        if (board[i][j] === 1) {
          board[i][j] = 0;
        }
      }
    }
    snakeCoordinates.forEach(([x, y]) => {
      board[x][y] = 1;
    });
  }
</script>

<svelte:window on:keydown={(e) => handleKeydown(e)} />

<div class="wrapper">
  <div class="board">
    {#each board as row, i}
      {#each row as _, j}
        {#if board[i][j] === 1}
          <div class={`cell ${i * 20 + j}`} style="background-color: red;" />
        {:else if board[i][j] === 2}
          <div class={`cell ${i * 20 + j}`} style="background-color: grey;" />
        {:else}
          <div class={`cell ${i * 20 + j}`} />
        {/if}
      {/each}
    {/each}
  </div>
  <div class="text">
    {#if lost}
      <h1>You lost! Better luck next time</h1>
    {:else}
      <h1>Score: {score}, Highest Score is: {highScore}</h1>
    {/if}
  </div>
</div>

<style>
  h1 {
    position: absolute;
    bottom: 50px;
    text-decoration-line: underline;
  }
  .wrapper {
    height: 70%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .board {
    margin-top: 20px;
    margin-bottom: 0;
    display: grid;
    grid-template-columns: repeat(20, 30px);
    grid-template-rows: repeat(20, 30px);
  }
  .cell {
    margin: 1px;
    border: 1px solid black;
  }
</style>
