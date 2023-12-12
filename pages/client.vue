<template>
    <view class="content">
        <uni-easyinput v-model="msg" type="textarea" auto-height placeholder="请输入内容"></uni-easyinput>
        <button @click="onSendClick">发送消息</button>
        <view class="box">
            <view v-for="item in messages" class="item">服务器[{{ item.host }}]回复消息：{{ item.msg }}</view>
        </view>
    </view>
</template>

<script lang="ts" setup>
import { UDPClient } from '@/uni_modules/uts-udp';
import { ref } from 'vue';

const msg = ref('');
const messages = ref<UTSUdpData[]>([]);

function onSendClick() {
    UDPClient.send({
        host: '255.255.255.255',
        port: 7000,
        receiveTimeout: 2000,
        msg: msg.value,
        enableRecive: true,
        onceReceive(data) {
            messages.value.push(data);
        },
        onError(error) {
            console.error(error);
        },
        onceReceiveTimeout() {
            console.warn('服务器超时未回复');
        },
    });
}
</script>

<style lang="scss" scoped>
.content {
    font-size: 15px;
}

.box {
    padding: 10px;
}
</style>
