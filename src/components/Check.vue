<script>
	import axios from 'axios'
	import {
			getImageData,
			getFloatLocationByExif
		} from '@/js_sdk/iz-exif/js_sdk/izExif/izExif.js'
	export default{
		name: "check",
		props:['username'],
		data () {
			return {
				flag:true,
				Image:'../../static/3.jpg',
				form:{
					imagepath:'',
					Words:'',
					Longitude:'',
					Latitude:'',
				},
			}
		},
		methods: {
			takePhoto() {
				let _this = this;
				//选择图片
				uni.chooseImage({
					sizeType:['original'],
					count: 1,
					sourceType: ['album','camera'],
					success: res => {
						this.form.imagepath = res.tempFilePaths[0];
						console.log(res.tempFilePaths[0])
						//保存图片，重新渲染
						uni.saveFile({
						    tempFilePath: res.tempFilePaths[0],
						    success: (suc) =>{
								this.Image = suc.savedFilePath;
								console.log(this.Image);
								getImageData(suc.savedFilePath).then(res=>{
								    console.log(res)
								    console.log(getFloatLocationByExif(res.exif).lat)
									this.form.Latitude = getFloatLocationByExif(res.exif).lat;
									this.form.Longitude = getFloatLocationByExif(res.exif).lon;
								}).catch(e=>{
								    console.log(e)
								})
						        uni.showToast({
						            title: "成功",
						            icon: "none"
						        });
						    },
						    fail: (error)  =>{
						        uni.showToast({
						            title: "失败",
						            icon: "error"
						        });
						    }
						});
					},
				});
			},
			getloc() {
				var that = this;
				uni.getLocation({
					type: 'wgs84',
					success: function (res) {
						
						console.log('当前位置的经度：' + res.longitude);
						that.form.Longitude = res.longitude;
						console.log('当前位置的纬度：' + res.latitude);
						that.form.Latitude = res.latitude;
						that.$refs.loc.close();
					}
				});
			},
			send() {
					uni.uploadFile({
					    url: 'http://192.168.0.109:5000/uniappimage', // 上传图片的接口地址
					    filePath: this.Image,// 图片文件的临时路径
					    name: 'Image', // 服务器接收图片的字段名
					    success: (res) => {
							console.log('Response from Flask:', res.data)
							res.data = JSON.parse(res.data)
							if (res.data.msg == 'True') {
								uni.showToast({
									title:"上传成功",
								})
							} else if (res.data.msg == 'False'){
								uni.showToast({
									icon : 'error',
									title:"未检测到",
								})
							}
					    },
					    fail: (err) => {
							uni.showToast({
								icon:'error',
								title:"上传失败，请使用手机拍摄并确保记录地理位置"
							})
					        console.error('Error uploading image:', err);
					    }
					});
			},
			pushdata(){
				uni.showToast({
					title:"上传成功"
				})
				// 发送 POST 请求
				uni.request({
					url:'http://192.168.0.109:5000/uniappinfo',
					data:this.form,
					method:'POST',
					success: (res) =>{
						console.log('Response from Flask:', res.data);
					},
					fail: (err) => {
						console.error('Error uploading data:', err);
					}
				})
				this.send();
			},
			
			openloc(){
				this.$refs.loc.open()
			},
		}
	}
</script>

<template>
	<view>
		<view >
			<view class="example">
				<view style=" height: 100vw ; width: 100vw; display: flex; justify-content: center; align-items: center;">
					<image  :src="Image" style="border-radius: 5%; height: 100%; width: 60%; border: 5rpx solid #c7c9ca;" mode="widthFix"  @click="takePhoto"></image>
				</view>
				<uni-group mode="card" style="height:200px">
					<uni-form ref="baseForm" >
						<div @click="openloc">
							<uni-forms-item label="经度:">
								<uni-easyinput v-model="form.Longitude" placeholder="请获取经度" />
							</uni-forms-item>
							<uni-forms-item label="纬度:" >
								<uni-easyinput v-model="form.Latitude" placeholder="请获取纬度" />
							</uni-forms-item>
						</div>
						<uni-forms-item label="补充:">
							<uni-easyinput v-model="form.Words" placeholder="补充信息" />
						</uni-forms-item>
					</uni-form>
				</uni-group>
				<button class="button" type="primary" @click="pushdata" style="width:70%;">上传</button>
			</view>
		</view>
		<view>
			<uni-popup ref="loc" type="bottom" safeArea backgroundColor="#fff">
				<view>
					<button  @click="getloc">获取位置信息</button>
				</view>
			</uni-popup>
		</view>
	</view>
</template>

<style>
	.button{
		margin-top: 15px;
		width: 70%;
		border-radius: 18px;
	}
</style>