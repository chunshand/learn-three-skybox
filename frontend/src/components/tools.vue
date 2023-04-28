<template>
    <div class="main">
        <div class="tools-container" :class="{ 'tools-container-show': show }">
            <div>
                <a-button type="primary" @click="handleOpenSkyBoxModal">天空盒子</a-button>
            </div>
            <div>
                <a-button type="primary" @click="handleOpenHDRModal">HDR</a-button>
            </div>
            <div>
                <a-button type="primary" @click="handleSetVideo">全景视频</a-button>
            </div>
        </div>
        <slot></slot>
        <div class="note">
            <p>鼠标左键点击拖动视图,空格键打开工具</p>
        </div>
        <div style="position: fixed;">
            <video preload="" ref="video" controls loop style="width: 100%; visibility: hidden; position: absolute"
                :src="videoSrc"></video>
        </div>
        <div ref="modal">
        </div>
        <a-modal v-model:visible="skyboxvisible" title="天空盒子" :getContainer="() => $refs.modal" @ok="handleSetSkyBox">
            <div class="sky-upload-container">
                <div>
                    <div class="avatar-uploader">
                    </div>
                    <a-upload name="avatar" list-type="picture-card" class="avatar-uploader" :show-upload-list="false"
                        :before-upload="(info: any) => { return handleChange(0, info) }">
                        <img v-if="!skyBoxList[0].loading && skyBoxList[0].image" :src="skyBoxList[0].image" />
                        <div v-else>
                            <loading-outlined v-if="skyBoxList[0].loading"></loading-outlined>
                            <plus-outlined v-else></plus-outlined>
                            <div class="ant-upload-text">左边</div>
                        </div>
                    </a-upload>
                    <div class="avatar-uploader">
                    </div>
                </div>
                <div>
                    <a-upload name="avatar" list-type="picture-card" class="avatar-uploader" :show-upload-list="false"
                        :before-upload="(info: any) => { return handleChange(2, info) }">
                        <img v-if="!skyBoxList[2].loading && skyBoxList[2].image" :src="skyBoxList[2].image" />
                        <div v-else>
                            <loading-outlined v-if="skyBoxList[2].loading"></loading-outlined>
                            <plus-outlined v-else></plus-outlined>
                            <div class="ant-upload-text">上边</div>
                        </div>
                    </a-upload>
                    <a-upload name="avatar" list-type="picture-card" class="avatar-uploader" :show-upload-list="false"
                        :before-upload="(info: any) => { return handleChange(5, info) }">
                        <img v-if="!skyBoxList[5].loading && skyBoxList[5].image" :src="skyBoxList[5].image" />
                        <div v-else>
                            <loading-outlined v-if="skyBoxList[5].loading"></loading-outlined>
                            <plus-outlined v-else></plus-outlined>
                            <div class="ant-upload-text">前边</div>
                        </div>
                    </a-upload>
                    <a-upload name="avatar" list-type="picture-card" class="avatar-uploader" :show-upload-list="false"
                        :before-upload="(info: any) => { return handleChange(3, info) }">
                        <img v-if="!skyBoxList[3].loading && skyBoxList[3].image" :src="skyBoxList[3].image" />
                        <div v-else>
                            <loading-outlined v-if="skyBoxList[3].loading"></loading-outlined>
                            <plus-outlined v-else></plus-outlined>
                            <div class="ant-upload-text">下边</div>
                        </div>
                    </a-upload>
                </div>
                <div>
                    <div class="avatar-uploader">
                    </div>
                    <a-upload name="avatar" list-type="picture-card" class="avatar-uploader" :show-upload-list="false"
                        :before-upload="(info: any) => { return handleChange(1, info) }">
                        <img v-if="!skyBoxList[1].loading && skyBoxList[1].image" :src="skyBoxList[1].image" />
                        <div v-else>
                            <loading-outlined v-if="skyBoxList[1].loading"></loading-outlined>
                            <plus-outlined v-else></plus-outlined>
                            <div class="ant-upload-text">右边</div>
                        </div>
                    </a-upload>
                </div>
                <div>
                    <div class="avatar-uploader">
                    </div>
                    <a-upload name="avatar" list-type="picture-card" class="avatar-uploader" :show-upload-list="false"
                        :before-upload="(info: any) => { return handleChange(4, info) }">
                        <img v-if="!skyBoxList[4].loading && skyBoxList[4].image" :src="skyBoxList[4].image" />
                        <div v-else>
                            <loading-outlined v-if="skyBoxList[4].loading"></loading-outlined>
                            <plus-outlined v-else></plus-outlined>
                            <div class="ant-upload-text">后边</div>
                        </div>
                    </a-upload>
                </div>
            </div>
        </a-modal>

        <a-modal v-model:visible="hdrvisible" title="HDR" :getContainer="() => $refs.modal" @ok="handleSetHDR">
            <a-upload name="avatar" list-type="picture-card" class="avatar-uploader" :show-upload-list="false"
                :before-upload="handleHdrChange">
                <div v-if="hdrUrl">
                    已上传
                </div>
                <div v-else>
                    <div class="ant-upload-text">点击上传</div>
                </div>
            </a-upload>
        </a-modal>
    </div>
</template>

<script lang="ts" setup>
import { ref, nextTick, watch } from "vue"
import { useMagicKeys } from '@vueuse/core'

const emit = defineEmits(['changeskybox', 'changehdr', 'changevideo'])
const modal = ref();
const show = ref(false);
const skyboxvisible = ref(false);
const hdrvisible = ref(false);
const hdrUrl = ref('');
const videoSrc = ref('/mp4.mp4')
const video = ref();
const DefaultImage = [
    {
        image: '',
        loading: false
    },
    {
        image: '',
        loading: false
    },
    {
        image: '',
        loading: false
    },
    {
        image: '',
        loading: false
    },
    {
        image: '',
        loading: false
    },
    {
        image: '',
        loading: false
    }
]
const { space } = useMagicKeys()
watch(space, (v: any) => {
    console.log(v);
    if (v) {
        show.value = !show.value;
    }

})
const skyBoxList = ref(JSON.parse(JSON.stringify(DefaultImage)))
const handleOpenSkyBoxModal = () => {
    skyBoxList.value = JSON.parse(JSON.stringify(DefaultImage));
    nextTick(() => {

        skyboxvisible.value = true;
    })
}
function getBase64(img: Blob, callback: (base64Url: string) => void) {
    const reader = new FileReader();
    reader.addEventListener('load', () => callback(reader.result as string));
    reader.readAsDataURL(img);
}

const handleChange = (index: number, file: File) => {
    const data = skyBoxList.value[index];
    data.loading = true;
    // Get this url from response in real world.
    getBase64(file, (base64Url: string) => {
        data.image = base64Url;
        data.loading = false;
    });
    return false;
};
const handleSetSkyBox = () => {
    console.log(skyBoxList.value);
    let arr: any[] = skyBoxList.value.map((item: any) => {
        return item.image
    })
    skyboxvisible.value = false;
    if (arr.filter((t) => t).length > 0) {
        nextTick(() => {
            emit('changeskybox', arr);
        })
    }

}
// ------------------------------------------------------------hdr
const handleOpenHDRModal = () => {
    nextTick(() => {
        hdrvisible.value = true;
    })
}
const handleSetHDR = () => {

    hdrvisible.value = false;
    if (hdrUrl.value) {
        nextTick(() => {
            emit('changehdr', hdrUrl.value);
        })
    }

}
const handleHdrChange = (file: any) => {
    getBase64(file, (base64Url: string) => {
        hdrUrl.value = base64Url;
    });
}

const handleSetVideo = () => {
    emit('changevideo', video.value);
}
</script>

<style lang="scss" scoped>
.main {
    width: 100vw;
    height: 100vh;



    .tools-container {
        width: 120px;
        height: 320px;
        position: fixed;
        right: 0;
        display: flex;
        align-items: center;
        background-color: #323232;
        padding: 32px 16px;
        box-sizing: border-box;
        flex-direction: column;
        transition: all 0.3s ease;
        transform: translateX(100%);
        box-sizing: border-box;

        >div {
            padding: 16px 0;
        }
    }

    .tools-container-show {
        transform: translateX(0%);
    }

    .note {
        position: fixed;
        right: 0;
        bottom: 0;
        font-size: 12px;
        color: #dddddd;
        text-align: right;
        padding: 0 24px;
    }

    .sky-upload-container {
        display: flex;

        >div {
            display: flex;
            flex-direction: column;
        }

        .avatar-uploader {
            width: 128px;
            height: 128px;

            img {
                width: 100%;
                height: 100%;
            }
        }

        .avatar-uploader>.ant-upload {
            width: 128px;
            height: 128px;
        }

        .ant-upload-select-picture-card i {
            font-size: 32px;
            color: #999;
        }

        .ant-upload-select-picture-card .ant-upload-text {
            color: #666;
        }
    }
}
</style>