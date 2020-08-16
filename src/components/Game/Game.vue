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
        <button class="battleButton" v-show="!battle" @click="battle = !battle">Battle!</button>
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
                <div
                  :style="{display: 'flex', flexDirection: 'row', width: '100%', justifyContent: 'center', alignItems: 'center' }"
                >
                  <button
                    class="battleButton"
                    @click="attack(selectedPlayer.strength, selectedPlayer.speed, selectedPlayer.type)"
                    :disabled="playerHealth < 0 || foeHealth < 0"
                  >Hit</button>
                  <div v-if="selectedPlayer.name === 'Leonidas'">
                    <button class="battleButton" @click="handleParry">Parry / Counter</button>
                    <button
                      class="battleButton"
                      @click="handleLeonidas"
                      :disabled="special"
                    >Spartan Fire</button>
                  </div>
                  <div v-if="selectedPlayer.name === 'Fenix'">
                    <button
                      class="battleButton"
                      @click="handleFenix"
                      :disabled="playerHealth > 0"
                    >Revive</button>
                  </div>
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
                <button class="nextLevel" @click="handleLevel">Next Round</button>
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
      header: "1 on 1 Good vs Evil",
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
        let dmg = strength * speed * 1.2;
        const foeDmg =
          this.foes[this.foeIndx].strength * this.foes[this.foeIndx].speed * 4;
        const varDmg = Math.max(Math.floor(Math.random() * dmg) + 1, 100);
        const dmgTaken = Math.max(Math.floor(Math.random() * foeDmg) + 1, 50);
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
        const dmg = strength * speed * 1.2;
        const foeDmg =
          this.foes[this.foeIndx].strength * this.foes[this.foeIndx].speed * 4;
        const secondGo = speed * 5;
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
      if (this.foeHealth <= 0) {
        this.log.push(
          `${this.foes[this.foeIndx].name} is killed by ${
            this.selectedPlayer.name
          }`
        );
        return (this.foeHealth = 0);
      }
      if (this.playerHealth <= 0) {
        this.log.push(
          `${this.selectedPlayer.name} is killed by ${
            this.foes[this.foeIndx].name
          }.`
        );
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
      this.playerHealth = 1500;
    },
    handleLeonidas: function () {
      this.special = true;
      this.foeHealth = this.foeHealth - 1500;
      this.log.push(
        `${this.selectedPlayer.name} uses Spartan Fire for 1500 and stuns ${
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
        this.log.push(
          `${this.selectedPlayer.name} try to parry ${
            this.foes[this.foeIndx].name
          } attack but misses and recieves 780 dmg`
        );
        return (this.playerHealth -= 780);
      }
      this.playerHealth -= 50;
      this.special = false;
      this.log.push(
        `${this.selectedPlayer.name} parries ${
          this.foes[this.foeIndx].name
        } attack and gets a special attack bonus. Also gets 50 dmg taken`
      );
    },
  },
};
</script>
