<template>
    <div>
        <button @click="makeInstance" class="addButton"> Make new Instance </button>

    </div>
</template>

<script>
import * as THREE from "three";
    export default {
        name: "meshMaker",
        props: {
                geomType: {
                    default: function() {
                        return new THREE.BoxBufferGeometry(1, 1, 1)
                    }
                },
                x: {
                   default: 1
                }, 
                y: {
                    default: 1
                }, 
                z: {
                    default: 1
                }, 
                width: {
                    default: 1
                }, 
                height: {
                    default: 1
                }, 
                depth: {
                    default: 1
                },
                color: {
                    default: 0x479BA8
                },
        },
        methods: {
            makeInstance() {
                console.log('making instance')
                const geometry = this.geomType
                this.material = new  THREE.MeshPhongMaterial({color: this.color})
                this.instance = new THREE.Mesh(geometry, this.material)
                this.instance.position.x = this.x
                this.instance.position.y = this.y
                this.instance.position.z = this.z
                this.$emit('model-created', this.instance)
            }
        },
    }
</script>

<style lang="css" scoped>
/* .addButton {
    position: absolute;
    top: 20vw;
    right: 1em;
} */

</style>