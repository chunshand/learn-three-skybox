<template>
    <ToolsComponent @changeskybox="changeskybox" @changehdr="changehdr" @changevideo="changevideo">
        <ThreeViewComponent ref="threeView" />
    </ToolsComponent>
</template>
<script setup lang="ts">
import { ref } from "vue"
import ToolsComponent from "./components/tools.vue"
import ThreeViewComponent from "./components/three-view.vue"
const threeView = ref();
const changeskybox = (arr: any) => {
    threeView.value.handleSetSkyBox(arr);
}
const changehdr = (value: any) => {
    threeView.value.handleSetHDR(value);
}
const changevideo = (value: any) => {
    threeView.value.handleSetVideo(value);
}

</script>
<style scoped>
*,
html,
body {
    padding: 0;
    margin: 0;
}
</style>
