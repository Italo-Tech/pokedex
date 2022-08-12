<template>
  <q-page class="flex items-start justify-center q-pa-md">
    <div class="column flex-center">
      <div class="column flex-center q-mb-xl text-grey-8">
        <span class="text-h5 q-pt-lg q-pb-md">Bem vindo ao Pokedex</span>
        <span class="text-subtitle1">Descubra novos pokemons pesquisando por seu nome</span>
      </div>

      <section>
        <q-card flat style="width: 300px;">
          <div v-if="pokemon.url" class="q-p-lg">
            <q-img :src="pokemon.url" fit="cover"/>
          </div>
          <q-img class="q-pa-lg" v-else src="https://assets.pokemon.com/assets/cms2/img/pokedex/full/038.png" fit="cover"/>
        </q-card>

        <div v-if="pokemon.id">
          <!--Id-->
          <q-card-actions>
            <span class="text-subtitle2 text-grey-7">Nº {{ pokemon.id }}</span>
          </q-card-actions>
          <!--Name-->
          <q-card-actions>
            <span class="text-h5 text-grey-10 text-capitalize">{{ pokemon.name }}</span>
          </q-card-actions>
          <!--Badge-->
          <q-card-actions class="q-gutter-sm">
            <q-badge v-for="what_type in pokemon.type" color="grey-7" :label="what_type.type.name" class="q-pa-sm text-capitalize"/>
          </q-card-actions>
        </div>
      </section>

      <div class="row justify-center full-width q-mt-xl">
        <q-select
          filled
          v-model="pokemonSearch"
          class=""
          input-debounce="0"
          color="secondary"
          label="Selecione um pokemon"
          label-color="secondary"
          transition-show="jump-up"
          transition-hide="jump-up"
          :options="pokemonOptions"
          clearable
          style="width: 250px"
          behavior="menu"
        >
          <template v-slot:no-option>
            <q-item>
              <q-item-section class="text-grey">
                Sem resultados
              </q-item-section>
            </q-item>
          </template>
        </q-select>
        <q-btn :disable="!pokemonSearch" color="secondary" label="pesquisar" @click="getPokemon"/>
      </div>
    </div>
  </q-page>
</template>

<script>
import api from "../services/api"
import { ref } from 'vue'
import { useQuasar } from "quasar";

export default {
  setup() {
    const $q = useQuasar()

    const pokemon = ref({
      id: null,
      name: null,
      type: [],
      url: null,
    })
    const pokemonOptions = ref([
      'golem', 'My dog', 'gyarados', 'snorlax',
      'steelix', 'hariyama', 'charizard',
      'aggron', 'wailord', 'glalie', 'metagross',
      'regirock', 'kyogre', 'groudon', 'torterra',
      'rhyperior', 'mamoswine', 'dialga', 'palkia',
      'heatran', 'spearow', 'raticate', 'rattata',
      'pidgeot', 'pidgeotto', 'pidgey', 'beedrill'
    ])
    const pokemonSearch = ref(null)
    const options = (pokemonOptions)

    const getPokemon = () => {
      api.get(`/pokemon/${pokemonSearch.value}/`)
        .then((response) => {
          pokemon.value.id = response.data.id
          pokemon.value.name = response.data.name
          pokemon.value.type = response.data.types
          pokemon.value.url = response.data.sprites.other.dream_world.front_default
          triggerPositive()
        })
        .catch((error) => {
          triggerNegative()
        })
    }
    const triggerPositive = () => {
      $q.notify({
        message: 'Pokemon encontrado!',
        color: 'positive',
        progress: true,
        avatar: 'https://img.quizur.com/f/img5e5545da203841.94955565.png?lastEdited=1582646751',
        actions: [
          { label: 'Fechar', color: 'white', handler: () => { /* ... */ } }
        ]
      })
    }
    const triggerNegative = () => {
      $q.notify({
        message: 'Pokémon não encontrado, tente novamente!',
        progress: true,
        color: 'negative',
        avatar: 'http://pa1.narvii.com/6431/79d57176492e24604caff0845a06d7f90b5e6d3f_00.gif',
        actions: [
          { label: 'Fechar', color: 'white', handler: () => { /* ... */ } }
        ]
      })
    }

    return {
      pokemon,
      pokemonSearch,
      pokemonOptions,
      options,

      triggerPositive,
      triggerNegative,
      getPokemon
    }
  }
}
</script>
