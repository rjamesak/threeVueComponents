<template>
    <div>

    </div>
</template>

<script>
    import * as THREE from "three";

    export default {
        name: "getModel",
        props: {
            xPos: {
                default: 1
            },
            yPos: {
                default: 1
            },
            zPos: {
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
        },
        
        methods: {
            main() {
                this.createBox()
                
            },
            createBox() {
                const geometry = new THREE.BoxBufferGeometry(this.width, this.height, this.depth)
                this.material = new THREE.MeshPhongMaterial({color: 0x44aa88})
                this.boxMesh = new THREE.Mesh(geometry, this.material)
                this.$parent.scene.add(this.boxMesh)
            },
        },
        mounted() {
            this.main()
        }

    }
</script>

<style lang="scss" scoped>

</style>