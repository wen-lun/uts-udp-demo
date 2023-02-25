<template>
	<view class="content">
		<uni-easyinput v-model="msg" type="textarea" auto-height placeholder="请输入内容"></uni-easyinput>
		<button @click="onSendClick">发送消息</button>
		<view class="box">
			<view v-for="item in messages" class="item">服务器回复消息：{{item.msg}}</view>
		</view>
	</view>
</template>

<script lang="ts" setup>
	import {
		UDPClient
	} from '../../uni_modules/uts-udp'
	import {
		ref
	} from "vue";

	const msg = ref('')
	const messages = ref < Array < {
		address: string,
		port: number,
		msg: string
	} >> ([])


	function onSendClick() {
		const client = new UDPClient('255.255.255.255', 7000, 1024 * 64)
		client.send(msg.value, rinfo => {
			messages.value.push(rinfo)
		}, error => {
			console.error(error)
		})
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
