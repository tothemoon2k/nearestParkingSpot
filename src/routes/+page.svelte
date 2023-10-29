<script>
    import axios from "axios";
    import { Map } from "mapbox-gl";
    import { onMount, onDestroy } from "svelte";
    import carIcon from "$lib/assets/car.png";

    let map;
    let mapContainer;
    let lng, lat, zoom;
    let steps = [];

    lng = -71.224518;
    lat = 42.213995;
    zoom = 16;

    onMount(() => {
        const initialState = { lng: lng, lat: lat, zoom: zoom };

        map = new Map({
            container: mapContainer,
            accessToken: "pk.eyJ1Ijoid29udG9uLWRvbiIsImEiOiJjbG9hODJra2gwYmxhMmxxb283eWVpZXY4In0.L7SGeg75p94O8OLTH8OS0g",
            style: "mapbox://styles/mapbox/navigation-night-v1",
            center: [initialState.lng, initialState.lat],
            zoom: initialState.zoom,
        });

        const start = [-80.83783554386717, 35.2270691431859];

    async function getRoute(end) {
        // make a directions request using cycling profile
        // an arbitrary start will always be the same
        // only the end or destination will change
        console.log(start)
        const query = await fetch(
            `https://api.mapbox.com/directions/v5/mapbox/driving/${start[0]},${start[1]};-80.85083554386717,35.2270691431859?steps=true&geometries=geojson&access_token=pk.eyJ1Ijoid29udG9uLWRvbiIsImEiOiJjbG9hODJra2gwYmxhMmxxb283eWVpZXY4In0.L7SGeg75p94O8OLTH8OS0g`,
            { method: 'GET' }
        );
        const json = await query.json();
        const data = json.routes[0];
        steps = data.legs[0].steps;
        const route = data.geometry.coordinates;
        const geojson = {
            type: 'Feature',
            properties: {},
            geometry: {
            type: 'LineString',
            coordinates: route
            }
        };
        // if the route already exists on the map, we'll reset it using setData
        if (map.getSource('route')) {
            map.getSource('route').setData(geojson);
        }
        // otherwise, we'll make a new request
        else {
            map.addLayer({
            id: 'route',
            type: 'line',
            source: {
                type: 'geojson',
                data: geojson
            },
            layout: {
                'line-join': 'round',
                'line-cap': 'round'
            },
            paint: {
                'line-color': '#ffffff',
                'line-width': 8,
                'line-opacity': 0.8
            }
    });
  }
  // add turn instructions here at the end
}

        map.on('load', () => {
            // Load an image from an external URL.
            map.loadImage( carIcon,
                (error, image) => {
                    if (error) throw error;
                    
                    // Add the image to the map style.
                    map.addImage('car', image);
                    
                    // Add a data source containing one point feature.
                    map.addSource('point', {
                    'type': 'geojson',
                    'data': {
                    'type': 'FeatureCollection',
                    'features': [
                    {
                    'type': 'Feature',
                    'geometry': {
                    'type': 'Point',
                    'coordinates': [-71.224518, 42.213995]
                    }
                    }
                    ]
                    }
                    });
            
            // Add a layer to use the image to represent the data.
            map.addLayer({
            'id': 'points',
            'type': 'symbol',
            'source': 'point', // reference the data source
            'layout': {
            'icon-image': 'car', // reference the image
            'icon-size': 0.12
            }
            });
            }
            );

            getRoute(start);

            // Add starting point to the map
            map.addLayer({
                id: 'start',
                type: 'circle',
                'source': 'point', // reference the data source
                'layout': {
                'icon-image': 'car', // reference the image
                'icon-size': 0.12
                },
                paint: {
                'circle-radius': 10,
                'circle-color': '#3887be'
                }
            });
            });

        navigator.geolocation.getCurrentPosition((position) => {
            let userLng = position.coords.longitude;
            let userLat = position.coords.latitude;
            map.setCenter([userLng, userLat])
            map.getSource('point').setData({
                'type': 'FeatureCollection',
                'features': [
                    {
                        'type': 'Feature',
                        'geometry': {
                            'type': 'Point',
                            'coordinates': [userLng, userLat]
                        }
                    }
                ]
            });
        });


    });
    
</script>


<div class="map-wrap">
    <!--<a href="/reserve" class="m-6 inline-flex items-center justify-center px-8 py-4 md:py-6 text-base font-medium text-center text-green-100 border border-green-500 rounded-lg shadow-sm cursor-pointer hover:text-white bg-gradient-to-br from-green-500 via-green-500 to-green-600">
        <img class="h-14 mr-3.5" src="https://img.icons8.com/ios-filled/200/ffffff/parking.png" alt="">
        <span class="text-2xl relative flex justify-center items-center">Find the Nearest <br> Parking Spot</span>
      </a>-->
    <div class="w-1/3 h-fit absolute bg-gray-800 z-10 m-6 shadow-xl rounded-2xl ">
        <h2 class="text-white text-2xl text-center mt-10 mb-8 font-semibold leading-6">The nearest parking space is 3 mins away</h2>
        <p class="text-white text-xl font-medium ml-4 flex items-center mb-4"><img class="h-6 w-auto mr-1" src="https://img.icons8.com/ios-filled/200/ffffff/marker.png" alt=""> 300 NW Brevard Blvd</p>
        
        <p class="text-white text-xl font-medium ml-4 flex items-center mb-10"><img class="h-6 w-auto mr-2" src="https://img.icons8.com/ios-filled/200/ffffff/money-bag.png" alt="">$2.00</p>

        

        
        <p class="text-white text-2xl font-semibold ml-4 mb-4">Directions</p>
        <ul class="text-white text-lg font-medium mb-6 ml-4 text-gray-300">
            {#each steps as step}
                <li class="my-2">{step.maneuver.instruction}</li>
            {/each}
        </ul>

    </div>

    <div class="map" bind:this={mapContainer} />
</div>

<style>
    a{
        z-index: 2;
        position: absolute;
    }

    .map-wrap{
        width: 100%;
        height: 100%;
        overflow: hidden;
        margin: 0 0 0 0;
        padding: 0 0 0 0;
    }

    .map {
        position: absolute;
        width: 100%;
        height: 100%;
        margin: 0 0 0 0;
        padding: 0 0 0 0;
    }
</style>
