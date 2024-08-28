<script>
	import Login from './src/components/Login.vue'
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<temp>
	<Login />
</temp>

<style>
	page {
		background-image: linear-gradient(to bottom, #0073ff 0%, #90cbff 50%,  #ECEFF1 75%, #ECEFF1 100%);; 
	}
	/*每个页面公共css */
	/* app-nav-bar {
	  background-color: grey;
	} */
</style>
