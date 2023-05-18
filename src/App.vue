<template>
  <v-app class="app">
    <v-container>
      <v-text-field
        label="Filtrar Polemons"
        v-model="search"
        variant="solo-inverted"
      /><br />
      <v-row>
        <v-col cols="2" v-for="pokemon in pokemonFilter" :key="pokemon.name">
          <v-card @click="showPokemon(getId(pokemon))">
            <v-container>
              <v-row class="mx-0 d-flex justify-center">
                <img
                  :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${getId(
                    pokemon
                  )}.png`"
                  :alt="pokemon.name"
                  class="imgPokemon"
                />
              </v-row>
              <h2 class="text-center">{{ getName(pokemon) }}</h2>
            </v-container>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
    <div class="text-center">
      <v-dialog v-model="modalPokemonDetail" width="50%">
        <v-card v-if="selectedPokemon">
          <v-card-title class="text-h5"> Detalhes do pokemon </v-card-title>
          <v-card-text>
            <v-row class="d-flex justify-center">
              <v-col cols="4">
                <img
                  :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selectedPokemon.id}.png`"
                  :alt="selectedPokemon.name"
                  class="imgPokemonDialog"
                />
              </v-col>
              <v-col cols="8">
                <h1>
                  {{ getName(selectedPokemon) }}
                </h1>
                <v-chip
                  class="mr-2"
                  label
                  v-for="typePokemon in selectedPokemon.types"
                  :key="typePokemon.slot"
                >
                  Tipo: {{ typePokemon.type.name }}
                </v-chip>
                <v-divider class="my-4"> </v-divider>
                <v-chip label class="">
                  Altura: {{ selectedPokemon.height * 2.54 }}cm
                </v-chip>
                <v-chip label class="ml-2">
                  Peso:
                  {{ (selectedPokemon.weight * 0.453).toFixed(1) }}Kg
                </v-chip>
              </v-col>
            </v-row>
          </v-card-text>
          <v-card-actions>
            <v-btn color="primary" block @click="modalPokemonDetail = false"
              >Fechar</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data: () => ({
    pokemons: [],
    search: "",
    modalPokemonDetail: false,
    selectedPokemon: null,
  }),
  components: {},
  created() {
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=50").then((response) => {
      this.pokemons = response.data.results;
      console.log(response.data);
    });
  },
  methods: {
    getId(pokemon) {
      // a api retorna uma url nela tem um ID, aqui separamos o ID pra pegarmos a imagem do pokemon por ele
      return Number(pokemon.url.split("/")[6]);
    },
    //Função para transformar a primeira letra do nome do pokemon maiuscula
    getName(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    showPokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selectedPokemon = response.data;
      });
      this.modalPokemonDetail = true;
    },
  },
  computed: {
    //Filtro do input
    pokemonFilter() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search.toLowerCase());
      });
    },
  },
};
</script>
<style>
.app {
  background: linear-gradient(
      to bottom right,
      rgba(10, 10, 10, 1),
      rgba(12, 39, 63, 1)
    )
    no-repeat center center fixed !important;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
}

.imgPokemon {
  width: 45%;
}
.imgPokemonDialog {
  width: 80%;
}
</style>