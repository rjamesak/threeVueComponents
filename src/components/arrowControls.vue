<template>
    <div class="grid">
        <button class="up" @click="moveUp">UP</button>
        <button class="down" @click="$emit('moveDown')">DOWN</button>
        <button class="left" @click="$emit('moveLeft')">LEFT</button>
        <button class="right" @click="$emit('moveRight')">RIGHT</button>
        <button class="add" @click="$emit('addBox')">ADD BOX</button>
    </div>
</template>

<script>
    export default {
        name: "arrowControls",
        methods: {
            moveUp() {
                this.$emit('moveUp')
            }
        },
    }
</script>

<style lang="css" scoped>
.grid {
    display: grid;
    position: absolute;
    left: 1em;
    bottom: 0em;
    grid-template-columns: auto auto auto;
    grid-template-rows: 40px 40px 40px;
}
.up {
    grid-column-start: 2;
}
.down {
    grid-column-start: 2;
    grid-row-start: 3;
}
.left {
    grid-column-start: 1;
}
.right {
    grid-column-start: 3;
}

</style>