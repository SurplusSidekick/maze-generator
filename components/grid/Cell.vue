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
        active: {
            required: true,
            type: Boolean
        },
        visited: {
            required: true,
            type: Boolean
        },
        targeted: {
            required: true,
            type: Boolean
        }
    },

    updated() {
        console.log("updated cell")
    }
}
</script>
