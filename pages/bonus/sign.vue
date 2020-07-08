<template>
	<view>
		<hchPoster ref="hchPoster" :canvasFlag.sync="canvasFlag" @cancel="canvasCancel" :posterObj.sync="posterData"/>
		<view :hidden="canvasFlag"><!-- 海报 要放外面放组件里面 会找不到 canvas-->
			<canvas class="canvas"  canvas-id="myCanvas" ></canvas><!-- 海报 -->
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				canvasFlag: true,
			}
		},
		methods: {
		},
		components:{
		}
	}
</script>

<style>

</style>
