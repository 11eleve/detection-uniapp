<script>
import axios from 'axios'
export default {
    data() {
        return {
			form : {
				Count: '',
				Password: '',
				user:true,
			},
			flag:'True',
			screenHeight:0,
        }
    },
	onLoad() {

		this.screenHeight = uni.getSystemInfoSync().windowHeight;
		console.log('高度2',this.screenHeight);
		
	},
    methods: {
        userlogin () {
   //          uni.request({
			// 	url:'http://192.168.0.105:5000/uniapplogin',
			// 	method:'POST',
			// 	data:this.form,
			// 	sslVerify:false,
			// 	success:(response) => {
			// 		const data = response.data
			// 		console.log(data)
			// 		if(this.form.user === false && data.msg == 'Check'){
			// 			uni.redirectTo({
			// 			url:'./Check'
			// 			});
			// 		} else if (this.form.user === true && data.msg == 'Repair'){
			// 			uni.redirectTo({
			// 			url:'./Repair'
			// 			});
			// 		} else {
			// 			alert("账号或密码错误")
			// 		}
			// 	},
			// 	fail:(error) => {
			// 	    console.error('Error submitting data:', error);
			// 	},
			// })
            if(this.form.user === false){
            uni.redirectTo({
             	url:'./Check'
             	});
			} else if (this.form.user === true){
             	uni.redirectTo({
             	url:'./Repair'
            });
			}
        },
		switch1Change: function (e) {
			this.form.user = e.detail.value
			console.log(this.user)
		},
    }
}
</script>

<template>
	<view :style="{height: screenHeight+'px'}">
		<view style="padding-top: 30%;">
			<uni-group mode="card" >
				<uni-forms ref="baseForm">
					<uni-icons type="person" size="100" class="person"></uni-icons>
				  	<uni-forms-item label="账号" required>
				  		<uni-easyinput v-model="form.Count" placeholder="请输入账号" />
				  	</uni-forms-item>
					<uni-forms-item label="密码" required>
						<uni-easyinput v-model="form.Password" placeholder="请输入密码" />
					</uni-forms-item>
					<view style="margin-left: 240px;">
						<text>是否维修</text>
						<switch checked @change="switch1Change" style=""/>
					</view>
					<button type="primary" @click="userlogin" style="width:70%; margin-top: 10px;">登陆</button>
				</uni-forms>
			</uni-group>
		</view>

	</view>
</template>

<style>
	.logo{
		width: 300rpx;
		height: 300rpx;
		margin-left: auto; 
		margin-right:auto; 
	}
	.person{
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	
</style>