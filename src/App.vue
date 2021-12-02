<template>
  <div id="app">
    <div class="max-w-md mx-auto overflow-hidden md:max-w-4xl">
      <div class="md:flex">
        <SlikaVjesala :brojSlike="vjesaloSlika" />

        <div class="p-4">
          <RijecZaPogoditi
            :rijecZaPogoditi="rijecZaPogoditi"
            :jeliKraj="jeliKraj"
            :pobjedaIliPoraz="pobjedaIliPoraz"
            :osvjeziKomponentu="osvjeziKomponentu"
            v-on:pogodi-rijec="pogodiRijec($event)"
            v-on:resetiraj="resetiraj()"
          />
        </div>
      </div>
    </div>

    <div class="max-w-md mx-auto overflow-hidden md:max-w-4xl">
      <Abeceda
        :resetirajAbecedu="resetirajAbecedu"
        :jeliKraj="jeliKraj"
        v-on:odigran-potez="pogodiSlovo($event)"
      />
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
    vjesaloSlika: function () {
      if (this.vjesaloSlika === 12) {
        this.pobjedaIliPoraz = "Poraz :(";
        this.rijecZaPogoditi.rijec.forEach((element) => {
          element.jeliSlovoOdigrano = true;
        });
        this.jeliKraj = true;
      }
    },
  },
  methods: {
    resetiraj() {
      this.pripremiRijecZaIgru();
      Object.assign(this.$data, inicijalnoStanje(this.resetirajAbecedu));
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
          this.pobjeda();
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
        this.pobjeda();
      } else {
        this.vjesaloSlika += 1;
      }
    },
    pobjeda() {
      this.pobjedaIliPoraz = "Pobjeda!!!";
      this.jeliKraj = true;
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

function inicijalnoStanje(stanje) {
  return {
    vjesaloSlika: 0,
    rijecZaPogoditi: {
      rijec: [],
      odigrano: [],
      rijecZaUsporedbu: "",
      kategorija: "",
    },
    pobjedaIliPoraz: "",
    jeliKraj: false,
    resetirajAbecedu: !stanje,
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
</style>
