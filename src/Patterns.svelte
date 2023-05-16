<script>

export let clearGameField
export let updateView
export let grid
let selected

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
        case 'Spaceship':
            spaceship()
            break
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
    grid[10][11] = 1
    grid[10][12] = 1
    grid[11][11] = 1
    grid[12][14] = 1
    grid[13][13] = 1
    grid[13][14] = 1
    await updateView()
}

async function spaceship() {
    await clearGameField()
    grid[8][10] = 1
    grid[8][11] = 1
    grid[8][14] = 1
    grid[8][15] = 1
    grid[9][12] = 1
    grid[9][13] = 1
    grid[10][12] = 1
    grid[10][13] = 1
    grid[11][9] = 1
    grid[11][11] = 1
    grid[11][14] = 1
    grid[11][16] = 1
    grid[12][9] = 1
    grid[12][16] = 1
    grid[14][9] = 1
    grid[14][16] = 1
    grid[15][10] = 1
    grid[15][11] = 1
    grid[15][14] = 1
    grid[15][15] = 1
    grid[16][11] = 1
    grid[16][12] = 1
    grid[16][13] = 1
    grid[16][14] = 1
    grid[18][12] = 1
    grid[18][13] = 1
    grid[19][12] = 1
    grid[19][13] = 1
    await updateView()
}

</script>

<select class="custom-select" bind:value={selected} on:change={handleSelect}>
    <option selected>--Patterns--</option>
    <option>Cloverleaf</option>
    <option>Beacon</option>
    <option>Spaceship</option>
</select>
