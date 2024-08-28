<script>
	export default{
		data(){
			return {
				contentText:{
					contentDefault: '已维修',
					contentFav: '未维修'
				},
				num:0,
				stop : false,
				forms : [
					{
						Words: '井盖偏移',
						Class:'uncovered',
						Longitude:'108.832683',
						Latitude:'34.127581',
						num:0,
						down:true,
					},
					{
						Words: '破损严重',
						Class:'broke',
						Longitude:'108.833217',
						Latitude:'34.127631',
						num:1,
						down:true,
					},
					{
						Words: '轻微破损',
						Class:'circle',
						Longitude:'108.832094',
						Latitude:'34.127308',
						num:2,
						down:true,
					},
					{
						Words: '偏移',
						Class:'uncovered',
						Longitude:'108.831798',
						Latitude:'34.126851',
						num:3,
						down:true,
					},
				]
			}
		},
		onLoad() {
			this.refresh();
		},
		methods: {
			unirequest(form) {
				uni.request({
				        url: 'http://192.168.0.109:5000/unigetdata', // 后端接口地址
				        method: 'GET',
				        success: (res) => {
							if (res.data.msg == 'Over'){
								uni.showToast({
									title:"没有更多信息",
								})
								this.stop = true;
							}
							else if(res.data.msg == 'True')
							{
								console.log('Data from Flask:')
								form.Words = res.data.Words // 更新 dataList
								form.Class = res.data.Class
								form.Longitude = res.data.Longitude
								form.Latitude = res.data.Latitude
								form.down = true
							} 
				        },
				});
				return form;
			},
			openMap() {
				let name = 'address';
				console.log("获取经纬度ssssfff", this.forms[this.num].Longitude, this.forms[this.num].Latitude);
				//打开地图，并将门店位置传入
				uni.openLocation({
					latitude: parseFloat(this.forms[this.num].Latitude),
					longitude: parseFloat(this.forms[this.num].Longitude),
					name,
					scale: 18,
					success: function () {
						console.log('success');
					}
				})
			},
			refresh() {
					for(let i = 0; i < 4; i++)
					{
						if(this.forms[i].down == false){
							this.unirequest(this.forms[i]);
						}
						if(this.stop == true){
							stop = false;
							break;
						}
					}
			},
			put() {
				uni.navigateTo({
				url:'./Check'
				});
				this.a = true;
			},
			openfuc(form){
				console.log(form.num)
				console.log(form.down)
				this.num = form.num
				console.log(this.num)
				this.$refs.func.open('bottom')
			},
			workdown(){
				if(this.forms[this.num].down = true){
					this.forms[this.num].down = false;
				} else if (this.forms[this.num].down = flase){
					this.forms[this.num].down = true;
				}
				
			},
		}
	}
</script>

<template>
	<view>
		<view style="display: flex; flex-direction: column;">
			<uni-group mode="card" v-for="(form, index) in forms">
					<view class="container" @click="openfuc(form)">
						<view style="width:50%">
							<view>经度:{{form.Longitude}}</view>
							<view>纬度:{{form.Latitude}}</view>
							<view>类别:{{form.Class}}</view>
							<view>补充:{{form.Words}}</view>
						</view>
						<view style="margin-left: 50px; margin-top: 10px; margin-left: 100px;">
							<uni-fav :checked="form.down" :content-text="contentText"/>
						</view>
					</view>
			</uni-group>
			<button class="button" type="primary" @click="refresh">刷新</button>
		</view>
		<view >
			<uni-popup ref="func" >
				<view  class="info">
					<button class="button" type="primary" style="background-color: #b4e1e1;  "  @click="openMap">导 航</button>
					<button class="button"  type="primary" style="background-color: #bdbdeb; "  @click="put" >重新上传</button>
					<button class="button"  type="primary" style="background-color: #bce3bf;  " @click="workdown" >完成维修</button>
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
	.container{
		/* 定义flex容器 */
		display: flex;
		/*设置容器内部容器的排列方向*/	
		flex-direction: row; 
	}
	.info{
		width: 100vw;
		padding: 5% 0;
		background-color: #fff;
		border-top-left-radius: 5vw;
		border-top-right-radius: 5vw;
	}
</style>