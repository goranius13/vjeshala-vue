<template>
  <div>
    <h2>{{ rijecZaPogoditi.kategorija }}</h2>
    <button
      class="slova"
      v-for="(slovo, indeks) in rijecZaPogoditi.rijec"
      :key="indeks"
      :disabled="true"
      :class="{ disabledButton: !rijecZaPogoditi.odigrano[indeks] }"
    >
      {{ rijecZaPogoditi.odigrano[indeks] === true ? slovo : "_" }}
    </button>
    <div class="max-w-md mx-auto overflow-hidden md:max-w-5xl mt-4">
      <div class="md:flex">
        <input
          class="border-2 border-gray-300"
          type="text"
          name="rijec-za-pogoditi"
          placeholder="pogodite riječ"
          v-model="kompletnaRijecZaPogoditi"
        />
      </div>
      <div class="md:flex">
        <button class="wide-button" v-on:click="pogodiRijec()">Pogodi</button>
        <button class="wide-button" @click="resetiraj()">Resetiraj</button>
      </div>
    </div>
    <h2 class="mt-4">{{ rezultatIgre }}</h2>
  </div>
</template>

<script>
export default {
  name: "RijecZaPogoditi",
  props: {
    rijecZaPogoditi: Object,
    osvjeziKomponentu: Number,
  },
  data() {
    return {
      kompletnaRijecZaPogoditi: "",
      rezultatIgre: "",
    };
  },
  watch: {
    osvjeziKomponentu: function () {
      this.$forceUpdate();
    },
  },
  methods: {
    resetiraj() {
      this.kompletnaRijecZaPogoditi = "";
      this.$emit("resetiraj");
    },
    pogodiRijec() {
      this.$emit("pogodi-rijec", this.kompletnaRijecZaPogoditi);
    },
    prikaziRezultat(pobjeda) {
      this.rezultatIgre = pobjeda ? "Pobjeda!!!" : "Poraz :(";
    },
  },
};
</script>

<style>
.wide-button {
  width: 80px;
}
</style>
