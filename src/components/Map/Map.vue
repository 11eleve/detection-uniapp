<template>
	<view class="map">
		<map
			id="my-map"
			ref="my-map"
			class="my-map"
			:style="{ height: allScreen - 44 + 'px' }"
			:latitude="myLatitude"
			:longitude="myLongitude"
			:markers="makers"
			enable-3D="true"
			show-compass="true"
			show-location="true"
			:polyline="myPolyline"
		></map>
		<!-- 样式里减44px是因为默认的导航栏高度为44px -->
		<cover-view class="control-btn">
			<cover-view class="location" @tap="locationClick">
				<cover-image class="control-btn-location-img" src="../../../static/2.png"></cover-image>
			</cover-view>
			<cover-view class="assemble" @tap="getRoute">
				<cover-image class="control-btn-assemble-img" src="../../../static/logo.png"></cover-image>
			</cover-view>
			<cover-view class="weather">
				<cover-image :src="weatherImg" class="weather-img"></cover-image>
				<cover-view class="wearth-txt">{{ temperature + '℃' }}</cover-view>
			</cover-view>
		</cover-view>
	</view>
</template>

<script>
var amapFile = require('@/common/amap-wx.js');
var myAmapFun = new amapFile.AMapWX({ key: 'fd4b2255d2c65525aa3ed9046409017e' });
export default {
	data() {
		return {
			// id: 0,
			allScreen: '',
			myLatitude: '', //纬度
			myLongitude: '', //经度
			makers: [
				{
					id: 1,
					latitude: '纬度，不给看',
					longitude: '经度，也不给看',
					title: '测试点',
					iconPath: '/static/imgs/edit-maker.png',
					width: '50px',
					height: '50px'
				}
			],
			weather: '',//天气
			temperature: '',//温度
			myPolyline:[]//路线
		};
	},
	onLoad() {
		let that = this;
		var myAmapFun = new amapFile.AMapWX({ key: that.$store.state.amapKey });
		that.getMyLocation();
		uni.getSystemInfo({
			success: function(e) {
				that.allScreen = e.screenHeight;
			},
			fail: function(e) {
				console.log(e);
			}
		});
		myAmapFun.getWeather({
			success: function(data) {
				that.weather = data.weather.data;
				that.temperature = data.temperature.data;
				console.log(data.weather.data);
			},
			fail: function(info) {
				//失败回调
				console.log(info);
			}
		});
	},
	onReady: function(res) {
		this.mapContext = uni.createMapContext('my-map', this);
	},
	methods: {
		getRoute() {
			let that = this;
			var myAmapFun = new amapFile.AMapWX({ key: that.$store.state.amapKey });
			that.mapContext.includePoints({//缩放视野展示所有经纬度
				points:[{
					latitude:that.myLatitude,
					longitude:that.myLongitude
				},{
					latitude:that.makers[0].latitude,
					longitude:that.makers[0].longitude
				}]
			})
			var myLocal = that.myLongitude+','+that.myLatitude
			var target = that.makers[0].longitude+','+that.makers[0].latitude
			myAmapFun.getWalkingRoute({//获取步行线路
				origin: myLocal,
				destination: target,
				success: function(data) {
					console.log(data);
					//获取线路规划的数据，后面还需要在地图上画出来，未完成，完成后会发新博客
				},
				fail: function(info) {
					console.log(info);
				}
			});
		},
		getMyLocation() {
			let that = this;
			uni.getLocation({
				type: 'gcj02',
				success: function(res) {
					that.myLatitude = res.latitude;
					that.myLongitude = res.longitude;
					console.log('myLatitude为' + that.myLatitude);
					console.log('myLongitude为' + that.myLongitude);
				},
				fail: function(e) {
					console.log(e);
				}
			});
		},
		locationClick() {
			console.log('location');
			let that = this;
			that.getMyLocation();
			that.mapContext.moveToLocation({
				longitude: that.myLongitude,
				latitude: that.myLatitude,
				success: function(res) {
					console.log('刷新成功');
				},
				fail: function(e) {
					console.log('调用失败');
					console.log(e);
				},
				complete: function() {
					console.log('调用结束');
				}
			});
		},
		assembleClick() {
			uni.chooseLocation({
				success: function(res) {
					console.log('位置名称：' + res.name);
					console.log('详细地址：' + res.address);
					console.log('纬度：' + res.latitude);
					console.log('经度：' + res.longitude);
				}
			});
		}
	},
	computed: {
		weatherImg: function() {
			//返回天气图片，未完善
			//全部天气类型在https://lbs.amap.com/api/webservice/guide/tools/weather-code/
			switch (this.weather) {
				case '晴':
					return 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-tourism/fd3680c0-797a-11ea-b997-9918a5dda011.png';
					break;
				case '多云':
					return 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-tourism/fd29d690-797a-11ea-b94e-47f67ecf8268.png';
					break;
				default:
					return 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-tourism/fd34fa20-797a-11ea-b94e-47f67ecf8268.png';
			}
		}
	}
};
</script>

<style scoped>
/* nvue好像不能用scss */
.my-map {
	width: 750rpx;
}
.control-btn {
	position: fixed;
	flex-direction: column;
	top: 400rpx;
	left: 20rpx;
	align-items: center;
	/* background-color: #f00; */
}
.location {
	margin-bottom: 30rpx;
	flex-direction: column;
	align-items: center;
}
.assemble{
	flex-direction: column;
	align-items: center;
}
.control-btn-location-img {
	width: 60rpx;
	height: 60rpx;
}
.control-btn-assemble-img {
	width: 80rpx;
	height: 80rpx;
}
.control-btn-txt {
	font-size: 19rpx;
	text-align: center;
}
.weather {
	position: fixed;
	bottom: 10rpx;
	right: 50rpx;
	background-color: #fff;
	flex-direction: row;
	align-items: center;
}
.weather-img {
	width: 50rpx;
	height: 50rpx;
}
.wearth-txt {
	color: #999999;
	font-size: 32rpx;
}
</style>
