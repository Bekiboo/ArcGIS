<script lang="ts">
	import MapView from '@arcgis/core/views/MapView'
	import '@arcgis/core/assets/esri/themes/light/main.css'
	import Graphic from '@arcgis/core/Graphic'
	import Point from '@arcgis/core/geometry/Point'
	import SimpleMarkerSymbol from '@arcgis/core/symbols/SimpleMarkerSymbol'
	import { restaurants } from './restaurants'
	import { hotels } from './hotels'
	import { shops } from './shops'

	let centerText: string

	let view: MapView

	const Map = (domNode: any, filters: { type?: string } = {}) => {
		view = new MapView({
			container: domNode,
			map: {
				basemap: 'streets-vector' // hybrid, satellite, topo, gray, dark-gray, streets, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector, topo-vector, streets-satellite, streets-satellite-vector, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector
			},
			zoom: 12,
			center: [47.528, -18.912] // longitude, latitude
		})

		view.watch('center', (center) => {
			centerText = `Longitude: ${center.longitude.toFixed(3)}, Latitude: ${center.latitude.toFixed(3)}`
		})

		view.when(() => {
			console.log('Map loaded')
		})

		const markerSymbol = new SimpleMarkerSymbol({
			color: [226, 119, 40],
			outline: {
				// autocasts as new SimpleLineSymbol()
				color: [255, 255, 255],
				width: 2
			}
		})

		filterMap('all')

		return {
			destroy() {
				view.destroy()
			}
		}
	}

	const createMarkerSymbol = (color: number[]) => {
		return new SimpleMarkerSymbol({
			color: color,
			outline: {
				// autocasts as new SimpleLineSymbol()
				color: [255, 255, 255],
				width: 2
			}
		})
	}

	const addMarker = (point: { coordinates: number[] }, color: number[]) => {
		const markerSymbol = createMarkerSymbol(color)

		const pointGraphic = new Graphic({
			geometry: new Point({
				longitude: point.coordinates[0],
				latitude: point.coordinates[1]
			}),
			symbol: markerSymbol
		})
		view.graphics.add(pointGraphic)
	}

	const filterMap = (type: string) => {
		view.graphics.removeAll()

		switch (type) {
			case 'all':
				// Show all points if no filter is applied
				restaurants.forEach((point) => {
					addMarker(point, [226, 119, 40])
				})
				hotels.forEach((point) => {
					addMarker(point, [40, 119, 226])
				})
				shops.forEach((point) => {
					addMarker(point, [40, 226, 119])
				})
				break
			case 'restaurants':
				restaurants.forEach((point) => {
					addMarker(point, [226, 119, 40])
				})
				break

			case 'hotels':
				hotels.forEach((point) => {
					addMarker(point, [40, 119, 226])
				})
				break

			case 'shops':
				shops.forEach((point) => {
					addMarker(point, [40, 226, 119])
				})
				break
		}
	}
</script>

<main>
	<div>Filters</div>
	<div>
		<button on:click={() => filterMap('all')}>All</button>
		<button on:click={() => filterMap('restaurants')}>Restaurants</button>
		<button on:click={() => filterMap('hotels')}>Hotels</button>
		<button on:click={() => filterMap('shops')}>Shops</button>
	</div>

	{#if centerText}
		<p>{centerText}</p>
	{/if}
	<div class="view" use:Map></div>
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
