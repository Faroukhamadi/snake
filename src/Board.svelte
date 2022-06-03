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
          <!-- <div class={`cell ${i * 20 + j}`}  /> -->
          <svg xmlns="http://www.w3.org/2000/svg" height="40" width="40"
            ><path
              d="M20 34.417Q17 34.417 14.375 33.292Q11.75 32.167 9.792 30.208Q7.833 28.25 6.708 25.625Q5.583 23 5.583 20Q5.583 17 6.708 14.375Q7.833 11.75 9.792 9.792Q11.75 7.833 14.375 6.708Q17 5.583 20 5.583Q23 5.583 25.625 6.708Q28.25 7.833 30.208 9.792Q32.167 11.75 33.292 14.375Q34.417 17 34.417 20Q34.417 23 33.292 25.625Q32.167 28.25 30.208 30.208Q28.25 32.167 25.625 33.292Q23 34.417 20 34.417Z"
            /></svg
          >
        {:else if board[i][j] === 2}
          <svg id="svg2" viewBox="0 0 780.88 708.9" version="1.0">
            <defs id="defs4">
              <radialGradient
                id="radialGradient5090"
                gradientUnits="userSpaceOnUse"
                cy="454.41"
                cx="-1001.5"
                gradientTransform="matrix(1 0 0 .66768 -11.429 120.1)"
                r="437.85"
              >
                <stop
                  id="stop5086"
                  stop-color="#fff"
                  stop-opacity="0"
                  offset="0"
                />
                <stop
                  id="stop5088"
                  stop-color="#fff"
                  stop-opacity=".42029"
                  offset="1"
                />
              </radialGradient>
              <filter
                id="filter6097"
                y="-.10793"
                width="1.1441"
                x="-.07206"
                height="1.2159"
              >
                <feGaussianBlur
                  id="feGaussianBlur6099"
                  stdDeviation="31.551255"
                />
              </filter>
              <radialGradient
                id="radialGradient6109"
                gradientUnits="userSpaceOnUse"
                cy="377.14"
                cx="-1063.9"
                gradientTransform="matrix(.16736 -2.1541 1.3593 .10561 -41.751 -2064.7)"
                r="119.18"
              >
                <stop id="stop6105" offset="0" />
                <stop id="stop6107" stop-opacity="0" offset="1" />
              </radialGradient>
              <filter
                id="filter7216"
                y="-.12775"
                width="1.2389"
                x="-.11944"
                height="1.2555"
              >
                <feGaussianBlur
                  id="feGaussianBlur7218"
                  stdDeviation="14.175239"
                />
              </filter>
              <linearGradient
                id="linearGradient8213"
                x1="-1071.4"
                gradientUnits="userSpaceOnUse"
                y1="1166.6"
                gradientTransform="translate(1311.5 -123.23)"
                x2="-1025.7"
                y2="626.62"
              >
                <stop id="stop8209" offset="0" />
                <stop id="stop8211" stop-opacity="0" offset="1" />
              </linearGradient>
              <filter
                id="filter9208"
                y="-.10287"
                width="1.08"
                x="-.040011"
                height="1.2057"
              >
                <feGaussianBlur
                  id="feGaussianBlur9210"
                  stdDeviation="18.218013"
                />
              </filter>
              <linearGradient
                id="linearGradient9220"
                x1="-1008.6"
                gradientUnits="userSpaceOnUse"
                y1="309.51"
                gradientTransform="matrix(.55076 0 0 .55076 1170.3 36.165)"
                x2="-1035.2"
                y2="375.07"
              >
                <stop id="stop9216" stop-color="#840b0b" offset="0" />
                <stop
                  id="stop9218"
                  stop-color="#840b0b"
                  stop-opacity="0"
                  offset="1"
                />
              </linearGradient>
              <filter
                id="filter11226"
                y="-.071696"
                width="1.3173"
                x="-.15867"
                height="1.1434"
              >
                <feGaussianBlur
                  id="feGaussianBlur11228"
                  stdDeviation="10.631173"
                />
              </filter>
              <linearGradient
                id="linearGradient11236"
                x1="-977.14"
                gradientUnits="userSpaceOnUse"
                y1="238.08"
                gradientTransform="translate(1311.5 -140.37)"
                x2="-1013.1"
                y2="375.22"
              >
                <stop id="stop11232" offset="0" />
                <stop id="stop11234" stop-opacity="0" offset="1" />
              </linearGradient>
              <filter
                id="filter13334"
                y="-.36093"
                width="1.4354"
                x="-.21772"
                height="1.7219"
              >
                <feGaussianBlur
                  id="feGaussianBlur13336"
                  stdDeviation="7.726234"
                />
              </filter>
              <filter
                id="filter17435"
                y="-.36982"
                width="1.1365"
                x="-.068245"
                height="1.7396"
              >
                <feGaussianBlur
                  id="feGaussianBlur17437"
                  stdDeviation="22.719784"
                />
              </filter>
            </defs>
            <g id="layer1" transform="translate(-165.32 -57.227)">
              <path
                id="path16377"
                opacity=".56379"
                filter="url(#filter17435)"
                transform="matrix(1.0161 0 0 2.2731 100.61 -965.16)"
                d="m510.08 713.29s-6.5-1.44-18.13-5.06c-15.38 0.84-31.04 1.54-46.9 2.08-183.43 6.26-332.78-27.11-332.78-60.51s158.65-59.38 342.34-59.38 323.22 25.98 323.22 59.38c6.59 23.31-109.17 46.71-258.38 56.75 2.11 0.31 4.28 0.6 6.5 0.87-0.11 0.03-0.22 0.07-0.34 0.1 1.01 0.12 1.69 0.18 1.69 0.18l-17.22 5.59z"
              />
              <path
                id="path2170"
                fill="#f00"
                d="m893.34 328.58c0 141.15-114.8 250.99-265.94 250.99s-281.67-109.84-281.67-250.99 122.88-282.16 273.81-255.71c150.66 26.41 280.1 141.31 273.8 255.71z"
              />
              <path
                id="path3144"
                d="m-1268.6 135.22s-142.8 48.57-157.1 228.57 128.6 331.43 291.4 257.14c162.87-74.28 225.73 17.15 328.59 31.43 102.85 14.29 305.71-82.85 242.85-225.71-62.85-142.86-48.57-160-180-245.72-131.43-85.711-277.04-115.93-337.14-111.42-114.3 8.566-188.6 65.71-188.6 65.71z"
                fill-rule="evenodd"
                transform="matrix(.55076 0 0 .42609 1170.3 44.776)"
                filter="url(#filter6097)"
                fill="url(#radialGradient5090)"
              />
              <path
                id="path8189"
                d="m-134.24 571.99c7.94 97.92 205.65 280.53 394.28 285.71 371.73 10.06 481.54-185.3 514.29-311.42 30.56-117.69-303.71 162.47-556.79 123.42-211.62-32.65-357.01-283.96-351.78-97.71z"
                fill-rule="evenodd"
                transform="matrix(.55076 0 0 .55076 447.99 113.48)"
                filter="url(#filter9208)"
                fill="url(#linearGradient8213)"
              />
              <path
                id="path6101"
                opacity=".66667"
                d="m77.186 134.85s160.59 118.61 202.85 122.85c50.42 5.06 222.86-54.28 220-88.57-2.85-34.28-162.85-171.43-225.71-157.14-62.86 14.286-200 94.29-197.14 122.86z"
                fill-rule="evenodd"
                transform="matrix(.55076 0 0 .55076 447.99 113.48)"
                filter="url(#filter7216)"
                fill="url(#radialGradient6109)"
              />
              <path
                id="path9212"
                fill-rule="evenodd"
                fill="url(#linearGradient9220)"
                d="m600.65 233.38s23.61-91.27 42.49-125.89c18.88-34.619 29.9-47.207 29.9-47.207l14.16 23.604s-40.91 20.453-56.65 88.123c-15.73 67.66-14.16 73.96-18.88 69.24-4.72-4.73-12.59-7.87-11.02-7.87z"
              />
              <path
                id="path10191"
                d="m414.19-79.215c-29.19 41.06-53.54 85.594-69.87 133.44-17.98 50.125-32.07 101.6-45.97 153.02 4.78 2.52 9.68 6.77 13.72 9.11 5.8-21.87 9.24-44.44 14.53-66.5 9.99-48.37 20.45-97.909 47.5-140.01 14.95-24.057 34.42-46.427 58.25-61.43-6.45-8.993-11.56-19.151-17.44-28.625l-0.58 0.812-0.14 0.188z"
                fill-rule="evenodd"
                transform="matrix(.55076 0 0 .55076 447.99 113.48)"
                filter="url(#filter11226)"
                fill="url(#linearGradient11236)"
              />
              <path
                id="path11238"
                filter="url(#filter13334)"
                fill-rule="evenodd"
                transform="matrix(.55076 0 0 .55076 447.99 113.48)"
                d="m278.96 172.06c-7.65 5.87-10.76 34.32-5.59 40.76 4.93 7.79 10.92 16.8 20.84 13.25 15.43-5.7 34.33-17.23 43.32-30.25-14.32 7.14-29.42 17-45.94 16.44-9.12-0.36-14.71-11.34-13.1-19.44l0.47-20.76z"
              />
            </g>
            <metadata>
              <rdf:RDF>
                <cc:Work>
                  <dc:format>image/svg+xml</dc:format>
                  <dc:type
                    rdf:resource="http://purl.org/dc/dcmitype/StillImage"
                  />
                  <cc:license
                    rdf:resource="http://creativecommons.org/licenses/publicdomain/"
                  />
                  <dc:publisher>
                    <cc:Agent rdf:about="http://openclipart.org/">
                      <dc:title>Openclipart</dc:title>
                    </cc:Agent>
                  </dc:publisher>
                  <dc:title>Apple Red</dc:title>
                  <dc:date>2007-02-11T11:14:42</dc:date>
                  <dc:description
                    >apple, apple, clip art, clipart, food, food, fruit, fruit,
                    image, media, png, public domain, red, red, svg,
                  </dc:description>
                  <dc:source
                    >http://openclipart.org/detail/3176/apple-red-by-valessiobrito-3176</dc:source
                  >
                  <dc:creator>
                    <cc:Agent>
                      <dc:title>valessiobrito</dc:title>
                    </cc:Agent>
                  </dc:creator>
                  <dc:subject>
                    <rdf:Bag>
                      <rdf:li>apple</rdf:li>
                      <rdf:li>clip art</rdf:li>
                      <rdf:li>clipart</rdf:li>
                      <rdf:li>food</rdf:li>
                      <rdf:li>fruit</rdf:li>
                      <rdf:li>image</rdf:li>
                      <rdf:li>media</rdf:li>
                      <rdf:li>png</rdf:li>
                      <rdf:li>public domain</rdf:li>
                      <rdf:li>red</rdf:li>
                      <rdf:li>svg</rdf:li>
                    </rdf:Bag>
                  </dc:subject>
                </cc:Work>
                <cc:License
                  rdf:about="http://creativecommons.org/licenses/publicdomain/"
                >
                  <cc:permits
                    rdf:resource="http://creativecommons.org/ns#Reproduction"
                  />
                  <cc:permits
                    rdf:resource="http://creativecommons.org/ns#Distribution"
                  />
                  <cc:permits
                    rdf:resource="http://creativecommons.org/ns#DerivativeWorks"
                  />
                </cc:License>
              </rdf:RDF>
            </metadata>
          </svg>
        {:else}
          <div class={`cell ${i * 20 + j}`} />
        {/if}
      {/each}
    {/each}
  </div>
  <div class="text">
    {#if lost}
      <h1>You lost! Better luck next time, Press ENTER to restart</h1>
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
    margin-top: 30px;
    margin-bottom: 0;
    display: grid;
    grid-template-columns: repeat(20, 30px);
    grid-template-rows: repeat(20, 30px);
    border: 3px solid blue;
  }
  .cell {
    margin: 1px;
  }
</style>
