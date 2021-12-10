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
  data() {
    return {
      abeceda: [],
    };
  },
  methods: {
    odigranPotez(indeks) {
      this.abeceda[indeks].odigrano = true;
      this.$emit("odigran-potez", this.abeceda[indeks].slovo);
    },
    onemoguciAbecedu() {
      for (let i = 0; i < this.abeceda.length; i++) {
        this.abeceda[i].odigrano = true;
      }
    },
    resetirajStanjeAbecede() {
      for (let i = 0; i < this.abeceda.length; i++) {
        this.abeceda[i].odigrano = false;
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
