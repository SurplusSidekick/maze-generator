<template>
    <div
        class="relative flex w-10 h-10 bg-red-300"
        :class="{
            'bg-blue-300': visited,
            'bg-green-500': active,
            'bg-red-500': targeted,
        }"
    ></div>
</template>

<script>
export default {
    props: {
        cell: {
            type: Object,
            required: true
        }
    },

    data() {
        return {
            visited: this.cell.visited,
            active: this.cell.active,
            targeted: this.cell.targeted,
        }
    },
}
</script>
