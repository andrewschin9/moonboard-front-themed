<template>
  <div class="home">
    <h1>CREATE A PROBLEM</h1>
    {{blankMessage}}<p><input v-model="prob_name" placeholder="Name of your problem (cannot leave blank)"></p>
    {{blankMessage}}{{blankMessage2}}<p><input v-model="grade" placeholder="Grade of your problem (don't add a V in front, must be at least 4)"></p>
    <p>Click to start selecting:</p>
    <button id="start" type="button" class="btn-round" v-on:click="selectStart()">Starting Hold(s)</button>
    <button id="middle" type="button" class="btn-round" v-on:click="selectMiddle()">Middle Hold(s)</button>
    <button id="finish" type="button" class="btn-round" v-on:click="selectFinish()">Finishing Hold(s)</button>
      <table id="board">
        <tr id="tr" v-for="(row, index1) in holds">
          <td v-for="(hold, index2) in row" v-on:click="changeTo(index1,index2,$event)">
            {{ hold }}
          </td>
        </tr>
      </table>
    <button type="button" class="submit" v-on:click="createProblem()">{{isLoggedIn()}}</button>{{blankMessage}}{{blankMessage2}}
  </div>
</template>
<style>
input {
  height: 35px;
  width: 500px;
}
button.submit {
  margin-top: 20px;
  margin-left: 10px;
  margin-right: 10px;
  height: 50px;
  width: 160px;
}
button#start {
  margin-left: 10px;
  margin-right: 10px;
  height: 60px;
  width: 140px;
  background-color: green;
  color: white;
}
button#middle {
  margin-left: 10px;
  margin-right: 10px;
  height: 60px;
  width: 140px;
  background-color: blue;
  color: white;
}
button#finish {
  margin-left: 10px;
  margin-right: 10px;
  height: 60px;
  width: 140px;
  background-color: red;
  color: white;
}
table#board {
  background: url(mbimg.png);
  background-size: cover;
}
table#board,
th,
td {
  table-layout: fixed;
  margin-top: 10px;
  margin-left: 10px;
  margin-right: auto;
  font-size: 1px;
  color: transparent;
  align-items: center;
  justify-content: center;
  border: 4px solid transparent;
  padding: 1px;
  height: 40px;
  width: 40px;
  border-collapse: separate;
}
table,
th,
td.red {
  border-color: red;
}
table,
th,
td.blue {
  border-color: blue;
}
table,
th,
td.green {
  border-color: rgb(85, 203, 85);
}
</style>
<script>
import axios from "axios";
export default {
  data: function () {
    return {
      loggedIn: "",
      blankMessage: "",
      blankMessage2: "",
      prob_name: "",
      grade: "",
      type: "S",
      circleColor: "rgb(85, 203, 85)",
      holds: [
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", ""],
      ],
    };
  },
  created: function () {},
  methods: {
    changeTo: function (index1, index2, element) {
      // console.log(typeof element.target.style.borderColor);
      if (element.target.style.borderColor === "") {
        element.target.style.borderColor = this.circleColor;
      } else if (element.target.style.borderColor !== "") {
        element.target.style.borderColor = "";
      }
      if (!this.holds[index1][index2]) {
        this.holds[index1][index2] = this.type;
      } else if (this.holds[index1][index2] !== "") {
        this.holds[index1][index2] = "";
      }
      console.log(this.holds);
      this.$forceUpdate();
      // this.information = thing;
    },
    selectStart: function () {
      this.type = "S";
      this.circleColor = "rgb(85, 203, 85)";
    },
    selectMiddle: function () {
      this.type = "M";
      this.circleColor = "Blue";
    },
    selectFinish: function () {
      this.type = "F";
      this.circleColor = "Red";
    },
    createProblem: function () {
      var grade = parseInt(this.grade);
      console.log(typeof grade);
      if (this.loggedIn === "false") {
        this.blankMessage = "";
        this.blankMessage2 = "Please log in";
      } else if (this.prob_name === "" || this.grade === "") {
        this.blankMessage = "NAME AND GRADE CAN'T BE BLANK";
      } else if (isNaN(grade)) {
        this.blankMessage = "";
        this.blankMessage2 = "GRADE MUST ONLY BE A NUMBER";
      } else {
        this.blankMessage = "";
        this.blankMessage2 = "";
        var params = {
          prob_name: this.prob_name,
          grade: this.grade,
          holds: this.holds,
        };
        axios.post("http://localhost:3000/api/problems", params);
        this.blankMessage = "SUCCESFULLY CREATED";
      }
    },
    isLoggedIn: function () {
      if (localStorage.getItem("jwt")) {
        this.loggedIn = "true";
        return "Submit";
      } else {
        this.loggedIn = "false";
        return "Must be logged in to submit problems";
      }
    },
    // changeColor: function (index2) {
    //   var table = document.getElementById("tr").getElementsByTagName("td");
    //   var td = table[index2];
    //   td.style.borderColor = this.finish;
    // },
  },
};
</script>