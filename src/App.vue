<template>
  <div id="app">
    <div class="max-w-md mx-auto overflow-hidden md:max-w-4xl">
      <div class="md:flex">
        <SlikaVjesala :brojSlike="vjesaloSlika" />

        <div class="p-4">
          <RijecZaPogoditi
            ref="rijecZaPogoditi"
            :rijecZaPogoditi="rijecZaPogoditi"
            :osvjeziKomponentu="osvjeziKomponentu"
            v-on:pogodi-rijec="pogodiRijec($event)"
            v-on:resetiraj="resetiraj()"
          />
        </div>
      </div>
    </div>

    <div class="max-w-md mx-auto overflow-hidden md:max-w-4xl">
      <Abeceda ref="abeceda" v-on:odigran-potez="pogodiSlovo($event)" />
    </div>
  </div>
</template>

<script>
import SlikaVjesala from "./components/SlikaVjesala.vue";
import RijecZaPogoditi from "./components/RijecZaPogoditi.vue";
import Abeceda from "./components/Abeceda.vue";
import axios from "axios";

export default {
  name: "App",
  components: {
    SlikaVjesala,
    RijecZaPogoditi,
    Abeceda,
  },
  data: function () {
    return inicijalnoStanje();
  },
  watch: {
    vjesaloSlika: function (newValue) {
      if (newValue === 12) {
        this.krajIgreIPobjeda(false);
      }
    },
  },
  methods: {
    resetiraj() {
      this.pripremiRijecZaIgru();
      Object.assign(this.$data, inicijalnoStanje());
      this.$refs.abeceda.resetirajStanjeAbecede();
    },
    pogodiSlovo(slovo) {
      if (
        this.rijecZaPogoditi.rijec.find((slovoRijeci) => slovoRijeci === slovo)
      ) {
        this.rijecZaPogoditi.rijec.forEach((slovoRijeci, indeks) => {
          if (slovoRijeci === slovo) {
            this.rijecZaPogoditi.odigrano[indeks] = true;
          }
        });
        if (
          this.rijecZaPogoditi.odigrano.find(
            (odigrano) => odigrano === false
          ) === undefined
        ) {
          this.krajIgreIPobjeda(true);
        }
      } else {
        this.vjesaloSlika += 1;
      }
      this.osvjeziKomponentu++;
    },
    pogodiRijec(rijec) {
      if (
        rijec.trim().toUpperCase() === this.rijecZaPogoditi.rijecZaUsporedbu
      ) {
        this.krajIgreIPobjeda(true);
      } else {
        this.vjesaloSlika += 1;
      }
    },
    krajIgreIPobjeda(istina) {
      this.$refs.abeceda.onemoguciAbecedu();
      this.$refs.rijecZaPogoditi.prikaziRezultat(istina);
      this.rijecZaPogoditi.odigrano.forEach((odigraniElement, indeks) => {
        if (!odigraniElement) this.rijecZaPogoditi.odigrano[indeks] = true;
      });
    },
    pripremiRijecZaIgru() {
      const regExZaDuplaSlova = /^LJ|^NJ|^DŽ/;

      axios
        .get(
          "http://localhost:3000/katalogRijeci/" +
            Math.floor(Math.random() * 99)
        )
        .then((response) => {
          this.rijecZaPogoditi.rijecZaUsporedbu = response.data.rijec;
          this.rijecZaPogoditi.kategorija = response.data.kategorija;
          while (response.data.rijec.length) {
            let brojZnakovaZaSlovo = regExZaDuplaSlova.test(response.data.rijec)
              ? 2
              : 1;
            this.rijecZaPogoditi.rijec.push(
              response.data.rijec.slice(0, brojZnakovaZaSlovo)
            );

            this.rijecZaPogoditi.odigrano.push(false);

            response.data.rijec = response.data.rijec.slice(brojZnakovaZaSlovo);
          }
        });
    },
  },
  mounted() {
    this.pripremiRijecZaIgru();
  },
};

function inicijalnoStanje() {
  return {
    vjesaloSlika: 0,
    rijecZaPogoditi: {
      rijec: [],
      odigrano: [],
      rijecZaUsporedbu: "",
      kategorija: "",
    },
    osvjeziKomponentu: 0,
  };
}
</script>

<style>
#app {
  font-family: Tahoma, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #282828;
  margin: 20px;
}

button {
  margin-top: 20px;
  margin-left: 10px;
  border: none;
  background-color: #1e95ea;
  color: white;
  height: 40px;
  width: 40px;
  border-radius: 20px;
  font-size: 14px;
}

.disabledButton {
  background-color: #d8d8d8;
}
</style>
