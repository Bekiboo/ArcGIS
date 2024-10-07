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

	const Map = (domNode: any) => {
		view = new MapView({
			container: domNode,
			map: {
				basemap: 'streets-vector' // hybrid, satellite, topo, gray, dark-gray, streets, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector, topo-vector, streets-satellite, streets-satellite-vector, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector
			},
			zoom: 12,
			center: [47.528, -18.912] // longitude, latitude
		})

		view.when(() => {
			console.log('Map loaded')
		})

		filterMap('all')

		return {
			destroy() {
				view.destroy()
			}
		}
	}

	const createMarkerSymbol = (color: number[] | string) => {
		return new SimpleMarkerSymbol({
			color: color,
			outline: {
				color: '#fff',
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
	<h1 class="bg-red-500">Antananarivo's Best Spots</h1>
	<div>Filters</div>
	<div>
		<input
			type="radio"
			id="all"
			name="filter"
			value="all"
			on:change={() => filterMap('all')}
			checked
		/>
		<label for="all">All</label>
		<input
			type="radio"
			id="restaurants"
			name="filter"
			value="restaurants"
			on:change={() => filterMap('restaurants')}
		/>
		<label for="restaurants">Restaurants</label>
		<input
			type="radio"
			id="hotels"
			name="filter"
			value="hotels"
			on:change={() => filterMap('hotels')}
		/>
		<label for="hotels">Hotels</label>
		<input
			type="radio"
			id="shops"
			name="filter"
			value="shops"
			on:change={() => filterMap('shops')}
		/>
		<label for="shops">Shops</label>
	</div>

	<div class="view" use:Map></div>
</main>

<style>
	.view {
		height: 800px;
		width: 100%;
	}
</style>
