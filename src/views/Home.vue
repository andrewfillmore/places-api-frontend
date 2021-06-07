<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>Add Your Favorite Venue</h2>
    <p>Name: <input type="text" v-model="createPlaceName" /></p>
    <p>Address: <input type="text" v-model="createPlaceAddress" /></p>
    <br />
    <button v-on:click="createPlace">Add Venue</button>
    <div v-for="place in places" v-bind:key="place.id">
      <h1>Place: {{ place.name }}</h1>
      <h1>Address: {{ place.address }}</h1>
      <button v-on:click="showPlace(place)">Venue Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Venue Info</h1>
        <img src="" alt="" />
        <p>Name: <input type="text" v-model="currentPlace.name" /></p>
        <p>Address: <input type="integer" v-model="currentPlace.address" /></p>

        <button v-on:click="updatePlace()">Update Venue</button>
        <button v-on:click="destroyPlace()">Delete Venue</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to Chicago Music Venues!",
      places: [],
      currentPlace: {},
      createPlaceName: "",
      createPlaceAddress: "",
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log("Places Array", response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.createPlaceName,
        address: this.createPlaceAddress,
      };
      axios
        .post("http://localhost:3000/places", params)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function () {
      console.log(this.currentPlace);
      var updatePlaceParams = {
        name: this.currentPlace.name,
        address: this.currentPlace.address,
      };
      axios
        .patch(
          `http://localhost:3000/places/${this.currentPlace.id}`,
          updatePlaceParams
        )
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function () {
      axios
        .delete(`http://localhost:3000/places/${this.currentPlace.id}`)
        .then((response) => {
          console.log("Venue Removed", response.data);
          var index = this.places.indexOf(this.currentPlace);
          this.places.splice(index, 1);
        });
    },
  },
};
</script>
