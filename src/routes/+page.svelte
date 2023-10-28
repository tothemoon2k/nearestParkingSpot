<script>
    import { Map } from "mapbox-gl";
    import { onMount, onDestroy } from "svelte";
    import carIcon from "$lib/assets/car.png"

    let map;
    let mapContainer;
    let lng, lat, zoom;

    lng = -71.224518;
    lat = 42.213995;
    zoom = 9;

    onMount(() => {
        const initialState = { lng: lng, lat: lat, zoom: zoom };

        map = new Map({
            container: mapContainer,
            accessToken: "pk.eyJ1Ijoid29udG9uLWRvbiIsImEiOiJjbG9hODJra2gwYmxhMmxxb283eWVpZXY4In0.L7SGeg75p94O8OLTH8OS0g",
            style: "mapbox://styles/mapbox/navigation-night-v1",
            center: [initialState.lng, initialState.lat],
            zoom: initialState.zoom,
        });

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
            });
        
            console.log("hey1");

        navigator.geolocation.getCurrentPosition((position) => {
            console.log("hey")
            console.log(position.coords.longitude, position.coords.latitude)
            map.setCenter([position.coords.longitude, position.coords.latitude])
        }, (e) => console.log(e));
        console.log("hey2")
    });

</script>


<div class="map-wrap">
    <div class="map" bind:this={mapContainer} />
</div>

<style>

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
