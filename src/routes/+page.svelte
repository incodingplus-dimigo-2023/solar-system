<script lang="ts">
  import { onMount } from "svelte";
  import { PointerLockControls } from "three/examples/jsm/controls/PointerLockControls";
  import * as THREE from "three";
  import { add_flush_callback, dataset_dev } from "svelte/internal";
  import { dev } from "$app/environment";

  let canvas: HTMLCanvasElement;
  let scene: THREE.Scene;
  let camera: THREE.PerspectiveCamera;
  let renderer: THREE.WebGLRenderer;
  let sun: THREE.Mesh;
  let earth: THREE.Mesh;
  let moon: THREE.Mesh;
  let mercury: THREE.Mesh;
  let venus: THREE.Mesh;
  let mars: THREE.Mesh;
  let jupiter: THREE.Mesh;
  let saturn: THREE.Mesh;
  let uranus: THREE.Mesh;
  let neptune: THREE.Mesh;
  let jupiterring: THREE.Mesh;
  let jupitering: THREE.Mesh;
  let europa: THREE.Mesh;
  let moveForward = false;
  let moveBack = false;
  let moveLeft = false;
  let moveRight = false;
  let cameraHeight = 180;
  const py = (t: number) => {
    return ((t * Math.PI) / 180);
  }
  const orbitalTiltAngle = 7;
  const orbitalTiltRadians = (orbitalTiltAngle * Math.PI) / 180;

  const toE = (a: number, b: number) => (a - b) / (a + b);
  const toR = (a: number, b: number, t: number) => {
    let e = toE(a, b);
    let jang = (a + b) / 2;
    return (jang * (1 - e * e)) / (1 + e * Math.cos(t));
  };

  const px = (a: number, b: number, t: number) => toR(a, b, t) * Math.cos(t); //x축 계산식
  const pz = (a: number, b: number, t: number) => toR(a, b, t) * Math.sin(t); //z축 계산식

  let controls: PointerLockControls;

  const onKeyDown = (event: KeyboardEvent) => {
    switch (event.code) {
      case "KeyW":
        moveForward = true;
        break;
      case "KeyA":
        moveLeft = true;
        break;
      case "KeyS":
        moveBack = true;
        break;
      case "KeyD":
        moveRight = true;
        break;
    }
  };

  const onKeyUp = (event: KeyboardEvent) => {
    switch (event.code) {
      case "KeyW":
        moveForward = false;
        break;
      case "KeyA":
        moveLeft = false;
        break;
      case "KeyS":
        moveBack = false;
        break;
      case "KeyD":
        moveRight = false;
        break;
    }
  };

  const onMouseWheel = (event: WheelEvent) => {
    console.log(event.deltaY);
    cameraHeight += event.deltaY / 60;
    camera.position.y = cameraHeight;
  };

  onMount(() => {
    
    camera = new THREE.PerspectiveCamera(
      90,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    controls = new PointerLockControls(camera, document.body);
    canvas.addEventListener("click", () => {
      controls.lock();
    });
    controls.addEventListener("lock", function () {
      console.log("lock");
    });

    controls.addEventListener("unlock", function () {
      console.log("unlock");
    });

    let id = 0;
    scene = new THREE.Scene();
    renderer = new THREE.WebGLRenderer({
      canvas: canvas,
      antialias: true,
    });

    // 태양
    const sunGeom = new THREE.SphereGeometry(40, 128, 128);
    const sunTextureLoader = new THREE.TextureLoader();
    const sunTexture = sunTextureLoader.load("/Textures/8k_sun.jpg");
    const sunMater = new THREE.MeshBasicMaterial({ map: sunTexture });
    const sun = new THREE.Mesh(sunGeom, sunMater);
    scene.add(sun);

    renderer.setSize(window.innerWidth, window.innerHeight);
    // 수성
    const mercuryGeom = new THREE.SphereGeometry(0.912, 128, 128);
    const mercuryTextureLoader = new THREE.TextureLoader()
    const mercuryTexture = mercuryTextureLoader.load("/Textures/8k_mercury.jpg")
    const mercuryMater = new THREE.MeshBasicMaterial({map: mercuryTexture});
    mercury = new THREE.Mesh(mercuryGeom, mercuryMater);
    sun.add(mercury);

    // 금성
    const venusGeom = new THREE.SphereGeometry(2.28, 128, 128);
    const venusTextureLoader = new THREE.TextureLoader();
    const venusTexture = venusTextureLoader.load("/Textures/venus.jpg");
    const venusMater = new THREE.MeshBasicMaterial({ map: venusTexture });
    venus = new THREE.Mesh(venusGeom, venusMater);
    sun.add(venus);

    // 지구
    const earthGeom = new THREE.SphereGeometry(2.4, 128, 128);
    const earthTextureLoader = new THREE.TextureLoader();
    const earthTexture = earthTextureLoader.load("/Textures/8k_earth_daymap.jpg");
    const earthMater = new THREE.MeshBasicMaterial({ map: earthTexture });
    earth = new THREE.Mesh(earthGeom, earthMater);
    sun.add(earth);

    // 달
    const moonGeom = new THREE.SphereGeometry(0.6, 128, 128);
    const moonTextureLoader = new THREE.TextureLoader();
    const moonTexture = moonTextureLoader.load("/Textures/8k_moon.jpg");
    const moonMater = new THREE.MeshBasicMaterial({ map: moonTexture });
    moon = new THREE.Mesh(moonGeom, moonMater);
    earth.add(moon);

    // 화성
    const marsGeom = new THREE.SphereGeometry(2, 128, 128);
    const marsTextureloader = new THREE.TextureLoader();
    const marsTexture = marsTextureloader.load("/Textures/Mars8k-modified.jpg");
    const marsMater = new THREE.MeshBasicMaterial({ map: marsTexture });
    mars = new THREE.Mesh(marsGeom, marsMater);
    sun.add(mars);

    //목성
    const JupiterGeom = new THREE.SphereGeometry(24, 128, 128);
    const JupiterTextureloader = new THREE.TextureLoader()
    const JupiterTexture = JupiterTextureloader.load("/Textures/jupiter.jpg");
    const JupiterMater = new THREE.MeshBasicMaterial({map: JupiterTexture});
    jupiter = new THREE.Mesh(JupiterGeom, JupiterMater);
    sun.add(jupiter);

    //목성 고리
    const jupiterringGeom = new THREE.RingGeometry(37, 34, 128);
    const jupiterringMater = new THREE.MeshBasicMaterial({
      color: 0x808080,
      side: THREE.DoubleSide,
    });
    jupiterring = new THREE.Mesh(jupiterringGeom, jupiterringMater);
    jupiter.add(jupiterring);

    const jupiteringGeom = new THREE.RingGeometry(40, 39, 128);
    const jupiteringMater = new THREE.MeshBasicMaterial({
      color: 0x808080,
      side: THREE.DoubleSide,
    });
    jupitering = new THREE.Mesh(jupiteringGeom, jupiteringMater);
    jupiter.add(jupitering);

    //유로파
    const europaGeom = new THREE.SphereGeometry(0.6, 128, 128);
    const europaMater = new THREE.MeshBasicMaterial({
      color: 0x767676,
    });
    europa = new THREE.Mesh(europaGeom, europaMater);
    jupiter.add(europa);

    //토성
    const SaturnGeom = new THREE.SphereGeometry(21.6, 128, 128);
    const SaturnTextureLoader = new THREE.TextureLoader();
    const SaturnTexture = JupiterTextureloader.load("/Textures/saturn.jpg");
    const SaturnMater = new THREE.MeshBasicMaterial({map: SaturnTexture});
    saturn = new THREE.Mesh(SaturnGeom, SaturnMater);
    sun.add(saturn);

    //천왕성
    const UranusGeom = new THREE.SphereGeometry(9.6, 128, 128);
    const UranusTextureLoader = new THREE.TextureLoader();
    const UranusTexture = UranusTextureLoader.load("Textures/2k_uranus.jpg");
    const UranusMater = new THREE.MeshBasicMaterial({map: UranusTexture});
    uranus = new THREE.Mesh(UranusGeom, UranusMater);
    sun.add(uranus);

    //해왕성
    const NeptuneGeom = new THREE.SphereGeometry(9.6, 128, 128);
    const NeptuneTextureLoader = new THREE.TextureLoader();
    const NepturnTexture = NeptuneTextureLoader.load("Textures/2k_neptune.jpg");
    const NeptuneMater = new THREE.MeshBasicMaterial({map: NepturnTexture});
    neptune = new THREE.Mesh(NeptuneGeom, NeptuneMater);
    sun.add(neptune)

    camera.position.set(0, 200, 200);
    camera.lookAt(0, 0, 0);
    const animation = (t:number) => {
      sun.rotation.y -= 0.01;
      sun.rotation.z = orbitalTiltRadians;
      sun.rotation.x = orbitalTiltRadians;
      earth.position.x = 1.2 * px(152.098_233, 147.098_291, Date.now() * 0.00007) ; //46,001,009
      earth.position.z = 1.2 * pz(152.098_233, 147.098_291, Date.now() * 0.00007) ; //69,817,445
      earth.position.y = -10;
      earth.rotation.y += 0.4;
      moon.position.x = 7.5 * Math.cos(Date.now() * 0.004); //0.005
      moon.position.z = 7.5 * Math.sin(Date.now() * 0.004); //0.005
      mercury.position.x = 1.2 * px(69.817_445, 46.001_009, Date.now() * 0.00003); //103.382.209
      mercury.position.z = 1.2 * pz(69.817_445, 46.001_009, Date.now() * 0.00003);
      mercury.rotation.y += 0.04;
      venus.position.x = 1.2 * px(108.942_780, 107.476_170, Date.now() * 0.000021); //249,232,432
      venus.position.z = 1.2 * pz(108.942_780, 107.476_170, Date.now() * 0.000021); //214,923,921
      venus.rotation.y += 0.04;
      mars.position.x = 1.2 * px(206.700_000, 249.200_000, Date.now() * 0.000007); //206.700_000
      mars.position.z = 1.2 * pz(206.700_000, 249.200_000, Date.now() * 0.000007); //249.200_000
      mars.rotation.y += 0.04;
      jupiter.position.x = 1.2 * px(740.573_600, 816.520_800, Date.now() * 0.00009); //740,573,600
      jupiter.position.z = 1.2 * pz(740.573_600, 816.520_800, Date.now() * 0.00009); //816,520,800
      jupiter.rotation.x = py(90)
      europa.position.x = 7.5 * Math.cos(Date.now() * 0.001); //0.005
      europa.position.z = 7.5 * Math.sin(Date.now() * 0.001); //0.005
      saturn.position.x = 1.2 * px(1.349_467 * 1000, 1.503_983 * 1000, Date.now() * 0.000017); //1,349,467,375
      saturn.position.z = 1.2 * pz(1.426_725 * 1000, 1.503_983 * 1000, Date.now() * 0.000017); //1,503,983,449
      saturn.rotation.y += 0.03;
      uranus.position.x = 1.2 * px(4.540_000 * 1000, 4.460_000 * 1000, Date.now() * 0.00011); //4,540,000,000
      uranus.position.z = 1.2 * pz(4.540_000 * 1000, 4.460_000 * 1000, Date.now() * 0.00011); //4.460,000,000
      neptune.position.x = 1.2 * px(4.459_631 * 1000, 4.536_874 * 1000, Date.now() * 0.00011); //4.459_631_496
      neptune.position.z = 1.2 * pz(4.459_631 * 1000, 4.536_874 * 1000, Date.now() * 0.00011); //4.536_874_325
      neptune.rotation.y += 0.03;
      const moveSpeed = 3;
      if (moveBack) {
        controls.moveForward(-moveSpeed);
      }
      if (moveLeft) {
        controls.moveRight(-moveSpeed);
      }
      if (moveRight) {
        controls.moveRight(moveSpeed);
      }
      if (moveForward) {
        controls.moveForward(moveSpeed);  
      }
      renderer.render(scene, camera);
      id = requestAnimationFrame(animation);
    };
    id = requestAnimationFrame(animation);
    document.addEventListener("keydown", onKeyDown);
    document.addEventListener("keyup", onKeyUp);
    canvas.addEventListener("wheel", onMouseWheel);
    canvas.addEventListener("keydown", onKeyDown);
    canvas.addEventListener("keyup", onKeyUp);
  });
</script>

<canvas bind:this={canvas} style="pointer-events: auto;" />
<div class="Wireframe">
  <div class="Ellipse">
    <div class="Polygon" />
  </div>
  <div>
    <select class="Rectangle">
      <option value="Sun">Sun</option>
      <option value="Mercury">Mercury</option>
      <option value="Venus">Venus</option>
      <option value="Earth">Earth</option>
      <option value="Mars">Mars</option>
      <option value="Jupiter">Jupiter</option>
      <option value="Saturn">Saturn</option>
      <option value="Uranus">Uranus</option>
      <option value="Neptune">Neptune</option>
    </select>
    <div class="planet">planet</div>
  </div>
  <div class="Rectangle-1">
    <div class="speed">speed</div>
  </div>
  <div class="Rectangle-2">
    <div class="system-size">system-size</div>
  </div>
  <div class="Rectangle-3">
    <div class="date">date</div>
  </div>
</div>

<style>
  canvas {
    background: url(8k_stars_milky_way.jpg);
  }
  select {
    position: absolute;
    top: 120px;
    left: 10px;
    font-size: 1px;
    border-color: transparent;
  }
  option {
    font-size: 9px;
  }
  #c {
    width: 100%;
    height: 100%;
    display: block;
    background: url(8k_stars_milky_way.jpg) no-repeat center center;
    background-size: cover;
  }
  .Wireframe {
    position: fixed;
    width: 155px;
    height: 1024px;
    top: 0;
    background-color: rgba(162, 156, 156, 0.2);
  }
  .Wireframe > div {
    margin-left: 10px;
    margin-top: 30px;
  }
  .system-size {
    margin-bottom: 30px;
    margin-right: 80px;
    width: 200px;
    height: 12.5px;
    font-weight: 400;
    font-size: 10px;
    line-height: 10px;
    color: #eceaea;
  }

  .Polygon {
    box-sizing: border-box;
    border-bottom: 9px solid transparent;
    border-top: 9px solid transparent;
    border-left: 15px solid rgb(0, 0, 0);
  }
  .planet {
    margin-bottom: 50px;
    margin-right: 80px;
    width: 200px;
    height: 12.5px;
    font-weight: 400;
    font-size: 10px;
    line-height: 10px;
    color: #eceaea;
  }
  .date {
    margin-bottom: 30px;
    margin-right: 80px;
    width: 200px;
    height: 12.5px;
    font-weight: 400;
    font-size: 10px;
    line-height: 10px;
    color: #eceaea;
  }
  .speed {
    margin-bottom: 30px;
    margin-right: 80px;
    width: 200px;
    height: 12.5px;
    font-weight: 400;
    font-size: 1px;
    line-height: 10px;
    color: #eceaea;
  }

  .Ellipse {
    border-radius: 20px;
    box-sizing: border-box;
    width: 37.5px;
    height: 37px;
    left: 10px;
    top: 28px;
    background: #d9d9d9;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .Rectangle {
    box-sizing: border-box;
    width: 135px;
    background: #4d4d4d;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .Rectangle-1 {
    box-sizing: border-box;
    width: 135px;
    height: 11px;
    background: #4f4e4e;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .Rectangle-2 {
    box-sizing: border-box;
    width: 135px;
    height: 11px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #4b4b4b;
  }

  .Rectangle-3 {
    box-sizing: border-box;
    width: 135px;
    height: 11px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #4b4b4b;
  }
</style> 