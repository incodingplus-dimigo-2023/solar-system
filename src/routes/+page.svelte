<script lang="ts">
  import { onMount } from "svelte";
  import * as THREE from "three";

  let canvas: HTMLCanvasElement;
  let scene: THREE.Scene;
  let camera: THREE.PerspectiveCamera;
  let renderer: THREE.WebGLRenderer;
  let sun: THREE.Mesh;
  let earth: THREE.Mesh;
  let moon: THREE.Mesh;

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

    const sunGeom = new THREE.SphereGeometry(30, 999, 999);
    const sunMater = new THREE.MeshBasicMaterial({ color: 0xffee00 });
    sun = new THREE.Mesh(sunGeom, sunMater);
    scene.add(sun);

    const earthGeom = new THREE.SphereGeometry(3, 32, 32);
    const earthMater = new THREE.MeshBasicMaterial({ color: 0x0000ff });
    earth = new THREE.Mesh(earthGeom, earthMater);
    scene.add(earth);

    const moonGeom = new THREE.SphereGeometry(1, 16, 16);
    const moonMater = new THREE.MeshBasicMaterial({ color: 0xffffff });
    moon = new THREE.Mesh(moonGeom, moonMater);
    earth.add(moon);

    camera.position.set(0, 180, 0);
    camera.lookAt(scene.position);

    const animation = () => {
      requestAnimationFrame(animation);
      sun.rotation.y += 0.01;
      earth.position.x = 150 * Math.cos(Date.now() * 0.001);
      earth.position.z = 150 * Math.sin(Date.now() * 0.001);
      moon.position.x = 15 * Math.cos(Date.now() * 0.005);
      moon.position.z = 15 * Math.sin(Date.now() * 0.005);
      renderer.render(scene, camera);
    };

    animation();
  });
</script>

<canvas bind:this={canvas}></canvas>
<style>
  canvas {
    width: 100%;
    height: 100%;
    display: block;
  }
</style>
