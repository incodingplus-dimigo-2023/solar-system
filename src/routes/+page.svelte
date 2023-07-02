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
  let mercury: THREE.Mesh;
  let venus: THREE.Mesh;
  /**
   * 
   * @param a 원일점
   * @param b 근일점
   */
  const toE = (a:number, b:number) => (a - b) / (a + b); 
  /**
   * 
   * @param a 원일점
   * @param b 근일점
   * @param t 각도
   */
  const toR = (a:number, b:number, t:number) => {
    let e = toE(a, b);
    let jang = (a + b) / 2;
    return (jang * (1 - e * e)) / (1 + e * Math.cos(t));
  }
  const px = (a:number, b:number, t:number) => toR(a, b, t) * Math.cos(t);
  const py = (a:number, b:number, t:number) => toR(a, b, t) * Math.sin(t);
  onMount(() => {
    let id = 0;
    scene = new THREE.Scene();
    camera = new THREE.PerspectiveCamera(
      90,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    renderer = new THREE.WebGLRenderer({ canvas });
    renderer.setSize(window.innerWidth, window.innerHeight);
    //수
    const mercuryGeom = new THREE.SphereGeometry(12, 999, 999);
    const mercuryMater = new THREE.MeshBasicMaterial({ color: 0x87CEEB });
    mercury = new THREE.Mesh(mercuryGeom, mercuryMater);
    scene.add(mercury);
    //금
    const venusGeom = new THREE.SphereGeometry(20, 999, 999);
    const venusMater = new THREE.MeshBasicMaterial({ color: 0xffff99 });
    venus = new THREE.Mesh(venusGeom, venusMater);
    scene.add(venus);
    //태
    const sunGeom = new THREE.SphereGeometry(180, 999, 999);
    const sunMater = new THREE.MeshBasicMaterial({ color: 0xffee00 });
    sun = new THREE.Mesh(sunGeom, sunMater);
    scene.add(sun);
    //지
    const earthGeom = new THREE.SphereGeometry(24, 32, 32);
    const earthMater = new THREE.MeshBasicMaterial({ color: 0x0000ff });
    earth = new THREE.Mesh(earthGeom, earthMater);
    scene.add(earth);
    //달
    const moonGeom = new THREE.SphereGeometry(8, 16, 16);
    const moonMater = new THREE.MeshBasicMaterial({ color: 0xffffff });
    moon = new THREE.Mesh(moonGeom, moonMater);
    earth.add(moon);

    camera.position.set(0, 180, 0);
    camera.lookAt(scene.position);

    const animation = () => {
      sun.rotation.y += 0.01;
      earth.position.x = 8 * px(152.098_233, 147.098_291, Date.now() * 0.0003); //46,001,009
      earth.position.z = 8 * py(152.098_233, 147.098_291, Date.now() * 0.0003); //69,817,445
      moon.position.x = 50 * Math.cos(Date.now() * 0.002);  //0.055
      moon.position.z = 50 * Math.sin(Date.now() * 0.002);  
      mercury.position.x = 8 * px(69.817_445, 46.001_009, Date.now() * 0.002); //107,476,170
      mercury.position.z = 8 * py(69.817_445, 46.001_009, Date.now() * 0.002); //108,942,780
      venus.position.x = 8 * px(108.942_780, 107.476_170, Date.now() * 0.0009); //249,232,432
      venus.position.z = 8 * py(108.942_780, 107.476_170, Date.now() * 0.0009); //206,655,215
      renderer.render(scene, camera);
      id = requestAnimationFrame(animation);
    };
    id = requestAnimationFrame(animation);

    const handleMouseWheel = (event: WheelEvent) => {
      const delta = event.deltaY;
      const zoomSpeed = 0.1;

      camera.position.y += delta * zoomSpeed;
      camera.lookAt(scene.position);
    };

    canvas.addEventListener("wheel", handleMouseWheel);
    return () => {
      canvas.removeEventListener('wheel', handleMouseWheel);
      cancelAnimationFrame(id);
    }
  });
</script>

<canvas bind:this={canvas}></canvas>
<style>
  canvas {
    width: 100%;
    height: 100%;
    display: block;
  }
  body{
    background-image: url(./Textures/ju.jpg);
    
  }
</style>
