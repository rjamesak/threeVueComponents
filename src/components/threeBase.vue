<template>
<div>
  <canvas id="c" ref="c">
    <getModel v-if="isMounted" :width="5" :height="1" :depth="2" :xPos="4" @model-created="addToScene"></getModel>
    <!-- <getModel v-if="isMounted" v-bind="geomParams"></getModel> -->
    <getModelCopy v-if="isMounted" @model-created="addToScene" v-bind="geomParams"/>
    <ground v-if="isMounted"></ground>
    <!-- <div id="guiBlock"></div> -->
  </canvas>
    <arrowControls @addBox="addBox" @moveUp="moveUp" @moveDown="moveDown" @moveRight="moveRight" @moveLeft="moveLeft"/>
</div>
</template>

<script>
import Vue from 'vue'
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import getModel from './getModel.vue'
import getModelCopy from './getModel copy.vue'
import ground from './ground.vue'
import arrowControls from './arrowControls'
export default {
  name: "threeBase",
  components: {
    getModel,
    getModelCopy,
    ground,
    arrowControls
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
      this.scene.background = new THREE.Color(0xaaaaaa);
      this.addLight();
      this.mouse = new THREE.Vector2();
      this.yellow = new THREE.Color("yellow")
      this.savedColor = new THREE.Color()
      this.initControls();
      this.initEventListeners();
      this.initRaycaster();

      this.render();
      // requestAnimationFrame(this.render);
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
      
      this.raycast()

      //something picked last frame
      if (this.picked) {
        this.picked = null
      }

      //something picked now
      if (this.intersects.length) {
        if (this.clicked) {
          this.clicked = false;
          this.picked = this.intersects[0].object;
          if(this.chosen) {
            //return last chosen object to old color
            this.chosen.material.color = this.savedColor;
          }
          this.chosen = this.picked; // chosen for manip
          this.savedColor = this.chosen.material.color;
          console.log(this.picked)
          this.picked.material.color = this.yellow;
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
        this.scene.children, true
      )
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
      this.chosen.position.z++;
    },
    moveDown() {
      this.chosen.position.z--;
    },
    moveLeft() {
      this.chosen.position.x--;
    },
    moveRight() {
      this.chosen.position.x++;
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
#c {
  width: 100vw;
  height: 100vh;
  display: block;
  overflow: hidden;
  position: relative;
}
</style>
