<template>
    <div class="grid">
        <button class="up" @click="$emit('moveUp')">
            <up-arrow-icon></up-arrow-icon>
            UP
        </button>
        <button class="down" @click="$emit('moveDown')">DOWN</button>
        <button class="left" @click="$emit('moveLeft')">
            <left-arrow-icon/>
            LEFT</button>
        <button class="right" @click="$emit('moveRight')">RIGHT</button>
        <button class="front" @click="$emit('moveFront')">FRONT</button>
        <button class="back" @click="$emit('moveBack')">BACK</button>
        <button class="scale" @click="$emit('scale')">SCALE OBJECT</button>
        <button class="rotate" @click="$emit('rotate')">ROTATE OBJECT</button>
        <button class="translate" @click="$emit('translate')">TRANSLATE OBJECT</button>
        <input  class = "color-picker" type="color"  @input="changeColor($event.target.value)"/>
        <div class="slotstyle">
        <slot></slot>
        </div>
    </div>
</template>

<script>
import upArrowIcon from './upArrowIcon.vue'
import leftArrowIcon from './leftArrowIcon.vue'
    export default {
  components: { upArrowIcon, leftArrowIcon },
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
    width: 18em;
    right: 0.5em;
    top: 3.8em;
    grid-template-columns: auto auto auto;
    grid-template-rows: auto auto auto;
    background-color: rgba(0, 0, 0, 0.8);
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
.color-picker {
    justify-self: center;
}
.slotstyle{
    grid-column: 1 / span 3;
}

</style>