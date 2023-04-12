<template>
    <!-- 音乐控制面板 -->
    <div
        v-show="store.musicOpenState"
        class="music"
        @mouseenter="volumeShow = true"
        @mouseleave="volumeShow = false"
    >
        <div class="btns">
            <span @click="musicListShow = true">音乐列表</span>
            <span @click="store.musicOpenState = false">回到一言</span>
        </div>
        <div class="control">
            <go-start
                fill="#efefef"
                size="30"
                theme="filled"
                @click="changeMusicIndex(0)"
            />
            <div class="state" @click="changePlayState">
                <play-one
                    v-show="!store.playerState"
                    fill="#efefef"
                    size="50"
                    theme="filled"
                />
                <pause
                    v-show="store.playerState"
                    fill="#efefef"
                    size="50"
                    theme="filled"
                />
            </div>
            <go-end
                fill="#efefef"
                size="30"
                theme="filled"
                @click="changeMusicIndex(1)"
            />
        </div>
        <div class="menu">
            <div v-show="!volumeShow" class="name">
        <span>{{
                store.getPlayerData.name
                    ? store.getPlayerData.name + " - " + store.getPlayerData.artist
                    : "未播放音乐"
            }}</span>
            </div>
            <div v-show="volumeShow" class="volume">
                <div class="icon">
                    <volume-mute
                        v-if="volumeNum == 0"
                        fill="#efefef"
                        size="24"
                        theme="filled"
                    />
                    <volume-small
                        v-else-if="volumeNum > 0 && volumeNum < 0.7"
                        fill="#efefef"
                        size="24"
                        theme="filled"
                    />
                    <volume-notice v-else fill="#efefef" size="24" theme="filled"/>
                </div>
                <el-slider
                    v-model="volumeNum"
                    :max="1"
                    :min="0"
                    :show-tooltip="false"
                    :step="0.01"
                />
            </div>
        </div>
    </div>
    <!-- 音乐列表弹窗 -->
    <Transition name="fade">
        <div
            v-show="musicListShow"
            class="music-list"
            @click="musicListShow = false"
        >
            <Transition name="zoom">
                <div v-show="musicListShow" class="list" @click.stop>
                    <close-one
                        class="close"
                        fill="#ffffff60"
                        size="28"
                        theme="filled"
                        @click="musicListShow = false"
                    />
                    <Player
                        ref="playerRef"
                        :shuffle="true"
                        :songId="playerData.id"
                        :songServer="playerData.server"
                        :songType="playerData.type"
                        :volume="volumeNum"
                    />
                </div>
            </Transition>
        </div>
    </Transition>
</template>

<script setup>
import {onMounted, reactive, ref, watch} from "vue";
import {CloseOne, GoEnd, GoStart, Pause, PlayOne, VolumeMute, VolumeNotice, VolumeSmall,} from "@icon-park/vue-next";
import Player from "@/components/Player/index.vue";
import {mainStore} from "@/store";

const store = mainStore();

// 音量条数据
let volumeShow = ref(false);
let volumeNum = ref(store.musicVolume ? store.musicVolume : 0.7);

// 播放列表数据
let musicListShow = ref(false);
const playerRef = ref(null);
const musicDialog = ref(null);
const playerData = reactive({
    server: import.meta.env.VITE_SONG_SERVER,
    type: import.meta.env.VITE_SONG_TYPE,
    id: import.meta.env.VITE_SONG_ID,
});

// 音乐播放暂停
const changePlayState = () => {
    playerRef.value.playToggle();
};

// 音乐上下曲
const changeMusicIndex = (type) => {
    playerRef.value.changeSong(type);
};

onMounted(() => {
    // 空格键事件
    window.addEventListener("keydown", (e) => {
        if (e.code == "Space") {
            changePlayState();
        }
    });
});

// 监听音量变化
watch(
    () => volumeNum.value,
    (value) => {
        store.musicVolume = value;
        playerRef.value.changeVolume(store.musicVolume);
    }
);
</script>

<style lang="scss" scoped>
.music {
    width: 100%;
    height: 100%;
    background: #00000040;
    backdrop-filter: blur(10px);
    border-radius: 6px;
    padding: 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: column;
    animation: fade;
    -webkit-animation: fade 0.5s;

    .btns {
        display: flex;
        align-items: center;
        margin-bottom: 6px;

        span {
            background: #ffffff26;
            padding: 2px 8px;
            border-radius: 6px;
            margin: 0px 6px;
            text-overflow: ellipsis;
            overflow-x: hidden;
            white-space: nowrap;

            &:hover {
                background: #ffffff4d;
            }
        }
    }

    .control {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-evenly;
        width: 100%;

        .state {
            .i-icon {
                width: 50px;
                height: 50px;
                display: block;
            }
        }

        .i-icon {
            width: 36px;
            height: 36px;
            display: flex;
            border-radius: 6px;
            align-items: center;
            justify-content: center;
            border-radius: 6px;
            transform: scale(1);

            &:hover {
                background: #ffffff33;
            }

            &:active {
                transform: scale(0.95);
            }
        }
    }

    .menu {
        height: 26px;
        width: 100%;
        line-height: 26px;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        .name {
            width: 100%;
            text-align: center;
            text-overflow: ellipsis;
            overflow-x: hidden;
            white-space: nowrap;
            animation: fade;
            -webkit-animation: fade 0.3s;
        }

        .volume {
            width: 100%;
            padding: 0 12px;
            display: flex;
            align-items: center;
            flex-direction: row;
            animation: fade;
            -webkit-animation: fade 0.3s;

            .icon {
                margin-right: 12px;

                span {
                    width: 24px;
                    height: 24px;
                    display: block;
                }
            }

            :deep(*) {
                transition: none;
            }

            :deep(.el-slider__button) {
                transition: 0.3s;
            }

            .el-slider {
                margin-right: 12px;
                --el-slider-main-bg-color: #efefef;
                --el-slider-runway-bg-color: #ffffff40;
                --el-slider-button-size: 16px;
            }
        }
    }
}

.music-list {
    position: fixed;
    top: 0;
    left: 0;
    margin: auto;
    width: 100%;
    height: 100%;
    background-color: #00000080;
    backdrop-filter: blur(20px);
    z-index: 1;

    .list {
        position: absolute;
        display: flex;
        align-items: center;
        justify-content: center;
        top: calc(50% - 300px);
        left: calc(50% - 320px);
        width: 640px;
        height: 600px;
        background-color: #ffffff66;
        border-radius: 6px;
        z-index: 999;
        @media (max-width: 720px) {
            left: calc(50% - 45%);
            width: 90%;
        }

        .close {
            position: absolute;
            top: 12px;
            right: 12px;
            width: 28px;
            height: 28px;
            display: block;

            &:hover {
                transform: scale(1.2);
            }

            &:active {
                transform: scale(0.95);
            }
        }
    }
}

// 弹窗动画
.fade-enter-active {
    animation: fade 0.3s ease-in-out;
}

.fade-leave-active {
    animation: fade 0.3s ease-in-out reverse;
}

.zoom-enter-active {
    animation: zoom 0.4s ease-in-out;
}

.zoom-leave-active {
    animation: zoom 0.3s ease-in-out reverse;
}

@keyframes zoom {
    0% {
        opacity: 0;
        transform: scale(0) translateY(-600px);
    }
    100% {
        opacity: 1;
        transform: scale(1) translateY(0);
    }
}
</style>
