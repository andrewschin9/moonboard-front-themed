<template>
  <div class="home">
    {{isLoggedIn()}}
    <h1>PROBLEMS LIST</h1>
    <hr>
    <hr>
    <p id="white">Search names: <input type="list" v-model='search_name' label='names'></p>
    <p id="white">Search grades: <select name="grades" id="grades" v-model='search_grade'>
        <option value="4">4</option>
        <option value="5">5</option>
        <option value="6">6</option>
        <option value="7">7</option>
        <option value="8">8</option>
        <option value="9">9</option>
        <option value="10">10</option>
        <option value="11">11</option>
        <option value="12">12</option>
        <option value="13">13</option>
        <option value="14">14</option>
      </select>
    </p>
    <p id="tip1">Click on a name to view a problem</p>
    <p id="tip1">In pop-up, click on + to add to favorites</p>
    <hr id="t1">
     <div v-for="problem in filterBy(filterBy(problems, search_name , 'prob_name'), search_grade , 'grade')">
      <p id="showProbs" v-on:click="showProblem(problem); filterPicked()"><strong id="t1">Name: </strong> {{problem.prob_name}}</p>
      <p id="grayish"><strong id="t2">Grade:</strong> {{problem.grade}}</p>
      
      <dialog id="problem-details">
        <form method = "dialog">
          <button id="close" class="btn-round">Close</button>
          <span id="fave" v-on:click="toggleFavorite()" v-bind:class="isFavorite()"></span>
          <p><strong id="t1">Name: </strong>{{currentProblem.prob_name}}</p>
          <p><strong id= "t1">Grade: </strong>{{currentProblem.grade}}</p>
          <table id="board">
            <tr id="tr" v-for="(row, index1) in holds">
              <td v-bind:class="updateHolds(index1,index2)" v-for="(hold, index2) in row">
            {{ hold }}
              </td>
            </tr>
          </table>
        </form>
      </dialog>
    </div>
  </div>
</template>

<style>
dialog#problem-details {
  background-color: rgb(122, 122, 122);
}
h1 {
  color: white;
}
span#fave {
  margin-left: 10px;
  font-size: 20px;
}
hr#t1 {
  border-color: black;
}
strong#t1 {
  color: rgba(255, 255, 255, 0.531);
}
strong#t2 {
  color: rgba(255, 255, 255, 0.531);
  margin-left: 10px;
  font-size: 13px;
}
p#grayish {
  color: gray;
}
,
p#white {
  color: white;
}
p#showProbs {
  color: rgb(163, 139, 4);
  margin-bottom: 0px;
  margin-top: 40px;
}
button#close {
  margin-bottom: 15px;
  margin-right: 20px;
  border-color: transparent;
  background-color: rgb(43, 43, 43);
  color: rgb(183, 183, 183);
}
p#tip1 {
  color: rgb(183, 183, 183);
  font-style: italic;
}
hr#bold {
  border-color: black;
}
</style>

<script>
import axios from "axios";
import Vue2Filters from "vue2-filters";

export default {
  mixins: [Vue2Filters.mixin],
  data: function () {
    return {
      favorites: [],
      logInStatus: "",
      pickedHolds: [],
      problems: [],
      prob_name: "",
      grade: "",
      search_name: "",
      search_grade: "",
      currentProblem: {},
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
  created: function () {
    this.problemsIndex();
    this.pickedHoldsIndex();
    this.favoritesIndex();
  },
  methods: {
    toggleFavorite: function () {
      var fave = this.favorites.find(
        (item) => item.problem_id === this.currentProblem.id
      );
      if (fave) {
        axios
          .delete("http://localhost:3000/api/favorites/" + fave.id)
          .then((response) => console.log(response));
        var index = this.favorites.indexOf(fave);
        this.favorites.splice(index, 1);
      } else {
        var params = {
          problem_id: this.currentProblem.id,
        };
        axios
          .post("http://localhost:3000/api/favorites", params)
          .then((response) => {
            this.favorites.push(response.data);
          });
      }
    },
    isFavorite: function () {
      if (
        this.favorites.find(
          (item) => item.problem_id === this.currentProblem.id
        )
      ) {
        return "ti-star";
      } else {
        return "ti-plus";
      }
    },
    // makeFavorite: function () {
    //   var params = {
    //     prob_name: this.prob_name,
    //     grade: this.grade,
    //     holds: this.holds,
    //   };
    //   axios.post("http://localhost:3000/api/favorites", params);
    // },
    favoritesIndex: function () {
      axios.get("http://localhost:3000/api/favorites").then((response) => {
        this.favorites = response.data;
      });
    },
    pickedHoldsIndex: function () {
      axios.get("http://localhost:3000/api/pickedholds").then((response) => {
        this.pickedHolds = response.data;
      });
    },
    filterPicked: function () {
      var filteredPicked = this.pickedHolds.filter(
        (item) => item.problem_id === this.currentProblem.id
      );
      var i;
      for (i = 0; i < filteredPicked.length; i++) {
        var a = filteredPicked[i].row;
        var b = filteredPicked[i].column;
        var c = filteredPicked[i].value;
        this.holds[a][b] = c;
      }
    },
    problemsIndex: function () {
      axios.get("http://localhost:3000/api/problems").then((response) => {
        this.problems = response.data;
      });
    },
    updateHolds: function (index1, index2) {
      if (this.holds[index1][index2] === "S") {
        return "green";
      } else if (this.holds[index1][index2] === "M") {
        return "blue";
      } else if (this.holds[index1][index2] === "F") {
        return "red";
      }
    },
    // hideProblem: function (problem) {
    //   document.querySelector("#problem-details").hideModal();
    // },
    showProblem: function (problem) {
      this.holds = [
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
      ];
      this.currentProblem = problem;
      document.querySelector("#problem-details").showModal();
    },
    isLoggedIn: function () {
      if (localStorage.getItem("jwt")) {
        return "";
      } else {
        return "---> Must be logged in to view problems <---";
      }
    },
  },
};
</script>
