<script lang="ts">
	import MapView from '@arcgis/core/views/MapView';
	import '@arcgis/core/assets/esri/themes/light/main.css';
	import Graphic from '@arcgis/core/Graphic';
	import Polyline from '@arcgis/core/geometry/Polyline';
	import Polygon from '@arcgis/core/geometry/Polygon';
	import Point from '@arcgis/core/geometry/Point';
	import SimpleLineSymbol from '@arcgis/core/symbols/SimpleLineSymbol';
	import SimpleFillSymbol from '@arcgis/core/symbols/SimpleFillSymbol';
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

		const polyline = new Polyline({
			paths: [
				[
					[-111.3, 52.68],
					[-98, 49.5],
					[-93.94, 29.89]
				]
			]
		});

		const lineSymbol = new SimpleLineSymbol({
			color: [226, 119, 40], // RGB color values as an array
			width: 4
		});

		const lineAtt = {
			Name: 'Keystone Pipeline', // The name of the pipeline
			Owner: 'TransCanada', // The owner of the pipeline
			Length: '3,456 km' // The length of the pipeline
		};

		const polylineGraphic = new Graphic({
			geometry: polyline,
			symbol: lineSymbol,
			attributes: lineAtt,
			popupTemplate: {
				title: '{Name}',
				content: [
					{
						type: 'fields',
						fieldInfos: [
							{
								fieldName: 'Name'
							},
							{
								fieldName: 'Owner'
							},
							{
								fieldName: 'Length'
							}
						]
					}
				]
			}
		});

		// Create a polygon geometry
		const polygon = new Polygon({
			rings: [
				[
					[-64.78, 32.3],
					[-66.07, 18.45],
					[-80.21, 25.78],
					[-64.78, 32.3]
				]
			]
		});

		// Create a symbol for rendering the graphic
		const fillSymbol = new SimpleFillSymbol({
			color: [227, 139, 79, 0.8],
			outline: {
				// autocasts as new SimpleLineSymbol()
				color: [255, 255, 255],
				width: 1
			}
		});

		// Add the geometry and symbol to a new graphic
		const polygonGraphic = new Graphic({
			geometry: polygon,
			symbol: fillSymbol
		});

		// First create a point geometry (this is the location of the Titanic)
		const point = new Point({
			longitude: -49.97,
			latitude: 41.73
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
		const pointGraphic = new Graphic({
			geometry: point,
			symbol: markerSymbol
		});

		view.graphics.addMany([polylineGraphic, polygonGraphic, pointGraphic]);

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
