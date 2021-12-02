<template>
  <div>
    <button
      v-for="(objekt, indeks) in abeceda"
      :key="indeks"
      v-on:click="odigranPotez(indeks)"
      :disabled="objekt.odigrano"
      :class="{ disabledButton: objekt.odigrano }"
    >
      {{ objekt.slovo }}
    </button>
  </div>
</template>
<script>
import axios from "axios";

export default {
  name: "Abeceda",
  props: {
    jeliKraj: Boolean,
    resetirajAbecedu: Boolean,
  },
  data() {
    return {
      abeceda: [],
    };
  },
  watch: {
    jeliKraj: function () {
      this.postaviStanjeAbecede(true);
    },
    resetirajAbecedu: function () {
      this.postaviStanjeAbecede(false);
    },
  },
  methods: {
    odigranPotez(indeks) {
      this.abeceda[indeks].odigrano = true;
      this.$emit("odigran-potez", this.abeceda[indeks].slovo);
    },
    postaviStanjeAbecede(stanje) {
      for (let i = 0; i < this.abeceda.length; i++) {
        this.abeceda[i].odigrano = stanje;
      }
    },
  },
  mounted() {
    axios.get("http://localhost:3000/abeceda/").then((response) => {
      this.abeceda = response.data;
    });
  },
};
</script>

<style>
button {
  margin-top: 20px;
  margin-left: 10px;
  border: none;
  background-color: #1e95ea;
  color: white;
  height: 40px;
  width: 70px;
  font-size: 14px;
}

.disabledButton {
  background-color: #d8d8d8;
}
</style>
