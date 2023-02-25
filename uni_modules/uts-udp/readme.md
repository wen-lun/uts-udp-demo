# 例子
* 服务端
```html
<template>
	<view class="content">
		<view class="btns">
			<button type="primary" size="mini" @click="onEnableClick">启动UDP服务</button>
			<button type="warn" size="mini" @click="onDisableClick">停止UDP服务</button>
		</view>
		<view class="status">{{server?'UDP服务已启动' : 'UDP服务未启动'}}</view>
		<view class="box">
			<view v-for="item in messages" class="item">
				<view class="info">
					<text class="label">主机：{{item.address}}</text>
					<text class="label">端口：{{item.port}}</text>
				</view>
				<view class="msg">消息：{{item.msg}}</view>
			</view>
		</view>
	</view>
</template>

<script lang="ts" setup>
	import {
		UDPServer
	} from '../../uni_modules/uts-udp'
	import {
		ref
	} from "vue";

	const server = ref < UDPServer > ()
	const messages = ref < Array < {
		address: string,
		port: number,
		msg: string
	} >> ([])

	function onEnableClick() {
		if (server.value) return;
		server.value = new UDPServer(7000, 1024 * 64);
		server.value.listener((rinfo) => {
			messages.value.push(rinfo)
			// 回复客户端
			server.value.send(`已收到消息：${rinfo.msg}`, rinfo.address, rinfo.port)
		}, error => {
			console.log(error)
		})
	}

	function onDisableClick() {
		server.value.close();
		server.value = undefined
	}
</script>

<style lang="scss" scoped>
	.content {
		font-size: 15px;
	}

	.btns {
		padding: 10px;
		text-align: right;

		button+button {
			margin-left: 5px;
		}
	}

	.box {
		padding: 10px;

		.item {
			padding: 10px;

			.label+.label {
				margin-left: 10px;
			}
		}
	}
</style>
```

* 客户端
```html
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
```

# uts-udp
### 开发文档
[UTS 语法](https://uniapp.dcloud.net.cn/tutorial/syntax-uts.html)
[UTS 原生插件](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html)
[Hello UTS](https://gitcode.net/dcloud/hello-uts/-/tree/dev)