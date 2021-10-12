<template>
    <div
        class="
            relative
            flex
            items-top
            justify-center
            min-h-screen
            bg-gray-100
            sm:items-center sm:pt-0
        "
    >
        <template v-for="(row, x) in grid">
            <div :key="'row_' + x" class="flex-row">
                <template v-for="(cell, y) in row">
                    <Cell
                        :key="'cell_' + generation + '_' + x + '_' + y"
                        :value="grid[x][y]"
                        :cell="
                            cells.filter(
                                (cell) => cell.x == x && cell.y == y,
                            )[0]
                        "
                        :x="x"
                        :y="y"
                    ></Cell>
                </template>
            </div>
        </template>
    </div>
</template>

<script>
import Cell from './Cell.vue'
export default {
    components: {
        Cell,
    },

    props: {
        grid: {
            type: Array,
            required: true,
        },
        cells: {
            type: Array,
            required: true,
        },
        generation: {
            type: Number,
            required: true,
        },
    },

    computed: {
        active() {
            return this.cells.filter((cell) => cell.active)[0]
        },

        targeted() {
            return this.cells.filter((cell) => cell.targeted)
        },

        visited() {
            return this.cells.filter((cell) => cell.visited)
        },
    },

    beforeUpdate() {
        this.evaluatePossibleTargets()
        this.updateGridState()
    },

    methods: {
        updateGridState() {
            this.$emit('update', this.cells)
        },

        evaluatePossibleTargets() {
            this.cells.map((cell) => {

                if(cell.visited) return cell;

                const distX =
                    cell.x > this.active.x
                        ? cell.x - this.active.x
                        : this.active.x - cell.x
                const distY =
                    cell.y > this.active.y
                        ? cell.y - this.active.y
                        : this.active.y - cell.y

                if (distX <= 1 && distY <= 1 && distX + distY === 1) {
                    cell.targeted = true
                    return cell
                }
                return cell
            })
        },
    },
}
</script>
