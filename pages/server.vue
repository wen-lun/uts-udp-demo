<template>
    <view class="content">
        <view class="btns">
            <button v-if="!isStart" type="primary" size="mini" @click="onEnableClick">启动UDP服务</button>
            <button v-else type="warn" size="mini" @click="onDisableClick">停止UDP服务</button>
        </view>
        <view class="status">{{ isStart ? 'UDP服务已启动' : 'UDP服务未启动' }}</view>
        <view class="box">
            <view v-for="item in messages" class="item">
                <view class="info">
                    <text class="label">主机：{{ item.host }}</text>
                    <text class="label">端口：{{ item.port }}</text>
                </view>
                <view class="msg">消息：{{ item.msg }}</view>
            </view>
        </view>
    </view>
</template>

<script lang="ts" setup>
import { UDPServer } from '@/uni_modules/uts-udp';
import { ref } from 'vue';

let server: UDPServer | undefined;
const isStart = ref(false);
const messages = ref<UTSUdpData[]>([]);

function onEnableClick() {
    if (isStart.value) return;
    server = server || new UDPServer(7000);
    server.listener(
        (data) => {
            messages.value.push(data);
            // 回复客户端
            server.send(`已收到消息：${data.msg}`, data.host, data.port);
        },
        (error) => {
            console.error(error);
            isStart.value = false;
        },
    );
    isStart.value = true;
}

function onDisableClick() {
    server?.stop();
    isStart.value = false;
}
</script>

<style lang="scss" scoped>
.content {
    font-size: 15px;
}

.btns {
    padding: 10px;
    text-align: right;

    button + button {
        margin-left: 5px;
    }
}

.box {
    padding: 10px;

    .item {
        padding: 10px;

        .label + .label {
            margin-left: 10px;
        }
    }
}
</style>
