<template>
  <form>
    <v-label>Airline Reservation</v-label>
    <v-text-field
      v-model="name"
      :error-messages="nameErrors"
      :counter="20"
      class="mx-5 my-5"
      label="Passenger Name"
      required
      @input="$v.name.$touch()"
      @blur="$v.name.$touch()"
    ></v-text-field>
    <v-text-field
      v-model="trips"
      :error-messages="emailErrors"
      label="Number of Trips"
      class="mx-5 my-5"
      required
    ></v-text-field>
    <v-select
      v-model="airline"
      :airline="airline"
      class="mx-5 my-5"
      label="Airline"
    ></v-select>
    <v-checkbox
      v-model="checkbox"
      class="mx-10 my-10"
      :error-messages="checkboxErrors"
      label="Do you agree?"
      required
      @change="$v.checkbox.$touch()"
      @blur="$v.checkbox.$touch()"
    ></v-checkbox>
    <v-btn class="mx-5" @click="addPassenger()"> Submit </v-btn>
    <v-btn @click="clear" class="mx-4"> clear </v-btn>

    <v-simple-table fixed-header height="500px" dark class="mx-5 my-5">
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">Passenger Name</th>
            <th class="text-left">Number of Trips</th>
            <th class="text-left">Airline</th>
            <th class="text-left"></th>
            <th class="text-left"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="airline in airlineData" :key="airline.id">
            <td>{{ airline.name }}</td>
            <td>{{ airline.trips }}</td>
            <td>{{ airline.airlineName }}</td>
            <td>
              <v-btn
                text
                color="blue"
                small
                @click="updatePassenger(airline._id)"
              >
                Edit
              </v-btn>
            </td>
            <td>
              <v-btn
                text
                color="red"
                small
                @click="deletePassenger(airline._id)"
              >
                Delete
              </v-btn>
            </td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
  </form>
</template>
<script>
import axios from "axios";

export default {
  name: "HelloWorld",

  props: {
    msg: String,
  },
  data() {
    return {
      airlineData: [],
      passData: [],
      alldata: [],
      adata: [],
      reqObj: {
        name: "John Doe",
        trips: 250,
        airline: 5,
        airlineName: "",
      },
    };
  },

  methods: {
    async addPassenger() {
      //console.log(this.pname);
      await axios
        .post("https://api.instantwebtools.net/v1/passenger", this.reqObj)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => console.log(error));
    },
    async getData() {
      await axios
        .get(`https://api.instantwebtools.net/v1/passenger?page=3889&size=10`)
        .then((response) => (this.airlineData = response.data.data))
        .catch((error) => console.log(error));
      console.log(this.airlineData);

      for (var i = 0; i < this.airlineData.length; i++) {
        this.alldata = this.airlineData[i].airline;
        // console.log(this.alldata);
        this.adata.push(this.alldata);
        //console.log(
        // "Elements from the array are: " + JSON.stringify(this.adata)
        // );
      }
      console.log("Final array:" + JSON.stringify(this.adata));
    },
    async deletePassenger(id) {
      await axios
        .delete("https://api.instantwebtools.net/v1/passenger/" + id)
        .then(() => {
          this.getData();
          alert("successfully deleted the passenger record");
        });
    },
    async updatePassenger(id) {
      var airlinename = "";
      var passid = "";

      await axios
        .get(`https://api.instantwebtools.net/v1/passenger/` + id)
        .then((response) => (this.passData = response.data))
        .catch((error) => console.log(error));
      console.log(this.passData);

      this.reqObj.name = this.passData.name;
      this.reqObj.trips = this.passData.trips;

      passid = this.passData._id;
      this.deletePassenger(id);

      for (var i = 0; i < this.passData.airline.length; i++) {
        airlinename = this.passData.airline[i].airline.name;
      }
      this.reqObj.airline = airlinename;

      await axios
        .post(
          "https://api.instantwebtools.net/v1/passenger/" + passid,
          this.reqObj
        )
        .then((response) => {
          console.log("Passenger data updated successfully" + response.data);
        })
        .catch((error) => console.log(error));
    },
    clear() {
      this.name = "";
      this.trips = 0;
      this.airlineName = "";
    },
  },

  mounted() {
    this.getData();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
