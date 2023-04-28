<template>
    <div class="three-view-container" ref="canvas">
    </div>
</template>

<script lang="ts" setup>
import * as THREE from "three"
import { ref, onMounted, nextTick, defineExpose } from "vue"
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader.js";
let videomesh: any = null;
const canvas = ref();
// 场景
const scene = new THREE.Scene();
// 渲染器
const renderer = new THREE.WebGLRenderer();
const DefaultImage = [
    // 左边
    '/l.png',
    // 右边
    '/r.png',
    // 上边
    '/u.png',
    // 下边
    '/d.png',
    // 后边
    '/b.png',
    // 前边
    '/f.png',
]
const handleInit = () => {
    // 宽
    let innerWidth = canvas.value.offsetWidth;
    // 高
    let innerHeight = canvas.value.offsetHeight;
    // 设置场景为白色
    scene.background = new THREE.Color('#ffffff');
    handleSetSkyBox();
    // 摄像机
    const camera = new THREE.PerspectiveCamera(75, innerWidth / innerHeight, 0.1, 1000);
    camera.position.z = 5;
    camera.position.y = 1;

    // 设置渲染器大小
    renderer.setSize(innerWidth, innerHeight);


    // renderer.domElement 实际是 canvas dom 对象
    canvas.value.appendChild(renderer.domElement)

    const controls = new OrbitControls(camera, renderer.domElement);
    controls.update();
    function animate() {
        // 下一帧执行
        requestAnimationFrame(animate);
        controls.update();
        // 渲染器渲染场景
        renderer.render(scene, camera);
    }

    animate();
    window.addEventListener('resize', () => {
        let innerWidth = canvas.value.offsetWidth;
        let innerHeight = canvas.value.offsetHeight;
        renderer.setSize(innerWidth, innerHeight);
    });
}
const handleClear = () => {
    scene.remove(videomesh);
}
const handleSetSkyBox = (arr: any[] = []) => {
    handleClear();
    let data: any[] = [];
    for (let i in DefaultImage) {
        data[i] = arr[i] ? arr[i] : DefaultImage[i]
    }
    console.log(data);
    const loader = new THREE.CubeTextureLoader();
    const texture = loader.load(data);
    scene.background = texture;
}
// HDR -------------------------------------------------
const handleSetHDR = (value = null) => {
    handleClear();

    let data = value ?? '/default.hdr'
    const pmremGenerator = new THREE.PMREMGenerator(renderer); // 使用hdr作为背景色
    pmremGenerator.compileEquirectangularShader();

    new RGBELoader()
        // .setDataType(THREE.UnsignedByteType)
        .load(data, function (texture: any) {
            const envMap = pmremGenerator.fromEquirectangular(texture).texture;
            // envMap.isPmremTexture = true;
            pmremGenerator.dispose();

            scene.environment = envMap; // 给场景添加环境光效果
            scene.background = envMap; // 给场景添加背景图
        });
}

// VRvideo --------------------------------------------------
const handleSetVideo = (refvideo: any) => {
    handleClear();
    // mp4.mp4
    const videoTexture = new THREE.VideoTexture(refvideo);
    videoTexture.needsUpdate = true;
    videoTexture.updateMatrix();
    const geometry = new THREE.SphereGeometry(100, 32, 16);

    const material = new THREE.ShaderMaterial({
        wireframe: false,
        side: THREE.DoubleSide,
        // @ts-ignore
        map: videoTexture,
        uniforms: {
            tex_0: new THREE.Uniform(videoTexture),
        },
        vertexShader: `
precision highp float;
varying vec2 v_uv;
void main() {
gl_Position = projectionMatrix *
    modelViewMatrix *
    vec4(position.xyz, 1.0);
v_uv = uv;
}
`,
        fragmentShader: `
precision highp float;
varying vec2 v_uv;
uniform sampler2D tex_0;
void main() {
    vec4 texColor = texture2D(tex_0, vec2(1. - v_uv.x, v_uv.y));
    gl_FragColor = texColor;
}
`,
    });
    videomesh = new THREE.Mesh(geometry, material);
    scene.add(videomesh);
    nextTick(() => {
        refvideo.play()
    })
}

defineExpose({
    handleSetSkyBox,
    handleSetHDR,
    handleSetVideo
})
onMounted(() => {
    nextTick(() => {
        handleInit();
    })
})
</script>

<style lang="scss" scoped>
.three-view-container {
    width: 100vw;
    height: 100vh;
    cursor: pointer;
}
</style>