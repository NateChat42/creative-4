<template>
<div>
  <h1>Manage Your Characters' Quests</h1>
  <div id="characters">
    <button :style="{backgroundColor: character.color }" :class="{ selected: active(character)}" v-for="character in characters" :key=character.id @click=selectCharacter(character)>{{character.name}}</button>
  </div>
  <div class="todoQuests" v-if="character">
    <p v-show="activeQuests.length === 0">You are done with all your quests! Good job!</p>
    <form @submit.prevent="addQuest">
      <input type="text" v-model="title">
      <button type="submit">Add a Quest</button>
    </form>
    <div class="controls">
      <button @click="showAll()">Show All</button>
      <button @click="showActive()">Show Active</button>
      <button @click="showCompleted()">Show Completed</button>
      <button @click="deleteCompleted()">Delete Completed</button>
    </div>
    <ul>
      <li v-for="quest in filteredQuests" :key="quest.id">
        <label :class="{ quest: true, completed: quest.completed }">
          {{ quest.title }}
          <!-- {{ quest.description }}
          {{ quest.reward }} -->
          <input type="checkbox" v-model="quest.completed" @click="completeQuest(quest)" />
          <span class="checkmark"></span>
        </label>
        <button @click="deleteQuest(quest)" class="delete">X</button>
      </li>
    </ul>
  </div>
</div>
</template>

<script>
import axios from 'axios';
// import { Swatches } from 'vue-color'
export default {
  name: 'Home',
  components: {
    // 'swatches-picker': Swatches
  },
  data() {
    return {
      characters: [],
      character: null,
      color: "#0F0",
      quests: [],
      title: '',
      description: '',
      reward: '',
      show: 'all',
    }
  },
  created() {
    this.getCharacters();
  },
  computed: {
    activeQuests() {
      return this.quests.filter(quest => {
        return !quest.completed;
      });
    },
    filteredQuests() {
      if (this.show === 'active')
        return this.quests.filter(quest => {
          return !quest.completed;
        });
      if (this.show === 'completed')
        return this.quests.filter(quest => {
          return quest.completed;
        });
      return this.quests;
    },
  },
  methods: {
    async getCharacters() {
      try {
        const response = await axios.get("/api/characters");
        console.log(response);
        this.characters = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    selectCharacter(character) {
      this.character = character;
      this.getQuests();
    },
    async getQuests() {
      try {
        const response = await axios.get(`/api/characters/${this.character._id}/quests`);
        this.quests = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    async addQuest() {
      try {
        await axios.post(`/api/characters/${this.character._id}/quests`, {
          title: this.title,
          description: this.description,
          reward: this.reward,
          completed: false
        });
        this.text = "";
        this.getQuests();
      } catch (error) {
        console.log(error);
      }
    },
    async completeQuest(quest) {
      try {
        axios.put(`/api/characters/${this.character._id}/quests/${quest._id}`, {
          title: this.title,
          description: this.description,
          reward: this.reward,
          completed: !quest.completed,
        });
        this.getQuests();
      } catch (error) {
        console.log(error);
      }
    },
    async deleteQuest(quest) {
      try {
        await axios.delete(`/api/characters/${this.character._id}/quests/${quest._id}`);
        this.getQuests();
      } catch (error) {
        console.log(error);
      }
    },
    showAll() {
      this.show = 'all';
    },
    showActive() {
      this.show = 'active';
    },
    showCompleted() {
      this.show = 'completed';
    },
    deleteCompleted() {
      this.quests.forEach(quest => {
        if (quest.completed)
          this.deleteQuest(quest);
      });
    },
    active(character) {
      return (this.character && character._id === this.character._id);
    },
    /*darkColor(color) {
      return (this.lightOrDark(color) === 'dark');
    },
    lightOrDark(color) {
      let hex = color;
      if (typeof color === 'object' && color !== null)
        hex = color.hex;

    // Convert it to RGB: http://gist.github.com/983661
      let rgb = +("0x" + hex.slice(1).replace( 
      hex.length < 5 && /./g, '$&$&'));

      const r = rgb >> 16;
      const g = rgb >> 8 & 255;
      const b = rgb & 255;
      
      // HSP (Highly Sensitive Poo) equation from http://alienryderflex.com/hsp.html
      const hsp = Math.sqrt(
      0.299 * (r * r) +
      0.587 * (g * g) +
      0.114 * (b * b)
      );

      // Using the HSP value, determine whether the color is light or dark
      if (hsp>127.5) {

          return 'light';
      } 
      else {

          return 'dark';
      }
    }*/
  }
}
</script>

<style scoped>
/* List */
ul {
  list-style: none;
}

li {
  background: #fff;
  width: 500px;
  min-height: 40px;
  padding: 10px;
  margin-bottom: 10px;
  font-size: 1em;
  display: flex;
  align-items: center;
}

.delete {
  display: none;
  margin-left: auto;
}

li:hover .delete {
  display: block;
}

label {
  width: 400px;
}

.completed {
  text-decoration: line-through;
}

/* Form */
input[type=checkbox] {
  transform: scale(1.5);
  margin-right: 10px;
}

input[type=text] {
  font-size: 1em;
}

.vc-swatches {
  margin-bottom: 20px;
}

#characters {
  margin-bottom: 20px;
}



button {
  font-family: 'Arvo';
  font-size: 1em;
  border: none;
  background-color: #bbb;
  padding: 5px 10px;
}

button:focus {
  outline: none;
}

button.selected {
  border-bottom: 2px solid #000;
}

.white {
  color: #fff;
}

/* Controls */
.controls {
  margin-top: 20px;
}

/* Custom checkbox
/* Customize the label (the container) */
.quest {
  display: block;
  position: relative;
  padding-left: 35px;
  margin-bottom: 12px;
  cursor: pointer;
  font-size: 22px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

/* Hide the browser's default checkbox */
.quest input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 25px;
  width: 25px;
  background-color: #eee;
}

/* On mouse-over, add a grey background color */
.quest:hover input~.checkmark {
  background-color: #ccc;
}

/* When the checkbox is checked, add a blue background */
.quest input:checked~.checkmark {
  background-color: #2196F3;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.quest input:checked~.checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.quest .checkmark:after {
  left: 9px;
  top: 5px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}
</style>
