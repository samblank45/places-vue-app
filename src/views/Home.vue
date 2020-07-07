<template>
  <div class="home">
  
  <div>
    Name: <input type="text" v-model="NewPlaceName"> <br>
    Address: <input type="text" v-model="NewPlaceAddress"> <br>
    <button v-on:click="createPlace()">Create</button>
  </div>

  <div v-for="place in places">
    <h1> {{ place.name}} </h1>
    <p> {{ place.address}} </p>
    <button v-on:click="showPlace(place)">More Info</button>
  </div>

  <dialog id="place-details">
    <form method="dialog">
      <h1>name: <input type="text" v-model="currentPlace.name"></h1>
      <p>address: <input type="text" v-model="currentPlace.address"></p>
      <button>close</button>
      <button v-on:click="updatePlace(currentPlace)">Update</button>
      <button v-on:click="destroyPlace(currentPlace)">destroy</button>
    </form>
  </dialog>

  </div>
</template>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      places: [],
      currentPlace: {},
      updateErrors: [],
      NewPlaceName: "",
      NewPlaceAddress: ""
    };
  },
  created: function() {
    axios.get("/api/places").then(response => {
      console.log("all places", response.data);
      this.places = response.data;
    });
  },
  methods: {
    createPlace: function() {
      var params = {
        name: this.NewPlaceName,
        address: this.NewPlaceAddress
      };
      axios
        .post("api/places", params)
        .then(response => {
          console.log("success", response.data);
          this.places.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function(place) {
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
      console.log(place);
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios
        .patch(`/api/places/${place.id}`, params)
        .then(response => {
          console.log("successfully updated", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.updateErrors = error.response.data.errors;
        });
    },
    destroyPlace: function(place) {
      axios.delete(`/api/places/${place.id}`).then(response => {
        console.log("successfully destroyed", response.data);
        var index = this.places.indexOf(place);
        this.products.splice(index, 1);
      });
    }
  }
};
</script>