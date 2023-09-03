<script lang="ts">
  import { onMount } from "svelte";
  import { PointerLockControls } from "three/examples/jsm/controls/PointerLockControls";
  import * as THREE from "three";

  let canvas: HTMLCanvasElement;
  let scene: THREE.Scene;
  let camera: THREE.PerspectiveCamera;
  let renderer: THREE.WebGLRenderer;;
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
  let moveForward = false;
  let moveBack = false;
  let moveLeft = false;
  let moveRight = false;
  let cameraHeight = 180;
  let planetObjects: THREE.Mesh[] = [];
  const planetsData = [
    {
      id: "1",
      name: "sun",
      radius: 2.4,
      texturePath: "Agni-Baume-land.jpg",
    },
    {
      id: "12",
      name: "mercury",
      radius: 0.912,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "13",
      name: "venus",
      radius: 2.28,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "14",
      name: "earth",
      radius: 2.4,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "141",
      name: "moon",
      radius: 0.6,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "15",
      name: "mars",
      radius: 2,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "16",
      name: "jupiter",
      radius: 24,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "17",
      name: "saturn",
      radius: 21.6,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "18",
      name: "uranus",
      radius: 9.6,
      texturePath: "EarthMap_2500x1250.jpg",
    },
    {
      id: "19",
      name: "neptune",
      radius: 9.6,
      texturePath: "EarthMap_2500x1250.jpg",
    },
  ];

  function createPlanet(data: any) {
    const textureLoader = new THREE.TextureLoader();
    const texture = textureLoader.load(data.texturePath);

    const geometry = new THREE.SphereGeometry(data.radius, 128, 128);
    const material = new THREE.MeshBasicMaterial({ map: texture });

    const planet = new THREE.Mesh(geometry, material);
    scene.add(planet);
    return planet;
  }
  const toE = (a: number, b: number) => (a - b) / (a + b);
  const toR = (a: number, b: number, t: number) => {
    let e = toE(a, b);
    let jang = (a + b) / 2;
    return (jang * (1 - e * e)) / (1 + e * Math.cos(t));
  };
  
  const px = (a: number, b: number, t: number) => toR(a, b, t) * Math.cos(t);
  const py = (a: number, b: number, t: number) => toR(a, b, t) * Math.sin(t);

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
    cameraHeight += event.deltaY / 50;
    camera.position.y = cameraHeight;
  };

  onMount(() => {
    camera = new THREE.PerspectiveCamera(
      90,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    )
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
        antialias:true
     });
    renderer.setSize(window.innerWidth, window.innerHeight);

    // 수성
    const mercuryGeom = new THREE.SphereGeometry(0.912, 128, 128);
    const mercuryMater = new THREE.MeshBasicMaterial({ color: 0x87ceeb });
    mercury = new THREE.Mesh(mercuryGeom, mercuryMater);
    scene.add(mercury);

    // 금성
    const venusGeom = new THREE.SphereGeometry(2.28, 128, 128);
    const venusTextureLoader = new THREE.TextureLoader();
    const venusTexture = venusTextureLoader.load("Textures/Atmosphere_2K.png");
    const venusMater = new THREE.MeshBasicMaterial({ map: venusTexture });
    venus = new THREE.Mesh(venusGeom, venusMater);
    scene.add(venus);

    // 태양
    const sunGeom = new THREE.SphereGeometry(40, 128, 128);
    const sunTextureLoader = new THREE.TextureLoader();
    const sunTexture = sunTextureLoader.load("Textures/Agni-Baume-land.jpg");
    const sunMater = new THREE.MeshBasicMaterial({ map: sunTexture });
    const sun = new THREE.Mesh(sunGeom, sunMater);
    scene.add(sun);

    // 지구

    // 달
    const moonGeom = new THREE.SphereGeometry(0.6, 128, 128);
    const moonTextureLoader = new THREE.TextureLoader();
    const moonTexture = moonTextureLoader.load("Textures/moon_8k_color_brim16.jpg");
    const moonMater = new THREE.MeshBasicMaterial({ map: moonTexture });
    moon = new THREE.Mesh(moonGeom, moonMater);
    earth.add(moon);

    // 화성
    const marsGeom = new THREE.SphereGeometry(2, 128, 128);
    const marsTextureloader = new THREE.TextureLoader();
    const marsTexture = marsTextureloader.load("Textures/Mars8k-modified.jpg");
    const marsMater = new THREE.MeshBasicMaterial({ map: marsTexture });
    mars = new THREE.Mesh(marsGeom, marsMater);
    scene.add(mars);

    //목성
    const JupiterGeom = new THREE.SphereGeometry(24, 128, 128);
    const JupiterMater = new THREE.MeshBasicMaterial({ color: 0xf8b878 });
    jupiter = new THREE.Mesh(JupiterGeom, JupiterMater);
    scene.add(jupiter);

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

    //토성
    const SaturnGeom = new THREE.SphereGeometry(21.6, 128, 128);
    const SaturnMater = new THREE.MeshBasicMaterial({ color: 0xf8b978 });
    saturn = new THREE.Mesh(SaturnGeom, SaturnMater);
    scene.add(saturn);

    //천왕성
    const UranusGeom = new THREE.SphereGeometry(9.6, 128, 128);
    const UranusMater = new THREE.MeshBasicMaterial({ color: 0xDCD7AF });
    uranus = new THREE.Mesh(UranusGeom, UranusMater);
    scene.add(uranus);

    //해왕성
    const NeptuneGeom = new THREE.SphereGeometry(9.6, 128, 128);
    const NeptuneMater = new THREE.MeshBasicMaterial({ color: 0x87ceeb });
    neptune = new THREE.Mesh(NeptuneGeom, NeptuneMater);

    for (const planetData of planetsData) {
      const planet = createPlanet(planetData);
      planetObjects.push(planet);
    }

    camera.position.set(0, 200, 200);
    camera.lookAt(0, 0, 0);
    
    const animation = () => {
      sun.rotation.y += 0.01;
      earth.position.x = 1.2 * px(152.098_233, 147.098_291, Date.now() * 0.00005); //46,001,009
      earth.position.z = 1.2 * py(152.098_233, 147.098_291, Date.now() * 0.00005); //69,817,445
      earth.rotation.y -= 0.1;
      moon.position.x = 7.5 * Math.cos(Date.now() * 0.001); //0.005
      moon.position.z = 7.5 * Math.sin(Date.now() * 0.001); //0.005
      mercury.position.x = 1.2 * px(69.817_445, 46.001_009, Date.now() * 0.0003); //103.382.209
      mercury.position.z = 1.2 * py(69.817_445, 46.001_009, Date.now() * 0.0003); //  
      venus.position.x = 1.2 * px(108.942_780, 107.476_170, Date.now() * 0.00011); //249,232,432
      venus.position.z = 1.2 * py(108.942_780, 107.476_170, Date.now() * 0.00011); //214,923,921
      mars.position.x = 1.2 * px(206.700_000, 249.200_000, Date.now() * 0.00007); //206.700_000
      mars.position.z = 1.2 * py(206.700_000, 249.200_000, Date.now() * 0.00007); //249.200_000
      mars.rotation.y += 0.1;
      jupiter.position.x = 1.2 * px(740.573_600, 816.520_800, Date.now() * 0.00009); //740,573,600
      jupiter.position.z = 1.2 * py(740.573_600, 816.520_800, Date.now() * 0.00009); //816,520,800
      saturn.position.x = 1.2 * px(1.349_467 * 1000, 1.503_983 * 1000, Date.now() * 0.000017); //1,349,467,375
      saturn.position.z = 1.2 * py(1.426_725 * 1000, 1.503_983 * 1000, Date.now() * 0.000017); //1,503,983,449
      uranus.position.x = 1.2 * px(4.540_000 * 1000, 4.460_000 * 1000, Date.now() * 0.000111); //4,540,000,000
      uranus.position.z = 1.2 * py(4.540_000 * 1000, 4.460_000 * 1000, Date.now() * 0.000111); //4.460,000,000
      neptune.position.x = 1.2 * px(4.459_631 * 1000, 4.536_874 * 1000, Date.now() * 0.000110); //4.459_631_496
      neptune.position.z = 1.2 * py(4.459_631 * 1000, 4.536_874 * 1000, Date.now() * 0.000110); //4.536_874_325
      const moveSpeed = 3;
      if (moveForward) {
        controls.moveForward(moveSpeed);
      }
      if (moveBack) {
        controls.moveForward(-moveSpeed);
      }
      if (moveLeft) {
        controls.moveRight(-moveSpeed);
      }
      if (moveRight) {
        controls.moveRight(moveSpeed);
      }
      renderer.render(scene, camera);
      id = requestAnimationFrame(animation);
    };
    jupitering.rotation.z = 180;
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
  canvas {
    width: 100%;
    height: 100%;
    display: block;
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
