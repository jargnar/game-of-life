<script>
  export const name = 'GAME OF LIFE';

	const NUM_CELL_COLS = 100;
  const NUM_CELL_ROWS = 100;
  
  const ALIVE = 'lime';
  const DEAD = '#333';

  export let generation = 0;
  export let cells = {}

  for (let i = 0; i < NUM_CELL_COLS; i++) cells[i] = {};
  for (let i = 0; i < NUM_CELL_COLS; i++) {
    for (let j = 0; j < NUM_CELL_ROWS; j++) {
      cells[i][j] = {status: DEAD, goodNeighbours: 0};
    }
  }

  let layout = '';
  for (let j = 0; j < NUM_CELL_ROWS; j++) {
    layout += '"' + 'x '.repeat(NUM_CELL_COLS) + '"\n';
  }

  function updateGoodNeighbours(i, j) {
    let goodNeighbours = 0;
    if (i-1 in cells && j-1 in cells[i-1])
      goodNeighbours += cells[i-1][j-1].status == ALIVE ? 1 : 0;

    if (i-1 in cells && j in cells[i-1])
      goodNeighbours += cells[i-1][j].status == ALIVE ? 1 : 0;

    if (i-1 in cells && j+1 in cells[i-1])
      goodNeighbours += cells[i-1][j+1].status == ALIVE ? 1 : 0;
    
    if (j-1 in cells[i])
      goodNeighbours += cells[i][j-1].status == ALIVE ? 1 : 0;
    
    if (j+1 in cells[i])
      goodNeighbours += cells[i][j+1].status == ALIVE ? 1 : 0;

    if (i+1 in cells && j-1 in cells[i+1])
      goodNeighbours += cells[i+1][j-1].status == ALIVE ? 1 : 0;
    
    if (i+1 in cells && j in cells[i+1])
      goodNeighbours += cells[i+1][j].status == ALIVE ? 1 : 0;
    
    if (i+1 in cells && j+1 in cells[i+1])
      goodNeighbours += cells[i+1][j+1].status == ALIVE ? 1 : 0;
  
    cells[i][j].goodNeighbours = goodNeighbours;
  }


  function updateSeed(i, j) {
    cells[i][j].status = ALIVE;
    for (let a = 0; a < NUM_CELL_COLS; a++) {
      for (let b = 0; b < NUM_CELL_ROWS; b++) {
        updateGoodNeighbours(a, b);
      }
    }
    updateGoodNeighbours(i, j);
  }

  function updateCellsInASingleGeneration() {

    for (let i = 0; i < NUM_CELL_COLS; i++) {
      for (let j = 0; j < NUM_CELL_ROWS; j++) {
        let s = cells[i][j].status
        let g = cells[i][j].goodNeighbours;

        if (s == DEAD && g == 3)
          cells[i][j].status = ALIVE;
        else if (s == ALIVE && (g == 2 || g == 3))
          cells[i][j].status = ALIVE;
        else
          cells[i][j].status = DEAD;
      }
    }

    for (let i = 0; i < NUM_CELL_COLS; i++) {
      for (let j = 0; j < NUM_CELL_ROWS; j++) {
        updateGoodNeighbours(i, j);
      }
    }
  
    generation += 1
    console.log(cells);
  }

  function simulate() {
    for (let g = 0; g < 1000; g++) {
        setTimeout( updateCellsInASingleGeneration, 200 * g);
    }
  }
</script>

<main>
<div class="container-fluid">

  <nav class="navbar navbar-dark bg-dark">
    <div class="container-fluid">
      {#if generation === 0}
        <span>
          Conway's Game Of Life, start with a seed and then click simulate...
        </span>
        <button class="btn btn-sm btn-outline-info" on:click={simulate}>Simulate</button>
      {:else}
        <p>Generation: {generation}, refresh page to reset...</p>
        <button class="btn btn-sm btn-outline-danger" on:click={() => location.reload()}>Reset</button>
      {/if}
    </div>
  </nav>

  <div id="grid" style="--layout: {layout}">
    {#each Array(NUM_CELL_COLS) as _, i}
        {#each Array(NUM_CELL_ROWS) as _, j}
          <div 
            id="cell-{i}-{j}" 
            style="background: {cells[i][j].status}; color: {cells[i][j].status == ALIVE ? '#ccc' : 'rgb(243, 233, 91)'};"
            on:click={() => {updateSeed(i, j)}}>
              
          </div>
        {/each}
    {/each}
  </div>
  </div>
</main>

<style>
  #meta {
    top: 0;
    width: 100%;
    background:#222;
  }
  #grid {
    min-height: 10px;
    min-width: 10px;
    display: grid;
    gap: 1px;
    grid-auto-columns: auto;
    grid-template-areas: var(--layout);
  }

  #grid > div {
    min-width: 10px;
    min-height: 10px;
  }
</style>