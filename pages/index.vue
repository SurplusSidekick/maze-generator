<template>
    <div class="relative">
        <Grid :cells="cells" :grid="grid" @update="handleGridUpdate" />
    </div>
</template>

<script>
export default {
    data() {
        return {
            gridStates: [],
            cells: [],
            grid: {
                x: 16,
                y: 9,
            },
        }
    },

    mounted() {
        this.generateCells()
    },

    methods: {
        next() {
            this.calculateNewState()
        },

        handleGridUpdate(data) {
            this.gridStates.push(data)
        },

        generateCells() {
            for (let x = 0; x < this.grid.x; x++) {
                for (let y = 0; y < this.grid.y; y++) {
                    const status = { x, y, active: false, visited: false }
                    this.cells.push(status)
                }
            }
        },
    },
}
</script>
