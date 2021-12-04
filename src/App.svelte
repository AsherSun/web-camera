<svelte:head>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.0/dist/css/bootstrap.min.css">
</svelte:head>
<main>
	<Button color="primary" on:click={drawImage}>拍照</Button>
	{#if imageData}
	<img src={imageData} alt="" id="image">
	{/if}
	<Modal body {isOpen} {toggle}>
		<ModalHeader>访问用户媒体设备失败</ModalHeader>
		<ModalBody>
			<p>请确保摄像头可访问</p>
			<p>Edge浏览器中的设置<a target="_blank" href="edge://settings/content/camera">edge://settings/content/camera</a></p>
			<p>Chrome浏览器中的设置: <a target="_blank" href="chrome://settings/content/camera">chrome://settings/content/camera</a></p>
		</ModalBody>
	</Modal>
</main>



<script lang="ts">
import { onMount } from "svelte";
import {Button, Modal, ModalHeader, ModalBody} from 'sveltestrap';

let video: HTMLVideoElement;
let imageData: string;
let isOpen: boolean = false;
const toggle = () => (isOpen = !isOpen);
const width = 500;
const height = 500;

onMount(() => {
	getUserMedia();
})

function getUserMedia() {
	navigator.mediaDevices.getUserMedia({video: {width: width, height: height}}).then((stream) => {
		video = document.createElement('video');
		video.width = width;
		video.height = height;
		video.src = stream as never as string;
		video.srcObject = stream;
		video.play();
	}).catch((error: Error) => {
		console.log('error', error.message, error.stack, error.name)
		toggle();
	})
}

function drawImage() {
	if (!video) {
		toggle();
		return;
	}
	const canvas = document.createElement('canvas');
	canvas.width = width;
	canvas.height = width;
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
  #image {
		width: 500px;
		height: 500px;
	}
</style>