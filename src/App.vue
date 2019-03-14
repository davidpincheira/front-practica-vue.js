<template>
  <div id="app">
    <div class="hero is-while is-gradient is-bold">
      <div class="hero-body">
        <h1 class="title"><span class="subtitle">Personajes</span></h1>

        <div class="field had-addons is-pulled-right">
          <div class="control">
            <input
              v-model="search"
              type="text"
              class="is-rounded"
              v-on:keyup.enter="searchData();"
            />
          </div>

          <div class="control">
            <button
              v-on:click="searchData"
              class="button is-success is-rounded"
            >
              buscar
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div
        class="columns is-desktop is-mobile is-tablet is-multiline is-centered"
      >
        <character
          @showModal="showModal"
          v-for="character of characters"
          :key="character.id"
          :character="character"
        />
      </div>
      <nav class="navigation" role="navegation" aria-label="pagination">
        <a class="pagination-previous" v-on:click="changePage(page - 1);"
          >Anterior</a
        >
        <a class="pagination-link is-current">{{ page }}</a>
        <a class="pagination-next" v-on:click="changePage(page + 1);"
          >Siguiente</a
        >
      </nav>
    </div>

    <div class="modal" :class="{ 'is-active': modal }" v-if="modal">
      <div class="modal-background" @click="modal = false;"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Acerca de: {{ currentCharacter.name }}</p>
        </header>

        <div class="modal-card-body">
          <p>Genero</p>
          <p>{{ currentCharacter.gender }}</p>
          <p>Status</p>
          <p>{{ currentCharacter.status }}</p>
          <p>Especie</p>
          <p>{{ currentCharacter.statspeciesus }}</p>
          <p>Tipo</p>
          <p>{{ currentCharacter.type }}</p>
        </div>

        <div class="footer modal-card-foot">
          <button class="button" @click="modal = false;">Cerrar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Character from "./components/Character";

export default {
  name: "App",
  components: {
    Character
  },
  data: function() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      currentCharacter: {}
    };
  },
  created() {},
  methods: {
    fetch() {
      const params = {
        page: this.page,
        name: this.search
      };
      let result = axios
        .get("https://rickandmortyapi.com/api/character/", { params })
        .then(res => {
          console.log(res.data.info);
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
        })
        .catch(err => {
          console.log(err);
        });
    },
    //metodo para cambiar la pagina
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.fetch();
    },
    searchData() {
      this.page = 1;
      this.fetch();
    },
    showModal(id) {
      this.fetchOne(id);
    },
    async fetchOne(id) {
      let result = await axios.get(
        `https://rickandmortyapi.com/api/character/${id}/`
      );
      this.currentCharacter = result.data;
      this.modal = true;
    }
  }
};
</script>
