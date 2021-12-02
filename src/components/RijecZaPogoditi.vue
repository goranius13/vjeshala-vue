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
        <button
          class="rijec"
          v-on:click="pogodiRijec()"
          :disabled="jeliKraj"
          :class="{ disabledButton: jeliKraj }"
        >
          Pogodi
        </button>
        <button @click="resetiraj()">Resetiraj</button>
      </div>
    </div>
    <h2>{{ pobjedaIliPoraz }}</h2>
  </div>
</template>

<script>
export default {
  name: "RijecZaPogoditi",
  props: {
    rijecZaPogoditi: Object,
    jeliKraj: Boolean,
    pobjedaIliPoraz: String,
    osvjeziKomponentu: Number,
  },
  data() {
    return {
      kompletnaRijecZaPogoditi: "",
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
  },
};
</script>

<style>
.slova {
  margin-top: 20px;
  margin-left: 10px;
  border: none;
  background-color: #1e95ea;
  color: white;
  height: 40px;
  width: 50px;
  font-size: 14px;
}

.rijec {
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
