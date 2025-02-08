<template>
  <canvas style="z-index:10000; width:100vw; height:100vh;" id="canvasElement"></canvas>
</template>
  
  <script>
  import * as THREE from 'three';
  import * as d3 from 'd3-scale-chromatic';
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';


  export default {
  name: 'SpheresComp01',
  data() {
    return {

    }
  },
  computed: {

  },
  methods: {
    init() {
        const self = this;
        const canvas = document.getElementById("canvasElement");
        const width = canvas.clientWidth;
        const height = canvas.clientHeight;

        const scene = new THREE.Scene();
        scene.position.y = -1;
        const camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000 );

        camera.aspect = width / height;
        camera.updateProjectionMatrix();
        camera.position.z = 5;





        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize( window.innerWidth, window.innerHeight );
        }
        addEventListener('resize', onWindowResize)



        const renderer = new THREE.WebGLRenderer({canvas, antialias:true})
        renderer.setClearColor(0x000000, 0); // Set clear color to black with zero alpha
        renderer.setPixelRatio( window.devicePixelRatio );
        renderer.setSize( width, height );
        renderer.shadowMap.enabled = true; // Enable shadow maps

        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true; // Enable damping (inertia)
        controls.dampingFactor = 0.50;
        controls.screenSpacePanning = true;
        controls.maxPolarAngle = Math.PI / 2;

        const light = new THREE.DirectionalLight( 0xffffff, 5 );
        light.position.set(5,5,5 ); // Position the light source more from the right
        light.castShadow = true; // Enable casting shadows for the light
        scene.add( light );
        const ambientLight = new THREE.AmbientLight( 0x404040, 2 ); // Add ambient light for softer shadows
        scene.add( ambientLight );

        // const planeGeometry = new THREE.PlaneGeometry( 10, 10 );
        // const planeMaterial = new THREE.ShadowMaterial({ opacity: 1, color: 0x808080 });
        // const plane = new THREE.Mesh( planeGeometry, planeMaterial );
        // plane.rotation.x = - Math.PI / 2;
        // plane.position.y = 0;
        // plane.receiveShadow = true; // Enable receiving shadows for the plane
        // scene.add( plane );
        const planeGeometry = new THREE.PlaneGeometry( 10, 10 );
        const planeMaterial = new THREE.MeshStandardMaterial( { color: 0x000000 } );
        const plane = new THREE.Mesh( planeGeometry, planeMaterial );
        plane.rotation.x = - Math.PI / 2;
        plane.position.y = -1;
        plane.receiveShadow = true; // Enable receiving shadows for the plane
        scene.add( plane );

        function createSpheres(){
  
            
            for (let i = 0; i < 500; i++) {

                // const y = Math.abs(generateNormalDistribution(0.5, 1));
                const y = self.generateNormalDistribution(1, 1);
                const x = self.generateNormalDistribution(0, Math.abs(y) + (Math.random()/2));
                const z = self.generateNormalDistribution(0,  Math.abs(y));
                const radius = (z + 1) * 0.01 + (y + 1) * 0.01; // Scale radius based on z and y values

                const sphereGeometry = new THREE.SphereGeometry(radius, 32, 32);
                const color = self.interpolateColor(x);
               // const opacity = (z + 1) / 2; // Normalize z to range [0, 1] for opacity
                const opacity = 1;
                const sphereMaterial = new THREE.MeshStandardMaterial({ color: color, transparent: true, opacity: opacity });
                const smallSphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
                smallSphere.position.set(x, y, z);
                smallSphere.castShadow = true;
                scene.add(smallSphere);


            }
        }

        createSpheres();

        function animate() {
            requestAnimationFrame(animate);
            controls.update(); // Update controls
            // Update light position to match camera position
            light.position.copy(camera.position);
            renderer.render(scene, camera);
        }
        animate();
    },
    generateNormalDistribution(mean, stdDev) {
        let u = 0, v = 0;
        while (u === 0) u = Math.random(); // Converting [0,1) to (0,1)
        while (v === 0) v = Math.random();
        return mean + stdDev * Math.sqrt(-1.0 * Math.log(u)) * Math.cos(2.0 * Math.PI * v);
    },
    interpolateColor(x) {
        const t = (x + 1) / 2; // Normalize x to range [0, 1]
        const color = d3.interpolateRdBu(t);
        return new THREE.Color(color);
    }
},
mounted() {
    this.init();
}
}
  </script>
  
  <style scoped>

  
  </style>