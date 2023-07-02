<script lang="ts">
    import { onMount } from "svelte";
    import * as THREE from "three";
  
    let canvas: HTMLCanvasElement;
    let scene: THREE.Scene;
    let camera: THREE.PerspectiveCamera;
    let renderer: THREE.WebGLRenderer;
    let sun: THREE.Mesh;
    let earth: THREE.Mesh;

    onMount(() => {
      scene = new THREE.Scene();
      camera = new THREE.PerspectiveCamera(
        90,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      renderer = new THREE.WebGLRenderer({ canvas });
      renderer.setSize(window.innerWidth, window.innerHeight);
  
      const sungeom = new THREE.SphereGeometry(30, 999, 999);
      const sunmater = new THREE.MeshBasicMaterial({ color: 0xffee00 });
      sun = new THREE.Mesh(sungeom, sunmater);
      scene.add(sun);
  
      const earthgeom = new THREE.SphereGeometry(3, 32, 32);
      const earthmater = new THREE.MeshBasicMaterial({ color: 0x0000ff });
      earth = new THREE.Mesh(earthgeom, earthmater);
      scene.add(earth);
      
      camera.position.set(0, 180, 0);
      camera.lookAt(scene.position);
      const ani = () => {
        requestAnimationFrame(ani);
        sun.rotation.y += 0.01;
        earth.position.x = 150 * Math.cos(Date.now() * 0.001);
        earth.position.z = 150 * Math.sin(Date.now() * 0.001);
        renderer.render(scene, camera);
      };
      ani();
    });
  </script>
  
  <canvas bind:this={canvas}></canvas>
  