

<main>
	<button on:click={drawImage}>拍照</button>
	<!-- svelte-ignore a11y-media-has-caption -->
	<video  id="video" />
	<canvas id="canvas" width="500" height="500" />
	{#if imageData}
	<img src={imageData} alt="" id="image">
	{/if}
	
	<!-- <button on:click={getUserMedia}>打开媒体设备</button> -->
	<!-- <button on:click={drawImage}>拍照</button> -->
</main>


<script lang="ts">
import { onMount } from "svelte";


let video: HTMLVideoElement;
let canvas: HTMLCanvasElement;
let imageData: string;
const width = 500;
const height = 500;

onMount(() => {
	getUserMedia();
})

function getUserMedia() {
	navigator.mediaDevices.getUserMedia({video: {width: width, height: height}}).then((stream) => {
		video = document.querySelector('#video') as HTMLVideoElement;
		video.src = stream as never as string;
		video.srcObject = stream;
		video.play();
	}).catch((error) => {
		console.log('error', JSON.stringify(error))
		console.log('访问用户媒体设备失败，请确保使用的是Edge或者Chrome浏览器！');
		console.log('请确保摄像头可访问')
		console.log('Edge浏览器中的设置: edge://settings/content/camera');
		console.log('Chrome浏览器中的设置: chrome://settings/content/camera');
	})
}

function drawImage() {
	if (!video) {
		alert('请先打开摄像头')
		return;
	}
	canvas = document.querySelector('#canvas') as HTMLCanvasElement;
	const context = canvas.getContext('2d');
	context.drawImage(video, 0, 0, width, height, 0, 0, width, height);
	imageData = canvas.toDataURL('image/png');
}
</script>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
	#video {
		width: 500px;
		height: 500px;
	}
  #image {
		width: 500px;
		height: 500px;
	}
</style>