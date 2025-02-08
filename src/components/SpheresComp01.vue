<template>
  <canvas style="width:900px; height:900px;" id="canvasElement"></canvas>
  </template>
  
  <script>
  import * as THREE from 'three';
  import * as d3 from 'd3-scale-chromatic';
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';


  export default {
  name: 'SpheresComp01',
  data() {
    return {
      light: null,
      scene: null,
      camera: null,
      renderer: null,
      controls: null,
      plane: null
    }
  },
  computed: {
    width() {
      return this.canvas.innerWidth;
    },
    height() {
      return this.canvas.innerHeight;
    },
    canvas() {
      return document.getElementById('canvasElement');
    }
  },
  methods: {
    init() {
      this.createScene();
      this.createCamera();
      this.createRenderer();
      this.createControls();
      this.createLight();
      this.createPlane();
      this.createSpheres();

    },
    createScene() {
      const scene = new THREE.Scene();
      scene.background = new THREE.Color( 0xaaaaaa );
      this.scene = scene;
    },
    createCamera() {
      const camera = new THREE.PerspectiveCamera( 75, this.width / this.height, 0.1, 1000 );
      camera.aspect = this.width / this.height;
      camera.updateProjectionMatrix();
      this.camera = camera;
    },
    createRenderer() {
      const canvas = this.canvas;
      const renderer = new THREE.WebGLRenderer({canvas, antialias:true})
      renderer.setClearColor(0x000000, 0); // Set clear color to black with zero alpha
      renderer.setPixelRatio( window.devicePixelRatio );
      renderer.setSize( this.width, this.height );
      renderer.shadowMap.enabled = true; // Enable shadow maps
      this.renderer = renderer;
    },
    createControls() {
      const controls = new OrbitControls(this.camera, this.renderer.domElement);
      controls.enableDamping = true; // Enable damping (inertia)
      controls.dampingFactor = 0.50;
      controls.screenSpacePanning = true;
      controls.maxPolarAngle = Math.PI / 2;
      this.controls = controls;
    },
    createLight() {
      const light = new THREE.DirectionalLight( 0xffffff, 5 );
      light.position.set(5,5,5 ); // Position the light source more from the right
      light.castShadow = true; // Enable casting shadows for the light
      this.light = light;
      this.scene.add( this.light );
     
      const ambientLight = new THREE.AmbientLight( 0x404040, 4 ); // Add ambient light for softer shadows
      this.ambientLight = ambientLight;
      this.scene.add( this.ambientLight );
    },
    createSphere() {
      const geometry = new THREE.SphereGeometry( 1, 32, 32 );
      const material = new THREE.MeshStandardMaterial( { color: d3.interpolateRainbow(Math.random()) } );
      const sphere = new THREE.Mesh( geometry, material );
      sphere.position.x = Math.random() * 10 - 5;
      sphere.position.y = Math.random() * 10 - 5;
      sphere.position.z = Math.random() * 10 - 5;
      sphere.castShadow = true; // Enable casting shadows for the sphere
      this.scene.add( sphere );
    },
    createSpheres(){
      const self = this;
      for (let i = 0; i < 1000; i++) {
      // const y = Math.abs(generateNormalDistribution(0.5, 1));
      const y = self.generateNormalDistribution(1, 1);
      const x = self.generateNormalDistribution(0, Math.abs(y) + (Math.random()/2));
      const z = self.generateNormalDistribution(0,  Math.abs(y));
      const radius = (z + 1) * 0.01 + (y + 1) * 0.01; // Scale radius based on z and y values
      const sphereGeometry = new THREE.SphereGeometry(radius, 32, 32);
      const color = self.interpolateColor(x);
      const opacity = (z + 1) / 2; // Normalize z to range [0, 1] for opacity
      const sphereMaterial = new THREE.MeshStandardMaterial({ color: color, transparent: true, opacity: opacity });
      const smallSphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
      smallSphere.position.set(x, y, z);
      smallSphere.castShadow = true;
      self.scene.add(smallSphere);
      }
    },
    createPlane() {
      const planeGeometry = new THREE.PlaneGeometry( 10, 10 );
      const planeMaterial = new THREE.ShadowMaterial({ opacity: 1, color: 0x000000 });
      const plane = new THREE.Mesh( planeGeometry, planeMaterial );
      plane.rotation.x = - Math.PI / 2;
      plane.position.y = -1;
      plane.receiveShadow = true; // Enable receiving shadows for the plane
      this.plane = plane;
      this.scene.add( this.plane );
    },
    animate() {
      requestAnimationFrame(this.animate.bind(this));
      this.controls.update();
      this.light.position.set(this.camera.position.x, this.camera.position.y, this.camera.position.z);
      this.renderer.render(this.scene, this.camera);
    },
    interpolateColor(x) {
        const t = (x + 1) / 2; // Normalize x to range [0, 1]
        const color = d3.interpolateRdBu(t);
        return new THREE.Color(color);
    },
    generateNormalDistribution(mean, stdDev) {
      let u = 0, v = 0;
      while (u === 0) u = Math.random(); // Converting [0,1) to (0,1)
      while (v === 0) v = Math.random();
      return mean + stdDev * Math.sqrt(-1.0 * Math.log(u)) * Math.cos(2.0 * Math.PI * v);
  }
  
  },
   mounted() {
     this.init();
    this.animate();
  }
  }
  </script>
  
  <style scoped>

  
  </style>