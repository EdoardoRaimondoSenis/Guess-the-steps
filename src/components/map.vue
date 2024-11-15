<script>
import mapboxgl from "mapbox-gl";
import * as turf from "@turf/turf";

export default {
  data() {
    return {
      map: null,
      punto1: null,
      punto2: null,
      nomePunto1: "",
      nomePunto2: "",
      passiStimati: null,
      distanzaMetri: null,
      distanzaKm: null,
      indovinatoPassi: null,
      messaggioIndovinato: "",
      markers: [],
      passiVisibili: false,  // Aggiunta per gestire la visibilità dei passi stimati
    };
  },
  mounted() {
    mapboxgl.accessToken = "pk.eyJ1IjoiemRpYWJvbGlrIiwiYSI6ImNtM2l4bzQ1czA0eWUya3M4aG1vejcxNzEifQ.H7xyUHDzeL5mbxV8WJDTDg";
    this.map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/streets-v11",
      center: [12.4964, 41.9028],
      zoom: 10,
    });

    this.map.on("click", (event) => {
      const coordinate = [event.lngLat.lng, event.lngLat.lat];
      if (!this.punto1) {
        this.punto1 = coordinate;
        this.aggiungiMarker(coordinate);
      } else if (!this.punto2) {
        this.punto2 = coordinate;
        this.aggiungiMarker(coordinate);
      } else {
        alert("Hai già selezionato due punti! Clicca su Reset per iniziare un nuovo gioco.");
      }
    });
  },
  methods: {
    aggiungiMarker(coordinate) {
      const marker = new mapboxgl.Marker({ draggable: false })
        .setLngLat(coordinate)
        .addTo(this.map);
      this.markers.push(marker);
    },
    calcolaPassi() {
      if (this.punto1 && this.punto2) {
        const puntoA = turf.point(this.punto1);
        const puntoB = turf.point(this.punto2);
        this.distanzaMetri = turf.distance(puntoA, puntoB, { units: "meters" }).toFixed(2);
        this.distanzaKm = (this.distanzaMetri / 1000).toFixed(2); // Distanza in chilometri

        const passoMedio = 0.75;
        this.passiStimati = Math.round(this.distanzaMetri / passoMedio);
        this.passiVisibili = true;  // Rendi visibili i passi solo dopo averli calcolati
      }
    },
    verificaIndovinato() {
      if (this.indovinatoPassi === this.passiStimati) {
        this.messaggioIndovinato = `Complimenti! Hai indovinato il numero di passi: ${this.passiStimati} passi.`;
      } else {
        this.messaggioIndovinato = `Peccato! La risposta corretta è: ${this.passiStimati} passi.`;
      }
    },
    resetGioco() {
      this.punto1 = null;
      this.punto2 = null;
      this.nomePunto1 = "";
      this.nomePunto2 = "";
      this.passiStimati = null;
      this.distanzaMetri = null;
      this.distanzaKm = null;
      this.indovinatoPassi = null;
      this.messaggioIndovinato = "";
      this.passiVisibili = false;  // Nascondi i passi quando resetti il gioco

      this.markers.forEach(marker => marker.remove());
      this.markers = [];
    },
  },
};
</script>

<template>
<div>
    <div id="map" style="height: 500px; width: 100%; margin-top: 10px;"></div>
</div>
<div>
    <label class="luogoUno">
    Luogo 1:
    <input v-model="nomePunto1" placeholder="Inserisci il nome del primo luogo" />
    <button @click="cercaLuogo(nomePunto1, 1)" :disabled="punto1 !== null">Trova Punto 1</button>
    </label>
    <label class="luogoDue">
    Luogo 2:
    <input v-model="nomePunto2" placeholder="Inserisci il nome del secondo luogo" />
    <button @click="cercaLuogo(nomePunto2, 2)" :disabled="punto2 !== null">Trova Punto 2</button>
    </label>
</div>
<div class="points">
    <div>
    <span>Punto 1: {{ punto1 ? `${nomePunto1} (${punto1.join(", ")})` : "Nessun punto selezionato" }}</span>
    <span>Punto 2: {{ punto2 ? `${nomePunto2} (${punto2.join(", ")})` : "Nessun punto selezionato" }}</span>
    </div>
    <button @click="calcolaPassi" :disabled="!punto1 || !punto2">Calcola Distanza</button>
    <button @click="resetGioco" style="margin-left: 10px;">Reset</button>

    <div v-if="passiVisibili">
        <label class="passi">
            Indovina quanti passi:
            <input type="number" v-model="indovinatoPassi" />
            <button @click="verificaIndovinato">Verifica</button>
        </label>
    </div>
    <p v-if="messaggioIndovinato">{{ messaggioIndovinato }}</p>
    <p v-if="distanzaMetri !== null">Distanza stimata in metri: {{ distanzaMetri }} m ({{ distanzaKm }} km)</p>
</div>
</template>

<style>
#map {
  position: relative;
  overflow: hidden;
}

.luogoUno input, .luogoDue input, .passi input {
    width: 20rem;
    height: 3rem;
    margin: 1rem 10px 1rem 0;
    padding: 0 1rem 0 1rem;
    border: none;
    border-radius: 10px;
}
.luogoUno button, .luogoDue button {
    margin-right: 2rem;
}

.points {
    div {
        margin: 1rem;
    }
    span {
        margin: 0 1rem; 
    }
}
</style>
