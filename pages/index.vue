<template>
    <div class="relative">
        <Grid :cells="cells" :generation="generation" :grid="grid" :grid-size="gridSize" @update="handleGridUpdate" />
        <button class="absolute right-5 bottom-5 w-10 h-10 rounded-full bg-green-900" @click="next"></button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            gridStates: [],
            cells: [],
            grid: [],
            pathTaken: [],
            stepNext: false,
            generation: 0,

            targetGenerations: 25*25,
            sleepTime: 250,

            gridSize: {
                x: 25,
                y: 25
            }
        }
    },

    mounted() {
        this.generateGrid();
        this.markCellActive(4);
    },

    methods: {
        next() {
            this.calculateNewState();
        },
        
        handleGridUpdate(data) {
            this.gridStates.push(data)
            if(this.generation < this.targetGenerations) {
                this.sleep(this.sleepTime).then(() => {
                    this.calculateNewState();
                })
            }
        },

        chooseRandom(items) {
            // console.log({items})
            const winner = items[Math.floor(Math.random()*items.length)]
            this.pathTaken.push(winner);
            // console.log({winner})
            return items[Math.floor(Math.random()*items.length)]
        },

        calculateNewState() {
            const targets = this.cells.filter(cell => cell.targeted);
            const newActive = this.chooseRandom(targets);
            if(!newActive) {
                console.log("done")
                return 0
            } else {
                this.cells = this.cells.map((cell) => {
                    if(cell.active) {
                        cell.active = false;
                        cell.visited = true;
                    }

                    if(cell.targeted) {
                        cell.targeted = false;
                    }

                    if(JSON.stringify(cell) === JSON.stringify(newActive)) {
                        cell.active = true;
                    }

                    return cell;

                })
                this.generation++;
            }
        },

        generateGrid() {
            const grid = []
            for (let x = 0; x < this.gridSize.x; x++) {
                grid[x] = []
                for (let y = 0; y < this.gridSize.y; y++) {
                    this.cells.push({ x, y })
                    grid[x][y] = { x, y }
                }
            }
            this.grid = grid
        },

        markCellActive(index) {
            this.cells[index].active = true
        },

        sleep(ms) {
            return new Promise(resolve => setTimeout(resolve, ms));
        }
    }
}
</script>
