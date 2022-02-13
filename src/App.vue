<script>
export default {
  data() {
    return {
      player_health: 100,
      monster_health: 100,
      game_is_on: false,
      attack_multiple: 10,
      special_attack_multiple: 25,
      heal_up_multiple: 15,
      monster_attack_multiple: 25,
      logs: [],
    };
  },
  methods: {
    start_game() {
      this.game_is_on = true;
    },
    attack() {
      var point = Math.ceil(Math.random() * this.attack_multiple);
      this.monster_health -= point;
      this.add_to_logs({ turn: "p", text: `PLAYER ATTACK: ${point}` });
      this.monster_attack();
    },
    special_attack() {
      var point = Math.ceil(Math.random() * this.special_attack_multiple);
      this.monster_health -= point;
      this.add_to_logs({ turn: "p", text: `SPECIAL ATTACK: ${point}` });
      this.monster_attack();
    },
    heal_up() {
      var point = Math.ceil(Math.random() * this.heal_up_multiple);
      this.player_health += point;
      this.add_to_logs({ turn: "p", text: `HEAL UP ${point}` });
      this.monster_attack();
    },
    give_up() {
      this.player_health = 0;
    },
    monster_attack() {
      var point = Math.ceil(Math.random() * this.monster_attack_multiple);
      this.player_health -= point;
      this.add_to_logs({ turn: "m", text: `MONSTER ATTACK: ${point}` });
    },
    add_to_logs(log) {
      this.logs.push(log);
    },
  },
  computed: {
    player_progress() {
      return {
        width: this.player_health + "%",
      };
    },
    monster_progress() {
      return {
        width: this.monster_health + "%",
      };
    },
  },
  watch: {
    player_health(value) {
      if (value <= 0) {
        this.player_health = 0;
        if (confirm("You lost the game. Do you want to try again?")) {
          this.player_health = 100;
          this.monster_health = 100;
          this.logs = [];
        }
      } else if (value >= 100) {
        this.player_health = 100;
      }
    },
    monster_health(value) {
      if (value <= 0) {
        if (confirm("You won the game. Do you want to try again?")) {
          this.player_health = 100;
          this.monster_health = 100;
          this.logs = [];
        }
      }
    },
  },
};
</script>

<template>
  <div>
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <div class="healthbar">
          <div
            :style="player_progress"
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white"
          >
            {{ player_health }}%
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <div class="healthbar">
          <div
            :style="monster_progress"
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white"
          >
            {{ monster_health }}%
          </div>
        </div>
      </div>
    </section>
    <section class="row controls" v-if="!gama_is_on">
      <div class="small-12 columns">
        <button id="start-game" @click="start_game">NEW GAME</button>
      </div>
    </section>

    <section class="row controls" v-if="game_is_on">
      <div class="small-12 columns">
        <button id="attack" @click="attack">ATTACK</button>
        <button id="special-attack" @click="special_attack">
          SPECIAL ATTACK
        </button>
        <button id="heal" @click="heal_up">HEAL UP</button>
        <button id="give-up" @click="give_up">GIVE UP!</button>
      </div>
    </section>

    <section class="row log" v-if="logs.length > 0">
      <div class="small-12 columns">
        <ul>
          <li
            v-bind:key="log"
            :class="{
              'player-turn': log.turn === 'p',
              'monster-turn': log.turn === 'm',
            }"
            v-for="log in logs"
          >
            {{ log.text }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<style>
@import "./assets/foundation.min.css";
@import "./assets/app.css";
</style>
