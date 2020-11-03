<template>
    <div>

    </div>
</template>

<script>
import {PlaneGeometry} from "three"
import {MeshBasicMaterial} from "three"
import {Mesh} from "three"
    export default {
        name: "ground",
        methods: {
            main() {
                this.createPlane()
            },
            createPlane() {
                const geometry = new PlaneGeometry(20, 20, 5, 5)
                this.material = new MeshBasicMaterial({wireframe: true})
                this.plane = new Mesh(geometry, this.material)
                console.log(this.plane)
                this.$parent.scene.add(this.plane)
            }
        },
        mounted() {
            this.main()
        }
    }
</script>

<style lang="scss" scoped>

</style>