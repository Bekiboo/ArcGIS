<script lang="ts">
	import MapView from '@arcgis/core/views/MapView';
	import '@arcgis/core/assets/esri/themes/light/main.css';
	import Graphic from '@arcgis/core/Graphic';
	import Point from '@arcgis/core/geometry/Point';
	import SimpleMarkerSymbol from '@arcgis/core/symbols/SimpleMarkerSymbol';

	let centerText: string;

	const createMap = (domNode: any) => {
		const view = new MapView({
			container: domNode,
			map: {
				basemap: 'streets-vector' // hybrid, satellite, topo, gray, dark-gray, streets, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector, topo-vector, streets-satellite, streets-satellite-vector, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector
			},
			zoom: 15,
			// center: [47.528, -18.912] // longitude, latitude
			center: [-98, 49.5]
		});

		view.watch('center', (center) => {
			centerText = `Longitude: ${center.longitude.toFixed(3)}, Latitude: ${center.latitude.toFixed(3)}`;
		});

		view.when(() => {
			console.log('Map loaded');
		});

		// generate 100 random points
		const points = Array.from({ length: 10000 }, () => {
			return new Point({
				longitude: Math.random() * 360 - 180,
				latitude: Math.random() * 180 - 90
			});
		});

		// Create a symbol for drawing the point
		const markerSymbol = new SimpleMarkerSymbol({
			color: [226, 119, 40],
			outline: {
				// autocasts as new SimpleLineSymbol()
				color: [255, 255, 255],
				width: 2
			}
		});

		// Create a graphic and add the geometry and symbol to it
		points.forEach((point) => {
			const pointGraphic = new Graphic({
				geometry: point,
				symbol: markerSymbol
			});

			view.graphics.addMany([pointGraphic]);
		});
		// const pointGraphic = new Graphic({
		// 	geometry: point,
		// 	symbol: markerSymbol
		// });

		// view.graphics.addMany([pointGraphic]);

		view.on('click', (event) => {
			// log coordinates of the click location
			console.log(event.mapPoint.latitude, event.mapPoint.longitude);
		});

		return {
			destroy() {
				view.destroy();
			}
		};
	};
</script>

<main>
	{#if centerText}
		<p>{centerText}</p>
	{/if}
	<div class="view" use:createMap></div>
</main>

<style>
	main {
		margin: 2rem;
	}
	.view {
		height: 800px;
		width: 100%;
	}
</style>
