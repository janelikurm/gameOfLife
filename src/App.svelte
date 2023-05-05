<script>

    import {onMount} from 'svelte';

    let rows = 20
    let cols = 20
    let grid = []
    let nextGrid = []
    let ALIVE = 'cell alive'
    let DEAD = 'cell dead'
    let intervalId

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

        let count = 0;
        if (row - 1 >= 0) {
            if (grid[row - 1][col] === 1) count++
        }
        if (row - 1 >= 0 && col - 1 >= 0) {
            if (grid[row - 1][col - 1] === 1) count++
        }
        if (row - 1 >= 0 && col + 1 < cols) {
            if (grid[row - 1][col + 1] === 1) count++
        }
        if (col - 1 >= 0) {
            if (grid[row][col - 1] === 1) count++
        }
        if (row + 1 < rows && col - 1 >= 0) {
            if (grid[row + 1][col - 1] === 1) count++
        }
        if (row + 1 < rows) {
            if (grid[row + 1][col] === 1) count++
        }
        if (row + 1 < rows && col + 1 < cols) {
            if (grid[row + 1][col + 1] === 1) count++
        }
        if (col + 1 < cols) {
            if (grid[row][col + 1] === 1) count++
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
        intervalId = setInterval(createNextGeneration, 250);
    }

    function stopGame() {
        clearInterval(intervalId)
    }


</script>
<main>
    <div>
        <h1>
            Game of Life
        </h1>
        <div>
            <button class="button" on:click={createNextGeneration}>Next generation</button>
            <button class="button" on:click={automaticNextGeneration}>Start</button>
            <button class="button" on:click={stopGame}>Stop</button>
            <button class="button" on:click={clearGameField}>Clear</button>
        </div>

        <div>
            <table id="gameField" style="border: 1px solid black">

            </table>

        </div>
    </div>


</main>
<style>

    table {
        border: 1px solid black;
        margin: auto;
    }

    :global(.cell) {
        width: 10px;
        height: 10px;
        border: 1px solid black;
        border-radius: 3px;
    }

    :global(.dead) {
        background-color: #C5D6C6;
    }

    :global(.alive) {
        background-color: #f38282;
    }

    div {
        padding: 10px;
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

</style>
