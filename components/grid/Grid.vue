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
        <template v-for="(row, x) in board">
            <div :key="'row_' + x" class="flex-row">
                <template v-for="(cell, y) in row">
                    <Cell
                        :key="cells[JSON.stringify([x, y])]"
                        :active="!!cell.active"
                        :visited="!!cell.visited"
                        :targeted="!!cell.targeted"
                        :x="x"
                        :y="y"
                    ></Cell>
                </template>
            </div>
        </template>
        <button
            class="
                absolute
                right-5
                bottom-5
                w-10
                h-10
                rounded-full
                bg-green-900
            "
            @click="next"
        ></button>
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
            type: Object,
            required: true,
        },
        cells: {
            type: Array,
            required: true,
        },
    },

    data() {
        return {
            state: [[], 32]
        }
    },

    computed: {
        board() {
            const board = []
            for (let i = 0; i < this.cells.length; i += this.grid.y) {
                board.push(this.cells.slice(i, i + this.grid.y))
            }

            return board
        },
    },

    beforeUpdate() {
        this.updateGridState()
    },

    methods: {
        next() {
            this.state = this.calculateNewState(...this.state)
        },

        updateGridState() {
            this.$emit('update', this.cells)
        },

        calculateNewState(oldTargetKeys, currentCellKey) {
            /* eslint-disable vue/no-mutating-props */

            // pick target
            if(!oldTargetKeys.length) {
                oldTargetKeys = this.getTargetKeys(currentCellKey)
            }

            const newActiveKey = this.pickTargetAtRandom(oldTargetKeys)

            // remove old target markers
            oldTargetKeys.forEach((target) => {
                this.cells[target].targeted = false
            })

            // mark old active as visited
            this.cells[currentCellKey].visited = true
            this.cells[currentCellKey].active = false

            // paint new active
            this.cells[newActiveKey].active = true

            // get new targets 
            const targetKeys = this.getTargetKeys(newActiveKey)

            // paint new targets
            targetKeys.forEach((newTarget) => {
                this.cells[newTarget].targeted = true
            })
            /* eslint-enable vue/no-mutating-props */
            // return currently important keys
            return [targetKeys, newActiveKey]
        },

        getTargetKeys(key) {
            const result = []
            for (let i = 0; i < 4; i++) {
                let newKey = null
                switch (i) {
                    case 0: // down
                        if (
                            (key+1) % this.grid.y !== 0 &&
                            key < this.cells.length - 1
                        )
                            newKey = key + 1
                        break
                    case 1: // up
                        if (key % this.grid.y !== 0 && key !== 0)
                            newKey = key - 1
                        break
                    case 2: // left
                        if (key > this.grid.y) newKey = key - this.grid.y
                        break
                    case 3: // right
                        if (key < this.cells.length - this.grid.y)
                            newKey = key + this.grid.y
                        break
                }
                console.log({i, key, newKey, length: this.cells.length, y: this.grid.y, x: this.grid.x})

                result.push(newKey)
            }

            const filtered = result.filter((item) =>{
                if(item === null) return false 
                if(this.cells[item].visited) return false   
                return true;
            })

            console.log({ filtered })
            return filtered
        },

        pickTargetAtRandom(targets) {
            return targets[Math.floor(Math.random() * targets.length)]
        },

        sleep(ms) {
            return new Promise((resolve) => setTimeout(resolve, ms))
        },
    },
}
</script>
