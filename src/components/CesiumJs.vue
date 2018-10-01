<template>
    <div id="cesiumContainer"></div>
</template>

<script>
import Cesium from 'cesium/Cesium';
import widgets from 'cesium/Widgets/widgets.css';

var image = {
  image: 'http://localhost:8000/images/track.png',
  width: 28,
  height: 28
};
export default {
  name: 'CesiumJs',
  data() {
    return {
      viewer: null
    };
  },
  mounted() {
    this.viewer = new Cesium.Viewer('cesiumContainer', {
      imageryProvider: Cesium.createTileMapServiceImageryProvider({
        url: Cesium.buildModuleUrl('Assets/Textures/NaturalEarthII')
      }),
      baseLayerPicker: false,
      geocoder: false
      // requestRenderMode: true
      // skyBox: false
    });
    // viewer.entities.add({
    //   position: Cesium.Cartesian3.fromDegrees(-35, 138),
    //   billboard: {
    //     // image: '../images/Cesium_Logo_overlay.png',
    //     image:
    //       'http://www.carpathianparks.org/cesium/Apps/Sandcastle/images/whiteShapes.png',
    //     imageSubRegion: new Cesium.BoundingRectangle(49, 43, 18, 18),
    //     color: Cesium.Color.LIME
    //     // width: 64,
    //     // height: 64
    //   }
    // });
    // this.viewer.entities.add({
    //   position: Cesium.Cartesian3.fromDegrees(138, -35),
    //   point: {
    //     pixelSize: 10,
    //     color: Cesium.Color.YELLOW
    //   }
    // });

    var ws = new WebSocket('ws://localhost:8181');
    ws.onopen = function() {
      console.log('websocket is connected ...');
      // ws.send('connected');
    };
    ws.onclose = function() {
      console.log('websocket is disconnected ...');
      // ws.send('disconnected');
      // ws.close();
    };
    ws.onmessage = this.onWsMessage;
    // ws.onmessage = function(ev) {
    //   //   console.log(ev.data);
    //   var jsondata = JSON.parse(ev.data);
    //   //   var jsondata = JSON.parse(ev.data);
    //   console.log(jsondata);
    //   for (var i = 0; i < jsondata.length; i++) {
    //     this.viewer.entities.add({
    //       position: Cesium.Cartesian3.fromDegrees(
    //         jsondata[i].longitude,
    //         jsondata[i].latitude
    //       ),
    //       point: {
    //         pixelSize: 10,
    //         color: Cesium.Color.YELLOW
    //       }
    //     });
    //   }
    // };
  },
  methods: {
    // onWsMessage: function(ev) {
    //   var jsondata = JSON.parse(ev.data);
    //   //   var jsondata = JSON.parse(ev.data);
    //   //   console.log(jsondata);
    //   //   this.viewer.entities.removeAll();
    //   for (var i = 0; i < jsondata.length; i++) {
    //     var entity = this.viewer.entities.getOrCreateEntity(i);
    //     entity.position = Cesium.Cartesian3.fromDegrees(
    //       jsondata[i].longitude,
    //       jsondata[i].latitude
    //     );
    //     //for a point
    //     // entity.point = {
    //     //   pixelSize: 10,
    //     //   color: Cesium.Color.YELLOW
    //     // };
    //     // for a billboard
    //     // entity.billboard = {
    //     //   image: 'http://localhost:8000/images/track.png',
    //     //   width: 16,
    //     //   height: 16
    //     // };
    //     entity.billboard = image;
    //     //for a polygon
    //     // this.viewer.entities.add({
    //     //   id: jsondata[i].id,
    //     //   position: Cesium.Cartesian3.fromDegrees(
    //     //     jsondata[i].longitude,
    //     //     jsondata[i].latitude,
    //     //     150000
    //     //   ),
    //     //   ellipse: {
    //     //     semiMinorAxis: 300000.0,
    //     //     semiMajorAxis: 300000.0,
    //     //     height: 200000.0,
    //     //     material: Cesium.Color.GREEN,
    //     //     outline: true // height must be set for outline to display
    //     //   }
    //     // });
    //     // this.viewer.entities.add({
    //     //   id: jsondata[i].id,
    //     //   position: Cesium.Cartesian3.fromDegrees(
    //     //     jsondata[i].longitude,
    //     //     jsondata[i].latitude
    //     //   ),
    //     //   point: {
    //     //     pixelSize: 10,
    //     //     color: Cesium.Color.YELLOW
    //     //   }
    //     // });
    //   }
    // },
    onWsMessage: function(ev) {
      var jsondata = JSON.parse(ev.data);
      //   var jsondata = JSON.parse(ev.data);
      //   console.log(jsondata);
      //   this.viewer.entities.removeAll();

      if (this.viewer.entities.values.length == 0) {
        for (var i = 0; i < jsondata.length; i++) {
          this.viewer.entities.add({
            id: jsondata[i].id,
            position: Cesium.Cartesian3.fromDegrees(
              jsondata[i].longitude,
              jsondata[i].latitude
            ),
            billboard: image
          });
        }
      } else {
        for (var i = 0; i < jsondata.length; i++) {
          // this.viewer.entities.getById(
          //   i
          // ).position = Cesium.Cartesian3.fromDegrees(
          //   jsondata[i].longitude,
          //   jsondata[i].latitude
          // );
          this.viewer.entities.values[
            i
          ].position = Cesium.Cartesian3.fromDegrees(
            jsondata[i].longitude,
            jsondata[i].latitude
          );
        }
      }
    }
  }
};
</script>

<style>
#cesiumContainer {
  /* width: 100%; */
  /* height: 100%; */
  width: 1024;
  height: 768;
  margin: 0;
  padding: 0;
  overflow: hidden;
}
</style>