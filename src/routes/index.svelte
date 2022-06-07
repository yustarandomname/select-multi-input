<script lang="ts">
	import { onMount } from 'svelte';

	let videoSource: HTMLVideoElement; // Video output source
	let videoSelected: MediaDeviceInfo; // video output selected
	let videoOptions: MediaDeviceInfo[] = []; // video ids as list

	async function getStream() {
		const deviceId = videoSelected ? {deviceId: { exact: videoSelected }} : { facingMode: 'environment' };

		const constraints = {
			video: deviceId
		};

		console.log(JSON.stringify(constraints));

		try {
			const stream = await navigator.mediaDevices.getUserMedia(constraints);
			videoSource.srcObject = stream;
			videoSource.play();
		} catch (error) {
			console.error('Error:', error);
		}
	}

	onMount(async () => {
		const stream = await getStream();
		const devices = await navigator.mediaDevices.enumerateDevices();
		videoOptions = devices.filter((device) => device.kind == 'videoinput');
		console.log(videoOptions.length);
	});
</script>

<!-- svelte-ignore a11y-media-has-caption -->
<video 
loop
playsInline
autoPlay={false}
controls={false}
preload="auto"
type="video/mp4" 
bind:this={videoSource} />

<select bind:value={videoSelected} on:change={getStream}>
	{#each videoOptions as option, index}
		<option value={option}>
			{option.label || 'Camera ' + (index + 1)}
		</option>
	{/each}
</select>
