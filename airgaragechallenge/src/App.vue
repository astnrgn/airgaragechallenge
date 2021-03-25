<template>
  <div id="app">
    <div class="search-header-and-input">
      <h2>Search Parking from Lowest to Highest</h2>
      <div>
        <input @input="getLocationInput" />
        <button @click="getYelpLocation">Search</button>
      </div>
    </div>
    <div>
      <div
        v-for="business in lowestToHighestParkingBusinesses"
        :key="business.id"
        class="parking-business-card"
      >
        <img
          v-if="business.image_url"
          :src="business.image_url"
          :alt="business.name"
        />
        <p>
          {{ business.location.display_address.join() }}
        </p>
        <p>
          {{ business.review_count }}
        </p>
        <a :href="business.url" target="_blank">
          {{ business.url }}
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  components: {},
  props: {},
  data: function () {
    return {
      apiKey:
        "mi5qSSqdhmrNXBjLq5MBMwuqcS0q8aE4u52fwqrG8CkrBjjksgdV8ZblHdh4ThtDqQVFapfOwrCqadcTH4sJIMhQgEcWpc0bK_9ms_rJ1H-xMT1Amp4tmH_PhAg3X3Yx",
      location: "",
      searchedParkingBusinesses: [],
    };
  },
  computed: {
    parkingBusinessesWithScore() {
      return this.searchedParkingBusinesses.map((business) => {
        let calculatedScore =
          (business.review_count * business.rating) /
          (business.review_count + 1);
        return { ...business, score: calculatedScore };
      });
    },
    lowestToHighestParkingBusinesses() {
      return this.parkingBusinessesWithScore.concat().sort((a, b) => {
        return a.score - b.score;
      });
    },
  },
  methods: {
    getLocationInput(event) {
      this.location = event.target.value;
    },
    getYelpLocation() {
      axios
        .get(
          "https://cors-anywhere.herokuapp.com/https://api.yelp.com/v3/businesses/search",
          {
            headers: {
              Authorization: `Bearer ${this.apiKey}`,
            },
            params: {
              location: this.location,
              categories: "parking",
            },
          }
        )
        .then((res) => {
          this.searchedParkingBusinesses = res.data.businesses;
        })
        .catch((err) => {
          console.log("err", err);
        });
    },
  },
};
</script>

<style>
.search-header-and-input {
  width: 100%;
  margin: 0 auto;
  display: flex;
  align-items: center;
  flex-direction: column;
  text-align: center;
}

.parking-business-card {
  border: 1px solid;
  display: grid;
  grid-template-columns: 1fr auto auto auto;
  grid-gap: 10px;
  align-items: center;
  margin: 120px auto;
}
</style>