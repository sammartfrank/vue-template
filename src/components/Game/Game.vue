<template>
  <div class="gameSection">
    <h1>{{header}}</h1>
    <template v-if="playing">
      <template v-if="selectedPlayer">
        <div>
          <span
            :style="{color: 'red', fontSize: '3em', fontWeight: '900'}"
          >{{ selectedPlayer.name }}</span>
          <h5>has been Selected</h5>
          <hr />
        </div>
        <button v-show="!battle" @click="battle = !battle">Battle!</button>
        <template v-if="battle">
          <div class="battleContainer">
            <div class="heroBattle">
              <div class="healthBar">
                <div
                  :style="{width: playerHealth < 0 ? 0 : calcPercent(playerHealth) + '%', height: '30px', backgroundColor: 'green'}"
                >{{ playerHealth }} HP</div>
              </div>
              {{ selectedPlayer.name }}
              <div v-if="playerHealth <= 0">
                <h2>You died</h2>
              </div>
              <div>
                <hr />
                <div :style="{display: 'flex', justifyContent: 'center'}">
                  <div>
                    <button
                      @click="attack(selectedPlayer.strength, selectedPlayer.speed, selectedPlayer.type)"
                      :disabled="playerHealth < 0"
                    >Hit</button>
                  </div>
                  <div v-show="selectedPlayer.name === 'Fenix'">
                    <button @click="handleFenix" :disabled="playerHealth > 0">Revive</button>
                  </div>
                </div>
                <div v-show="selectedPlayer.name === 'Leonidas'">
                  <button @click="handleParry">Try to Parry and Counter</button>
                  <button @click="handleLeonidas" :disabled="special">Spartan Fire</button>
                </div>
              </div>
            </div>
            <span>VS</span>
            <div class="heroBattle">
              <div class="healthBar">
                <div
                  :style="{ width: foeHealth < 0 ? 0 : calcPercent(foeHealth) + '%',height: '30px', backgroundColor: 'green' }"
                >{{ foeHealth }} HP</div>
              </div>
              {{ foes[this.foeIndx].name }}
              <div v-if="foeHealth <= 0">
                <h2>{{this.foes[this.foeIndx].name}} Defeated!!</h2>
                <button @click="handleLevel">Next Round</button>
              </div>
            </div>
          </div>
        </template>
        <template>
          <div>
            <li :key="message" v-for="message in log">{{ message }}</li>
          </div>
        </template>
        <button @click="handleReset">Reset</button>
      </template>
      <template v-else>
        <h4>Choose a character</h4>
        <div class="charactersContainer">
          <div
            v-for="(values, key) in characters"
            :key="key"
            class="card"
            @click="handleSelectPlayer(values)"
          >
            <h1>{{values.name}}</h1>
            <p>Strength: {{ values.strength }} / 50</p>
            <p>Speed: {{ values.speed }} / 50</p>
            <p>Type: {{ values.type }}</p>
          </div>
        </div>
      </template>
    </template>
    <div v-else>
      <button
        @click="playing = !playing"
        :style="{padding: '10px', background: 'transparent', border: '1px solid #ccc', cursor: 'pointer' }"
      >Start Game</button>
    </div>
  </div>
</template>

<script>
import "../../styles/_game.css";
import { characters } from "./characters";
import { foes } from "./foes";

export default {
  name: "Game",
  props: {},
  data: function () {
    return {
      header: "Game Template",
      playing: false,
      battle: false,
      selectedPlayer: null,
      playerHealth: null,
      foeHealth: null,
      special: false,
      characters: characters,
      foes: foes,
      foeIndx: 0,
      log: [],
    };
  },
  computed: {},
  methods: {
    handleSelectPlayer: function (player) {
      this.selectedPlayer = player;
      this.playerHealth = player.hp;
      this.foeHealth = this.foes[this.foeIndx].hp;
    },
    attack: function (strength, speed, type) {
      if (type === "melee") {
        let dmg = (strength + speed) * speed * 2;
        const foeDmg =
          this.foes[this.foeIndx].strength * this.foes[this.foeIndx].speed * 5;
        const varDmg = Math.max(Math.floor(Math.random() * dmg) + 1);
        const dmgTaken = Math.max(Math.floor(Math.random() * foeDmg + 1));
        this.log.push(
          `${this.selectedPlayer.name} hits ${varDmg} true Damage to ${
            this.foes[this.foeIndx].name
          }`
        );
        this.log.push(
          `${this.foes[this.foeIndx].name} hits ${dmgTaken} to ${
            this.selectedPlayer.name
          } `
        );
        let newFoeHealth = this.foeHealth - varDmg;
        let newPlayerHealth = this.playerHealth - dmgTaken;
        this.foeHealth = newFoeHealth;
        this.playerHealth = newPlayerHealth;
      }
      if (type === "range") {
        const dmg = strength * speed;
        const foeDmg =
          this.foes[this.foeIndx].strength * this.foes[this.foeIndx].speed * 20;
        const secondGo = speed * 2;
        const varDmg = Math.max(Math.floor(Math.random() * dmg) + 1, 100);
        this.log.push(
          `${this.selectedPlayer.name} hits ${varDmg} true Damage to ${
            this.foes[this.foeIndx].name
          }. And gets a second Chance doing ${secondGo} dmg`
        );
        const dmgTaken = Math.max(Math.floor(Math.random() * foeDmg + 1, 5));
        this.log.push(
          `${this.foes[this.foeIndx].name} hits ${dmgTaken} to ${
            this.selectedPlayer.name
          } `
        );
        let newFoeHealth = this.foeHealth - varDmg - secondGo;
        let newPlayerHealth = this.playerHealth - dmgTaken;
        this.foeHealth = newFoeHealth;
        this.playerHealth = newPlayerHealth;
      }
    },
    calcPercent: function (param) {
      let total = this.selectedPlayer.hp;
      return parseInt((param * 100) / total, 10);
    },
    handleReset: function () {
      this.selectedPlayer = null;
      this.playing = false;
      this.battle = false;
      this.playerHealth = null;
      this.special = false;
      this.foeIndx = 0;
      this.log = [];
      this.foeHealth = this.foes[this.foeIndx].hp;
    },
    handleFenix: function () {
      this.playerHealth = 500;
    },
    handleLeonidas: function () {
      this.special = true;
      this.foeHealth = this.foeHealth - 1000;
      this.log.push(
        `${this.selectedPlayer.name} uses Spartan Fire for 1000hp and stuns ${
          this.foes[this.foeIndx].name
        }`
      );
    },
    handleLevel: function () {
      this.foeIndx += 1;
      this.foeHealth = this.foes[this.foeIndx].hp;
      this.playerHealth = this.selectedPlayer.hp;
      this.special = false;
      this.log = [];
    },
    handleParry: function () {
      let chance = Math.random();
      if (chance < 0.5) {
        return (this.playerHealth -= 360);
      }
      this.playerHealth -= 50;
      this.special = false;
    },
  },
};
</script>
