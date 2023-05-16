<script>

    import {onMount} from 'svelte';

    let patterns = [
        {id: 1, text: `--Patterns--`},
        {id: 2, text: `Cloverleaf`},
        {id: 3, text: `something`},

    ];

    let selected;
    let rows = 25
    let cols = 25
    let grid = []
    let nextGrid = []
    let ALIVE = 'cell alive'
    let DEAD = 'cell dead'
    let intervalId
    let gameOn = false
    let speed


    onMount(async () => {
        await createGameField()
        await createGrids()
    })

    function createGameField() {
        const table = document.getElementById("gameField")
        for (let i = 0; i < rows; i++) {
            const row = table.insertRow()
            for (let j = 0; j < cols; j++) {
                const cell = row.insertCell()
                cell.setAttribute('class', DEAD)
                cell.onclick = cellClick
                cell.setAttribute('id', i + '_' + j)
            }
        }
    }

    function createGrids() {
        for (let i = 0; i < rows; i++) {
            grid [i] = []
            nextGrid [i] = []
            for (let j = 0; j < cols; j++) {
                grid[i][j] = 0
                nextGrid [i][j] = 0
            }
        }
    }

    function cellClick() {
        let cellClass = this.getAttribute('class')
        let rowCol = this.id.split('_')
        let row = rowCol[0]
        let col = rowCol[1]

        if (cellClass === 'cell alive') {
            this.setAttribute('class', DEAD)
            grid[row][col] = 0
        } else {
            this.setAttribute('class', ALIVE)
            grid[row][col] = 1
        }
    }

    async function createNextGeneration() {
        for (let i = 0; i < rows; i++) {
            for (let j = 0; j < cols; j++) {
                setDeadOrAlive(i, j)
            }
        }
        await copyAndResetGrid()
        await updateView()
    }


    function setDeadOrAlive(row, col) {
        let numOfNeighbours = countNeighbours(row, col)
        if (grid[row][col] === 1) {
            if (numOfNeighbours === 2 || numOfNeighbours === 3) {
                nextGrid[row][col] = 1
            } else if (numOfNeighbours < 2) {
                nextGrid[row][col] = 0
            } else if (numOfNeighbours > 3) {
                nextGrid[row][col] = 0
            }
        } else if (grid[row][col] === 0) {
            if (numOfNeighbours === 3) {
                nextGrid[row][col] = 1
            }
        }
    }

    function countNeighbours(row, col) {
        let count = 0
        for (let i = row - 1; i <= row + 1; i++) {
            for (let j = col - 1; j <= col + 1; j++) {
                if (i === row && j === col) continue
                if (i >= 0 && i < rows && j >= 0 && j < cols && grid[i][j] === 1) {
                    count++
                }
            }
        }
        return count
    }

    function copyAndResetGrid() {
        for (let i = 0; i < rows; i++) {
            for (let j = 0; j < cols; j++) {
                grid[i][j] = nextGrid[i][j]
                nextGrid[i][j] = 0
            }
        }
    }

    function updateView() {
        for (let i = 0; i < rows; i++) {
            for (let j = 0; j < cols; j++) {
                let cell = document.getElementById(i + '_' + j)
                if (grid[i][j] === 0) {
                    cell.setAttribute('class', DEAD)
                } else {
                    cell.setAttribute('class', ALIVE)
                }
            }
        }
    }

    async function clearGameField() {
        clearInterval(intervalId)
        for (let i = 0; i < rows; i++) {
            for (let j = 0; j < cols; j++) {
                document.getElementById(i + '_' + j).setAttribute('class', DEAD)
            }
        }
        await createGrids()
    }

    function automaticNextGeneration() {
        clearInterval(intervalId)
        speed = document.getElementById('customRange').value
        intervalId = setInterval(createNextGeneration, speed)
    }

    function stopGame() {
        clearInterval(intervalId)
    }

    async function changeSpeed() {
        if (gameOn) {
            await stopGame()
            automaticNextGeneration()
        }
    }

    function generateRandomField() {
        for (let i = 0; i < rows; i++) {
            for (let j = 0; j < cols; j++) {
                let isAlive = Math.round(Math.random());
                if (isAlive === 1) {
                    let cell = document.getElementById(i + '_' + j)
                    cell.setAttribute('class', ALIVE)
                    grid[i][j] = 1
                }
            }
        }
    }

    async function cloverleaf() {
        await clearGameField()
        grid[8][11] = 1
        grid[8][13] = 1
        grid[9][9] = 1
        grid[9][10] = 1
        grid[9][11] = 1
        grid[9][13] = 1
        grid[9][14] = 1
        grid[9][15] = 1
        grid[10][8] = 1
        grid[10][12] = 1
        grid[10][16] = 1
        grid[11][8] = 1
        grid[11][10] = 1
        grid[11][14] = 1
        grid[11][16] = 1
        grid[12][9] = 1
        grid[12][10] = 1
        grid[12][12] = 1
        grid[12][14] = 1
        grid[12][15] = 1
        grid[14][9] = 1
        grid[14][10] = 1
        grid[14][12] = 1
        grid[14][14] = 1
        grid[14][15] = 1
        grid[15][8] = 1
        grid[15][10] = 1
        grid[15][14] = 1
        grid[15][16] = 1
        grid[16][8] = 1
        grid[16][12] = 1
        grid[16][16] = 1
        grid[17][9] = 1
        grid[17][10] = 1
        grid[17][11] = 1
        grid[17][13] = 1
        grid[17][14] = 1
        grid[17][15] = 1
        grid[18][11] = 1
        grid[18][13] = 1
        await updateView()
    }

    async function beacon() {
        await clearGameField()
        grid[10][11] = 1;
        grid[10][12] = 1;
        grid[11][11] = 1;
        grid[12][14] = 1;
        grid[13][13] = 1;
        grid[13][14] = 1;
        await updateView()
    }

    function handleSelect() {
        switch (selected) {
            case '--Patterns--':
                clearGameField()
                break
            case 'Cloverleaf':
                cloverleaf()
                break
            case 'Beacon':
                beacon()
                break
        }
    }

</script>
<main>
    <div>
        <h1>
            Game of Life
        </h1>
        <div class="button-row1">
            <button class="button" on:click={() => gameOn = true} on:click={automaticNextGeneration}>Start</button>
            <button class="button" on:click={() => gameOn = false} on:click={stopGame}>Stop</button>
            <button class="button" on:click={clearGameField}>Clear</button>
        </div>

        <div class="button-row2">
            <button class="button" on:click={createNextGeneration}>Next generation</button>
            <button class="button" on:click={generateRandomField}>Random</button>
        </div>


        <table id="gameField"></table>

        <label for="customRange" class="form-label">Speed</label>
        <input on:change={changeSpeed} type="range" id="customRange" min="100" max="1000" value="550">

        <div>
            <select class="custom-select" bind:value={selected} on:change={handleSelect}>
                <option selected>--Patterns--</option>
                <option>Cloverleaf</option>
                <option>Beacon</option>
            </select>

        </div>


    </div>
    <div class="footer"></div>

</main>
<style>


    .custom-select {
        position: relative;
        margin: 1em;
        border-radius: 3px;
        width: 130px;
        height: 25px;
        text-align: center;

    }

    .button-row1 {
        display: flex;
        justify-content: center;
        margin: 0 2em 0;
    }

    .button-row2 {
        display: flex;
        justify-content: center;
        margin: 0 2em 0.8em;

    }

    label {
        display: block;
    }

    table {
        margin: auto auto 20px;
        border: 1px solid #adacac;
    }

    :global(.cell) {
        width: 10px;
        height: 10px;
        border: 1px solid #adacac;
        border-radius: 3px;
    }

    :global(.dead) {
        background-color: #C5D6C6;
    }

    :global(.alive) {
        background-color: #f38282;
    }

    main {
        display: flex;
        justify-content: center;
        align-content: flex-end;
        font-family: AppleMyungjo, serif;
    }

    .button:hover {
        filter: drop-shadow(0 0 2em #eaa9a9);
    }

    .footer {
        padding-bottom: 50px;
    }

</style>
