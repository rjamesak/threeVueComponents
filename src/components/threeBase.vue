<template>
<div>


    <canvas id="c" ref="c">
      <getModel v-if="isMounted" :width="5" :height="1" :depth="2" :xPos="4" @model-created="addToScene"></getModel>
      <!-- <getModel v-if="isMounted" v-bind="geomParams"></getModel> -->
      <getModelCopy v-if="isMounted" @model-created="addToScene" v-bind="geomParams"/>
      <ground v-if="isMounted"></ground>
      <!-- <div id="guiBlock"></div> -->
    </canvas>
      <arrowControls class="guiControls" @addBox="addBox" @moveUp="moveUp" @moveDown="moveDown" 
      @moveRight="moveRight" @moveLeft="moveLeft"
      @moveFront="moveFront"
      @moveBack ="moveBack"
      @changeColor="changeColor"
      @scale="scale"
      @rotate="rotate"
      @translate="translate">
      <mesh-maker @model-created="addToScene"/>
      </arrowControls>
      

</div>
</template>

<script>
import Vue from 'vue'
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { TransformControls } from "three/examples/jsm/controls/TransformControls.js";
import getModel from './getModel.vue'
import getModelCopy from './getModel copy.vue'
import ground from './ground.vue'
import arrowControls from './arrowControls'
import meshMaker from './meshMaker.vue'
export default {
  name: "threeBase",
  components: {
    getModel,
    getModelCopy,
    ground,
    arrowControls,
    meshMaker,
  },
  data() {
    return {
      down: false,
      then: 0,
      clicked: false,
      isMounted: false,
      geomParams: {
        xPos: -3, 
        yPos: 3, 
        zPos: 4,
        width: 3, 
        height: 1, 
        depth: 1,
        color: 0x2f34a1,
      },
      models: []
    };
  },
  methods: {
    main() {
      const canvas = document.querySelector("#c");
      this.renderer = new THREE.WebGLRenderer({ canvas });
      this.createCamera();
      this.scene = new THREE.Scene();
      window.scene = this.scene; //for three.js inspector extension
      window.THREE = THREE       //for three.js inspector
      this.scene.background = new THREE.Color(0xaaaaaa);
      this.addLight();
      this.mouse = new THREE.Vector2();
      this.yellow = new THREE.Color("yellow")
      this.savedColor = new THREE.Color()
      this.newColor = new THREE.Color()
      this.initControls();
      this.initTransformControls();
      this.initEventListeners();
      this.initRaycaster();

      this.render();
      this.isMounted = true;
    },
    render(now) {
      now *= 0.001; //convert ms to s
      // const deltaTime = now - this.then;
      // console.log(deltaTime)
      this.then = now;

      if (this.resizeRendererToDisplaySize(this.renderer)) {
        const canvas = this.renderer.domElement;
        this.camera.aspect = canvas.clientWidth / canvas.clientHeight;
        this.camera.updateProjectionMatrix();
      }
      

      this.controls.update();

      //something picked last frame
      if (this.picked) {
        this.picked = null
        this.intersects = null
      }

      this.raycast()
      //something picked now
      if (this.intersects.length > 0) {
        // for (let objects of this.intersects) {
          // console.log(objects.type)
        // }
        this.picked = this.intersects[0].object;
        console.log('picked')
        if (this.clicked) {
          this.clicked = false;
          //if object clicked last frame
          if(this.chosen) {
            //return last chosen object to old color
            this.chosen.material.color = this.savedColor;
            this.tControls.detach()
            // remove chosen if selected again
            if(this.chosen.id === this.picked.id) {
              this.chosen = null
              }
            // if picked !== chosen, then choose
            else if(this.chosen.id !== this.picked.id) {
              this.choose()
            }
          }
          else {
            this.choose()
          }
          
          // account for clicking already chosen object
          // if picked === chosen, do nothing
          // if first pick (!chosen), then choose

          

          // this.chosen = this.picked; // chosen for manip
          // this.savedColor = this.chosen.material.color;
          // console.log(this.picked)
          // this.picked.material.color = this.yellow
          // // //transform controls
          // this.tControls.attach(this.picked)

        }

      }

      this.clicked = false;

      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.render); //passing it the render function
    },
    resizeRendererToDisplaySize(renderer) {
      const canvas = renderer.domElement;
      const pixelRatio = window.devicePixelRatio;
      const width = (canvas.clientWidth * pixelRatio) | 0;
      const height = (canvas.clientHeight * pixelRatio) | 0;
      const needResize = canvas.width !== width || canvas.height !== height;
      if (needResize) {
        console.log("needs resize");
        this.renderer.setSize(width, height, false);
      }
      return needResize;
    },
    createCamera() {
      const fov = 75;
      const aspect = 2;
      const near = 0.1;
      const far = 100;
      this.camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
      this.camera.position.set(0, -10, 4);
      this.camera.up.set(0,0,1)
      this.camera.lookAt(0, 0, 0);
    },
    addLight() {
      const color = 0xffffff;
      const intensity = 0.75;
      const light = new THREE.DirectionalLight(color, intensity);
      const ambLight = new THREE.AmbientLight(color, intensity);
      light.position.set(-1, 2, 4);
      this.scene.add(light);
      this.scene.add(ambLight);
    },
    raycast() {
      this.raycaster.setFromCamera(this.mouse, this.camera);
      this.intersects = this.raycaster.intersectObjects(
        this.models, true
      )
    },
    choose() {
            this.chosen = this.picked; // chosen for manip
            this.savedColor = this.chosen.material.color;
            console.log(this.picked)
            this.picked.material.color = this.yellow
            // //transform controls
            this.tControls.attach(this.picked)
    },
    
    initEventListeners() {
      this.$refs.c.addEventListener("mousemove", this.onMouseMove, false);
      //   this.controls.addEventListener("mousedown", this.onMouseDown, false);
      this.$refs.c.addEventListener("mousedown", this.onMouseDown, false);
      this.$refs.c.addEventListener("mouseup", this.onMouseUp, false);
      this.$refs.c.addEventListener("click", this.mouseClicked, false);
    },
    initControls() {
      this.controls = new OrbitControls(this.camera, this.renderer.domElement)
    },
    initTransformControls() {
      this.tControls = new TransformControls(this.camera, this.renderer.domElement)
      this.scene.add(this.tControls)
      // this.tControls.addEventListener("change", this.render)
      this.tControls.addEventListener("mouseDown", () => {
        this.controls.enabled = false
        console.log('tControl Mousedown event')
      })
      this.tControls.addEventListener("mouseUp", () => this.controls.enabled = true)
    },
    initRaycaster() {
      this.raycaster = new THREE.Raycaster();
      this.picked = null;
    },
    onMouseMove() {
      this.mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      this.mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    },
    onMouseDown() {
      this.down = true;
      console.log("mouseDown");
    },
    onMouseUp() {
      this.down = false;
      console.log("mouseup");
    },
    mouseClicked() {
      this.clicked = true;
      console.log("click!");
    },
    moveUp() {
      this.geomParams.zPos++;
      // this.models[0].position.z++
      if(this.chosen) {
        this.chosen.position.z++;
      }
    },
    moveDown() {
      if(this.chosen) {
        this.chosen.position.z--;
      }
    },
    moveLeft() {
      if(this.chosen) {
        this.chosen.position.x--;
      }
    },
    moveRight() {
      if(this.chosen) {
        this.chosen.position.x++;
      }
    },
    moveFront() {
      if(this.chosen) {
        this.chosen.position.y--;
      }
    },
    moveBack() {
      if(this.chosen) {
        this.chosen.position.y++;
      }
    },
    scale() {
      this.tControls.setMode('scale')
    },
    rotate() {
      this.tControls.setMode('rotate')
    },
    translate() {
      this.tControls.setMode('translate')
    },
    changeColor(color) {
      //receiving color as "#FFFFFF" hex color
      //how to make a real three color?
      console.log('color: ', color.replace("#", "0x"))
      const formattedColor = color.replace("#", "0x")
      this.savedColor.setHex(formattedColor)
      // this.chosen.material.color.setHex(formattedColor)
      // this.chosen.material.color = color;
    },
    addBox() {
      const ModelClass = Vue.extend(getModel)
      this.newBox = new ModelClass()
      this.newBox.$mount()
      this.$refs.c.appendChild(this.newBox.$el)
    },
    addToScene(mesh) {
      this.scene.add(mesh)
      this.models.push(mesh)
    },
    
    
  },
  mounted() {
    this.main();
  },
};
</script>

<style lang="css" scoped>
.grid {
  display: grid;
  grid-template-columns: auto auto;

}

#c {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  position: relative;
}
</style>
