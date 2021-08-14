<template>
  <div class="main">
    <div class="flex">
      <div class="map-holder">
        <div id="map"></div>
      </div>
    </div>
     <span class="label">Longitude:</span>
      <input v-model.number="longitudeForm" class= 'input-group' placeholder="add coordinates"  >
      <p v-if="!longIsValid" class="error-message"> Valid longitude required</p> 
       <span class="label">Latitude:</span>
      <input  v-model.number="latitudeForm" class= 'input-group'  placeholder="add coordinates" >
         <p v-if="!latIsValid" class="error-message"> Valid latitude required</p> 
      <span class="label">Description:</span>
        <input v-model="descriptionBox"  class= 'input-group' placeholder="add description" >
    
        <span class="label">Photo Direction:</span>
      <input v-model="photoDirection" class= 'input-group' placeholder="add a direction">
      <span class="label">Name:</span>
      <input v-model="toiletNameForm" class= 'input-group' placeholder="add the name of the loo">
     
      <button type="button" :disabled="!formIsValid"  class="point-btn" @click="addCircle(toiletNameForm,descriptionBox,longitudeForm,latitudeForm);submitPoint()">Generate point</button>
  </div>
</template>


<script>
  import mapboxgl from "mapbox-gl";
  export default {
    data() {
      return {
        toiletName:"",
        location: "",
        longitudeForm: null,
        latitudeForm:null,
        descriptionBox:null,
        photoDirection:null,
        toiletNameForm:null,

        access_token: "MAPBOX KEY GOES HERE",
        center: [-2.7306302,51.0838057],
        map: {},
        circleStyle : {'circle-radius': 9,
          'circle-opacity':0.5,
          'circle-stroke-width': 1,
          'circle-color': '#f79d36',
          'circle-stroke-color': 'grey'},
      };
    },
    mounted() {
      this.createMap();

    },

    computed:{

        longIsValid () { return typeof this.longitudeForm === 'number' && this.longitudeForm >-180 && this.longitudeForm < 180
        },

        latIsValid () { return typeof this.latitudeForm === 'number' && this.latitudeForm >-90 && this.latitudeForm < 90
        },
        
       formIsValid() {
          return this.longIsValid & this.latIsValid}


    },
    

   

    methods: {

        submitPoint(){
          
      
          if (this.FormIsValid){
            console.log("Long valid")
          }

          else{
            console.log("form invalid.....")
          }
        }

        ,
          

    addCircle(toiletName,description,longitude,latitude,){
        // ToDo Functional API removes the need for this section, update data by replacement rather than creating  different sources & layers
        this.map.addSource(toiletName, {
          "type": "geojson",
          "data": {
          "type": "Feature",
          "geometry": {
          "type": "Point",
          "coordinates": [longitude, latitude]},
          'properties': {
                  'fid':"1",
                  "name":toiletName,
                  "creation_date":new Date().toISOString(),
                  "notes":description,
                  "direction":"blank",
                  "auth_id":"1",
                  "photo_id":"blank"
                  }
            }
          })
        
        this.map.addLayer({
          'id': toiletName,
          'type': 'circle',
          'source': toiletName,
          'paint':this.circleStyle
        }) 


           this.map.on('click', toiletName, (e) => {
              
              const coordinates = e.features[0].geometry.coordinates.slice();
              const name =e.features[0].properties.name;
              const notes = e.features[0].properties.notes;
              
              
              while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
                coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
              }
              
              new mapboxgl.Popup()
                .setLngLat(coordinates)
                .setHTML('<strong><p>Name: '+name+'</p> <p>Note: '+notes+'</p><img class=popup_image src=https://ghubsample.s3.eu-west-2.amazonaws.com/images/20210813929_2_2.png>,</p>')//ToDo wrap this in function
                .addTo(this.map);
            });
            //ToDo couldn't work out a good way of storing image and then rendering in pop up, if had API would be straightforward to call image once uploaded
           this.map.on('mouseenter', toiletName, () => {
              this.map.getCanvas().style.cursor = 'pointer';
            });
            
            this.map.on('mouseleave', toiletName, () => {
              this.map.getCanvas().style.cursor = '';
            });
            //ToDo duplicated code should be a function
    }
,



      async createMap() {
        try {
          mapboxgl.accessToken = this.access_token;

        const response = await fetch("https://ghubsample.s3.eu-west-2.amazonaws.com/toilet_locations.geojson")
        const data = await response.json();
        this.geojson = data;



          this.map = new mapboxgl.Map({
            container: "map",
            style: "mapbox://styles/mapbox/streets-v11",
            center: this.center,
            zoom: 10,
          });
          this.map.on('load', () => {
            
            this.map.addSource('toiletData', {
              type: 'geojson',
              // Use a URL for the value for the `data` property.
              data: this.geojson
            });
            this.map.addLayer({
              'id': 'toilet-layer',
              'type': 'circle',
              'source': 'toiletData',
              'paint': this.circleStyle
            })
            this.map.on('click', 'toilet-layer', (e) => {
              
              const coordinates = e.features[0].geometry.coordinates.slice();
              const name =e.features[0].properties.name;
              const notes = e.features[0].properties.notes;
              const photoID = e.features[0].properties.photo_id;
              
              
              while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
                coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
              }
              
              new mapboxgl.Popup()
                .setLngLat(coordinates)
                .setHTML('<strong><p>Name: '+name+'</p> <p>Note: '+notes+'</p><img class=popup_image src=https://ghubsample.s3.eu-west-2.amazonaws.com/images/'+photoID+'.png>,</p>')
                .addTo(this.map);
            });
            
            this.map.on('mouseenter', 'toilet-layer', () => {
              this.map.getCanvas().style.cursor = 'pointer';
            });
            
            this.map.on('mouseleave', 'toilet-layer', () => {
              this.map.getCanvas().style.cursor = '';
            });

          });
        } catch (err) {
          console.log("map error", err);
        }
      },
    }
  };
</script>