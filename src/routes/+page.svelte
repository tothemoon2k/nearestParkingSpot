<script>
    import { Map } from "mapbox-gl";
    import { onMount, onDestroy } from "svelte";
    import carIcon from "$lib/assets/car.png";

    let map;
    let mapContainer;
    let lng, lat, zoom;
    let steps = [];
    let duration;
    let distance;
    let start = [];
    let mode = "Details";
    let finding = false;

    async function getRoute(end) {
            const query = await fetch(
                `https://api.mapbox.com/directions/v5/mapbox/driving/${start[0]},${start[1]};-80.85083554386717,35.2270691431859?steps=true&geometries=geojson&access_token=pk.eyJ1Ijoid29udG9uLWRvbiIsImEiOiJjbG9hODJra2gwYmxhMmxxb283eWVpZXY4In0.L7SGeg75p94O8OLTH8OS0g`,
                //random end address
                { method: 'GET' }
            );
            const json = await query.json();
            const data = json.routes[0];
            steps = data.legs[0].steps;
            duration = (Math.floor(data.duration / 60));
            distance = ((data.distance) * 0.000621371).toFixed(2);
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
    }

    onMount(() => {
        map = new Map({
            container: mapContainer,
            accessToken: "pk.eyJ1Ijoid29udG9uLWRvbiIsImEiOiJjbG9hODJra2gwYmxhMmxxb283eWVpZXY4In0.L7SGeg75p94O8OLTH8OS0g",
            style: "mapbox://styles/mapbox/navigation-night-v1",
            center: [-71.224518, 42.213995], //Inital Center
            zoom: 16, //Inital Zoom
        });

        map.on('load', () => {
            // Load an image from an external URL.
            map.loadImage(carIcon,
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
                                        'coordinates': [start[0], start[1]]
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
                            'icon-size': 0.12,
                            'icon-rotate': ['get', 'bearing'],
                        }
                    });
                    
            }
            );

            navigator.geolocation.getCurrentPosition((position) => {
                let userLng = position.coords.longitude;
                let userLat = position.coords.latitude;
                start = [userLng, userLat]
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

        const updateLocation = () => {
            navigator.geolocation.getCurrentPosition((position) => {
                let userLng = position.coords.longitude;
                let userLat = position.coords.latitude;
                start = [userLng, userLat];
                
                getRoute(start);
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
        }

        
        setInterval(updateLocation, 5000);
    });
    

    function formatDistance(meters) {
        const miles = (meters * 0.000621371);
        if (miles >= 1) {
            return `${miles.toLocaleString()} mi`; 
        } else {
            return `${(miles * 5280).toFixed(0)} ft`; 
        }
}

    const go = () => {
        map.zoomIn(4);
        mode = "Travel";
    }

    const find = () =>{
        getRoute(start);
        finding = true;
    }

    const iconImages = {
        'Turn left': 'https://cdn-icons-png.flaticon.com/512/724/724999.png',
        'Turn right': 'https://cdn-icons-png.flaticon.com/512/725/725000.png',
        'Go straight': 'https://static.thenounproject.com/png/802358-200.png',
        'Turn slight left': 'https://cdn-icons-png.flaticon.com/512/724/724999.png',
        'Turn slight right': 'https://cdn-icons-png.flaticon.com/512/725/725000.png',
        'depart': 'https://cdn-icons-png.flaticon.com/512/64/64227.png',
    };
        
</script>


<div class="map-wrap flex justify-center md:justify-start">
    {#if finding === false}
        <button on:click={find} class="z-10 m-6 inline-flex items-center justify-center px-8 py-4 md:py-6 text-base font-medium text-center text-green-100 border border-green-500 rounded-lg shadow-sm cursor-pointer hover:text-white bg-gradient-to-br from-green-500 via-green-500 to-green-600">
            <img class="h-14 mr-3.5" src="https://img.icons8.com/ios-filled/200/ffffff/parking.png" alt="">
            <span class="text-2xl relative flex justify-center items-center">Find the Nearest <br> Parking Spot</span>
        </button>
    {:else if finding === true}
        <div class="mt-5 infoW md:w-1/2 lg:w-1/3 h-fit {mode === "Details" ? "pb-14" : "py-16"} absolute bg-gray-800 z-10 md:m-6 shadow-xl rounded-2xl">
            {#if mode === "Details"}
                <h2 class="text-white text-2xl text-center mt-10 mb-8 font-semibold leading-6 mx-4 md:mx-0">The nearest parking space is {duration || 2} mins away</h2>
                <p class="text-white text-lg md:text-xl font-medium ml-5 flex items-center mb-1.5 md:mb-4"><img class="h-6 w-auto mr-1" src="https://img.icons8.com/ios-filled/200/ffffff/marker.png" alt=""> 300 NW Brevard Blvd</p>

                <p class="text-white text-lg md:text-xl font-medium ml-5 flex items-center mb-1.5 md:mb-4"><img class="h-6 w-auto mr-1" src="https://img.icons8.com/pastel-glyph/200/ffffff/route--v1.png" alt="">{distance || 1.4} Miles Away</p>
                
                <p class="text-white text-lg md:text-xl font-medium ml-5 flex items-center mb-4 md:mb-10"><img class="h-6 w-auto mr-2" src="https://img.icons8.com/ios-filled/200/ffffff/money-bag.png" alt="">$2.00</p>

                

                
                <p class="hidden lg:block text-white text-2xl font-semibold ml-5 mb-4">Directions</p>
                <ul class="hidden lg:block text-white text-lg font-medium mb-6 ml-5 text-gray-300">
                    {#each steps as step}
                        <li class="my-2">{step.maneuver.instruction}</li>
                    {/each}
                </ul>

                <button class="bg-green-500 font-medium text-xl text-white p-5 rounded-2xl absolute bottom-4 right-4" on:click={go}>Go</button>
            {:else if mode === "Travel"}
                <div class="flex items-center justify-center gap-12 w-full h-full">
                    <img class="h-24 w-auto" src={iconImages[steps[0].maneuver.instruction] || "https://img.icons8.com/isometric/400/car.png"} alt="">
                    <div>
                        <h1 class="text-white text-4xl text-center font-semibold">{formatDistance(steps[0].distance)}</h1>
                        <p class="text-white text-3xl font-medium flex items-center">{steps[0].maneuver.instruction}</p>
                    </div>
                </div>
            {/if}
        </div>
    {/if}

    <div class="map" bind:this={mapContainer} />
</div>

<style>
    

    @media (max-width: 768px) {
        .infoW{
            width: 91%;
        }
    }

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
