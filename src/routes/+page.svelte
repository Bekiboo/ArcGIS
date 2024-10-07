<script lang="ts">
	import MapView from '@arcgis/core/views/MapView'
	import '@arcgis/core/assets/esri/themes/light/main.css'
	import Graphic from '@arcgis/core/Graphic'
	import Point from '@arcgis/core/geometry/Point'
	import PopupTemplate from '@arcgis/core/PopupTemplate'
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
				basemap: 'streets' // hybrid, satellite, topo, gray, dark-gray, streets, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector, topo-vector, streets-satellite, streets-satellite-vector, streets-navigation-vector, streets-night-vector, streets-relief-vector, streets-terrain-vector
			},
			zoom: 13,
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

	const addMarker = (point: any, color: number[]) => {
		const markerSymbol = createMarkerSymbol(color)

		const popupTemplate = new PopupTemplate({
			title: point.name,
			content: [
				{
					type: 'fields',
					fieldInfos: [
						{
							fieldName: 'description',
							label: 'Description'
						},
						{
							fieldName: 'rating',
							label: 'Rating'
						}
					]
				}
			]
		})

		const pointGraphic = new Graphic({
			geometry: new Point({
				longitude: point.coordinates[0],
				latitude: point.coordinates[1]
			}),
			symbol: markerSymbol,
			attributes: point,
			popupTemplate
		})
		
		view.graphics.add(pointGraphic)
	}

	let currentFilter = 'all'

	const filterMap = (type: string) => {
		view.graphics.removeAll()

		currentFilter = type

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

<main class="h-screen relative overflow-hidden">
	<header class="backdrop-blur-md bg-slate-800 bg-opacity-20 absolute z-10 right-0 p-4 rounded-bl-lg shadow-2xl flex flex-col gap-4">
			<h1 class="font-black text-3xl text-slate-800">Antananarivo's Best Spots</h1>

			<!-- FILTERS -->
			<div class="flex justify-between">
				<input
					class="hidden"
					type="radio"
					id="all"
					name="filter"
					value="all"
					on:change={() => filterMap('all')}
					checked
				/>
				<label for="all" class="btn btn-primary {currentFilter === 'all' ? '' : 'btn-outline'}"
					>All</label
				>
				<input
					class="hidden"
					type="radio"
					id="restaurants"
					name="filter"
					value="restaurants"
					on:change={() => filterMap('restaurants')}
				/>
				<label
					for="restaurants"
					class="btn btn-primary {currentFilter === 'restaurants' ? '' : 'btn-outline'}"
					>Restaurants</label
				>
				<input
					class="hidden"
					type="radio"
					id="hotels"
					name="filter"
					value="hotels"
					on:change={() => filterMap('hotels')}
				/>
				<label for="hotels" class="btn btn-primary {currentFilter === 'hotels' ? '' : 'btn-outline'}"
					>Hotels</label
				>
				<input
					class="hidden"
					type="radio"
					id="shops"
					name="filter"
					value="shops"
					on:change={() => filterMap('shops')}
				/>
				<label for="shops" class="btn btn-primary {currentFilter === 'shops' ? '' : 'btn-outline'}"
					>Shops</label
				>
			</div>
	</header>

	<!-- MAP -->
	<div class="h-screen w-screen" use:Map></div>
</main>

