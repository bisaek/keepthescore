<template>
  <ul>
    <li v-for="player in players" :key="player" class="m-4 relative">
      {{ player.name }}:
      <input type="number" v-model="player.score" class="bg-gray-800" />
      <button v-on:click="removePlayer(player.id)">remove</button>
      <div class="inset-y-0 right-0 absolute">
        <button v-for="changedNumber in scoreChangedButtons" :key="changedNumber" 
        v-on:click="changeScore(player, -changedNumber)" class="bg-black ml-4 px-2">
          -{{changedNumber}}
        </button>
        <input v-model="player.newScore" type="number" class="text-black ml-4" />
        <button v-on:click="changeScore(player)" class="bg-black ml-4 px-2">
          change score
        </button>
        <button v-for="changedNumber in scoreChangedButtons" :key="changedNumber" 
        v-on:click="changeScore(player, changedNumber)" class="bg-black ml-4 px-2">
          +{{changedNumber}}
        </button>
      </div>
    </li>
    <li>
      <form v-on:submit.prevent="addPlayer">
        <input
          type="text"
          placeholder="new player"
          v-model="newPlayerName"
          class="text-black"
        />
        <button type="submit">add player</button>
      </form>
    </li>
  </ul>
  <div class="absolute inset-x-0 bottom-0">
    <ul>
      <li class="save load m-4">
        <input
          type="text"
          v-model="saveName"
          placeholder="name"
          class="text-black p-1"
        />
        <button v-on:click="save" class="bg-black ml-4 p-1">save</button>
        <button v-on:click="load" class="bg-black ml-4 p-1">load</button>
        <button v-on:click="removeSave" class="bg-black ml-4 p-1">
          remove save
        </button>
        <button v-on:click="seeSaves = !seeSaves" class="bg-black ml-4 p-1">
          see saves
        </button>
      </li>
      <div v-if="seeSaves">
        <li v-for="save in saves" :key="(save, seeSaves)">
          {{ save }}
        </li>
      </div>
    </ul>
  </div>
</template>

<script>
import './assets/tailwind.css'

export default {
  name: "App",
  data() {
    return {
      players: [], //players array. every player is a object with name, score, newScore and id
      newPlayerName: "",
      newPlayerId: 0,
      saveName: "",
      seeSaves: false,
      saves: {},
      scoreChangedButtons: [1, 3, 5, 10, 50]
    };
  },
  methods: {
    addPlayer: function () {
      this.players.push({
        name: this.newPlayerName,
        score: 0,
        newScore: 1,
        id: this.newPlayerId,
      });
      this.newPlayerName = "";
      this.newPlayerId++;
    },
    removePlayer: function (playerId) {
      this.players.splice(
        this.players.findIndex((x) => x.id === playerId),
        1
      );
    },
    save: function () {
      // create a new let and set is to the localStorage
      let saves = JSON.parse(localStorage.getItem("keepTheScore"));
      // if the localStorage is null set is to an object
      if (saves == null) saves = {};
      // update the saved to the localStorage
      saves[this.saveName] = this.players;
      localStorage.setItem("keepTheScore", JSON.stringify(saves));
      // update the saved to this.saves
      this.saves = Object.keys(
        JSON.parse(localStorage.getItem("keepTheScore"))
      );
    },
    load: function () {
      this.players = JSON.parse(localStorage.getItem("keepTheScore"))[
        this.saveName
      ];
    },
    removeSave: function () {
      let saves = JSON.parse(localStorage.getItem("keepTheScore"));
      delete saves[this.saveName];
      localStorage.setItem("keepTheScore", JSON.stringify(saves));
      // update the saved
      this.saves = Object.keys(
        JSON.parse(localStorage.getItem("keepTheScore"))
      );
    },
    changeScore: function (player, score) {
      if (score == null) score = parseInt(player.newScore);
      player.score += score;
    },
  },
  mounted: function () {
    document.body.classList.add("bg-gray-800", "text-white")
    let saves = JSON.parse(localStorage.getItem("keepTheScore"));
    if (saves != null) this.saves = Object.keys(saves);
  },
};
</script>

<style>
</style>
