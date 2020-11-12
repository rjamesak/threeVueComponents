<template>
    <div class="grid">
        <button class="up" @click="$emit('moveUp')">UP</button>
        <button class="down" @click="$emit('moveDown')">DOWN</button>
        <button class="left" @click="$emit('moveLeft')">LEFT</button>
        <button class="right" @click="$emit('moveRight')">RIGHT</button>
        <button class="front" @click="$emit('moveFront')">FRONT</button>
        <button class="back" @click="$emit('moveBack')">BACK</button>
        <button class="scale" @click="$emit('scale')">SCALE OBJECT</button>
        <button class="rotate" @click="$emit('rotate')">ROTATE OBJECT</button>
        <button class="translate" @click="$emit('translate')">TRANSLATE OBJECT</button>
        <input type="color"  @input="changeColor($event.target.value)"/>
    </div>
</template>

<script>
    export default {
        name: "arrowControls",
        methods: {
            changeColor(color) {
                this.$emit('changeColor', color)
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
.back {
    grid-column-start: 3;
    grid-row-start: 1;
}
.scale, .rotate, .translate {
    grid-row-start: 4;
}

</style>