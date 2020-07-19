# React-Native Leaflet by Aristech

### FPackage working with react-native. Not tested on expo

This package requires the html file from android assets using react-native-fs

```
~/yourProject/android/app/src/main/assets/index.html
```

or create an assets folder in your project dir & add in react-native.config.js

```
module.exports = {
  project: {
    ios: {},
    android: {},
  },
  assets: ['./src/assets'],
};
```

You can add assets with:

```
react-native link
```

## A Leaflet map component with no native code for React Native applications

### Why Use This Library

### This is port from reggie3/react-native-webview-leaflet

This component is useful if you want to display HTML elements on an interactive map. Since the elements are standard HTML items, they can be SVG's, emojis, text, images, etc.

Additionally, the elements can even be animated, updated, and changed as required.

### Why Not Use This Library

You may not want to use this library if you'd rather use Google map tiles and data vice the tiles and map data from Open Street Maps.

## Installation

Install using npm or yarn like this:

```javascript
npm install --save rn-leaflet
```

or

```javascript
yarn add rn-leaflet
```

## Usage

and import like so

```javascript
import WebViewLeaflet from "rn-leaflet";
```

A typical example is shown below:

```javascript
<WebViewLeaflet
  ref={(component) => (this.webViewLeaflet = component)}
  // The rest of your props, see the list below
/>
```

## Props

| property            | required | type                            | purpose                                                                                                                                                                                                         |
| ------------------- | -------- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| backgroundColor     | optional | string                          | Color seen prior to the map loading                                                                                                                                                                             |
| doShowDebugMessages | optional | boolean                         | show debug information from the component containing the Leaflet map                                                                                                                                            |
| loadingIndicator    | optional | React.ReactElement              | custom component displayed while the map is loading                                                                                                                                                             |
| onError             | optional | function                        | Will receive an error event                                                                                                                                                                                     |
| onLoadEnd           | optional | function                        | Called when map stops loading                                                                                                                                                                                   |
| onLoadStart         | optional | function                        | Called when the map starts to load                                                                                                                                                                              |
| onMessageReceived   | required | function                        | This function receives messages in the form of a WebviewLeafletMessage object from the map                                                                                                                      |
| mapLayers           | optional | MapLayer array                  | An array of map layers                                                                                                                                                                                          |
| mapMarkers          | optional | MapMarker array                 | An array of map markers                                                                                                                                                                                         |
| mapShapes           | optional | MapShape[]                      | An array of map shapes                                                                                                                                                                                          |
| mapCenterPosition   | optional | {lat: [Lat], lng: [Lng]} object | The center position of the map. This coordinate will not be accurate if the map has been moved manually. However, calling the map's setMapCenterPosition function will cause the map to revert to this location |
| ownPositionMarker   | optional | Marker                          | A special marker that has an ID of OWN_POSTION_MARKER_ID                                                                                                                                                        |  |
| zoom                | optional | number                          | Desired zoom value of the map                                                                                                                                                                                   |

### Example Marker

```javascript
ownPositionMarker={{
    id: '1',
    coords: {lat: 36.00, lng, -76.00},
    icon: "❤️",
    size: [24, 24],
    animation: {
      name: AnimationType.BOUNCE,
      duration: ".5",
      delay: 0,
      interationCount: INFINITE_ANIMATION_ITERATIONS
    }
  }}
```

After loading, the map expects to receive an array of map layer information objects. A sample object showing a [MapBox](https://www.mapbox.com/) tile layer is shown below.

```javascript
  {
    baseLayerName: 'OpenStreetMap',  // the name of the layer, this will be seen in the layer selection control
    baseLayerIsChecked: 'true',  // if the layer is selected in the layer selection control
    layerType: 'TileLayer',  // Optional: a MapLayerType enum specifying the type of layer see "Types of Layers" below. Defaults to TILE_LAYER
    baseLayer: true,
    // url of tiles
    url: `https://api.tiles.mapbox.com/v4/mapbox.streets/{z}/{x}/{y}.png?access_token=${mapboxToken}`,
    // attribution string to be shown for this layer
    attribution:
      '&amp;copy <a href=&quot;http://osm.org/copyright&quot;>OpenStreetMap</a> contributors'
  }
```

### Types of Layers

```javascript
export enum MapLayerType {
  IMAGE_LAYER = "ImageOverlay",
  TILE_LAYER = "TileLayer",
  VECTOR_LAYER = "VectorLayer",
  VIDEO_LAYER = "VideoOverlay",
  WMS_TILE_LAYER = "WMSTileLayer"
}
```

### Communicating with the map

#### Listening for Events

This library supports map clicked, map marker clicked, and the map events that are exposed by Leaflet.

##### Leaflet Map Events

The following Map Events are passed to the function designated by the onMessageReceived prop.

```javascript
export enum WebViewLeafletEvents {
  MAP_COMPONENT_MOUNTED = "MAP_COMPONENT_MOUNTED",
  MAP_READY = "MAP_READY",
  DOCUMENT_EVENT_LISTENER_ADDED = "DOCUMENT_EVENT_LISTENER_ADDED",
  WINDOW_EVENT_LISTENER_ADDED = "WINDOW_EVENT_LISTENER_ADDED",
  UNABLE_TO_ADD_EVENT_LISTENER = "UNABLE_TO_ADD_EVENT_LISTENER",
  DOCUMENT_EVENT_LISTENER_REMOVED = "DOCUMENT_EVENT_LISTENER_REMOVED",
  WINDOW_EVENT_LISTENER_REMOVED = "WINDOW_EVENT_LISTENER_REMOVED",
  ON_MOVE_END = "onMoveEnd",
  ON_MOVE_START = "onMoveStart",
  ON_MOVE = "onMove",
  ON_RESIZE = "onResize",
  ON_UNLOAD = "onUnload",
  ON_VIEW_RESET = "onViewReset",
  ON_ZOOM_END = "onZoomEnd",
  ON_ZOOM_LEVELS_CHANGE = "onZoomLevelsChange",
  ON_ZOOM_START = "onZoomStart",
  ON_ZOOM = "onZoom",
  ON_MAP_TOUCHED = "onMapClicked",
  ON_MAP_MARKER_CLICKED = "onMapMarkerClicked"
}
```

Events prefixed with "ON" will receive the below object containing information about the map

```javascript
{
  center, // center of the map
    bounds, // the bounds of the map
    zoom; // the zoom level of the map
}
```

### Creating Map Markers

```javascript
{
  id: uuidV1(), // The ID attached to the marker. It will be returned when onMarkerClicked is called
  position: {lat: [LATITTUDE], lng: [LONGITUDE]}, // Latitude and Longitude of the marker
  icon: '🍇', // HTML element that will be displayed as the marker.  It can also be text or an SVG string.
  size: [32, 32],
  animation: {
    duration: getDuration(),
    delay: getDelay(),
    iterationCount,
    type: AnimationType.BOUNCE
  }
}
```

### Adding Leaflet Geometry Layers to the Map

Thanks to @gotoglup for the PR adding leaflet geometry layers. A geometry layer can be added to the may by following the example below:

```javascript
mapShapes={[
  {
    shapeType: MapShapeType.CIRCLE,
    color: "#123123",
    id: "1",
    center: { lat: 34.225727, lng: -77.94471 },
    radius: 2000
  }
]}
```

### Available Animations

Marker animations can be specified by setting the animation.type of the marker object to an AnimationType enum.
Values for AnimationType can be found in the models.ts file in the WebViewLeaflet directory of this project.

```javascript
@keyframes spin {
  50% {
    transform: rotateZ(-20deg);
    animation-timing-function: ease;
  }
  100% {
    transform: rotateZ(360deg);
  }
}
```

```
## Changelog



## LICENSE

MIT
```
