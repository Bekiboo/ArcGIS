<script lang="ts">
	import MapView from '@arcgis/core/views/MapView';
	import '@arcgis/core/assets/esri/themes/light/main.css';

	let centerText: string;

	const createMap = (domNode: any) => {
		const view = new MapView({
			container: domNode,
			map: {
				basemap: 'streets-vector'
			},
			zoom: 15,
			center: [47.528, -18.912] // longitude, latitude
		});

		view.watch('center', (center) => {
			centerText = `Longitude: ${center.longitude.toFixed(3)}, Latitude: ${center.latitude.toFixed(3)}`;
		});
	};
</script>

<main>
	{#if centerText}
		<p>{centerText}</p>
	{/if}
	<div class="view" use:createMap></div>
</main>

<style>
	.view {
		height: 400px;
		width: 800px;
	}
</style>
