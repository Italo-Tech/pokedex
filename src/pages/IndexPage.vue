<template>
  <q-page class="flex items-start justify-center q-pa-md">
    <div class="column flex-center">
      <div class="column flex-center q-mb-xl">
        <span class="text-h5 q-pt-lg q-pb-md">Bem vindo ao Pokedex</span>
        <span class="text-subtitle1">Descubra novos pokemons pesquisando por seu nome</span>
      </div>

      <section>
        <q-card flat style="width: 300px;">
          <div v-if="pokemonUrl" class="q-p-lg">
            <q-img :src="pokemonUrl" fit="cover"/>
          </div>
          <q-img class="q-pa-lg" v-else src="https://assets.pokemon.com/assets/cms2/img/pokedex/full/038.png" fit="cover"/>
        </q-card>

        <div v-if="pokemonId" class="full-width">
          <div class="">
            <span class="text-subtitle2 text-grey-7">Nº {{ pokemonId }}</span>
          </div>
          <div class="full-width q-mt-sm column">
            <span class="text-h5 text-grey-10 text-capitalize">{{ pokemonName }}</span>
          </div>
          <div class="q-gutter-sm q-mt-xs">
            <q-badge v-for="type in pokemonType" color="grey-7" :label="type.type.name" class="q-pa-sm text-capitalize"/>
          </div>
        </div>
      </section>

      <div class="row justify-center full-width q-mt-xl">
        <!--<q-input filled v-model="pokemonSearch" label="Digite nome pokemon" />
        <q-btn color="primary" label="pesquisar" @click="getPokemon"/>-->

        <q-select
          filled
          v-model="pokemonSearch"
          use-input
          input-debounce="0"
          label="Selecione um pokemon"
          :options="pokemonOptions"
          @filter="filterFn"
          style="width: 250px"
          behavior="menu"
        >
          <template v-slot:no-option>
            <q-item>
              <q-item-section class="text-grey">
                No results
              </q-item-section>
            </q-item>
          </template>
        </q-select>
        <q-btn color="primary" label="pesquisar" @click="getPokemon"/>
      </div>
    </div>
  </q-page>
</template>

<script>
import api from "../services/api"
import { ref, onBeforeMount } from 'vue'
import { useQuasar } from "quasar";

export default {
  setup() {
    const $q = useQuasar()

    const pokemonId = ref('')
    const pokemonName = ref('')
    const pokemonType = ref([])
    const pokemonUrl = ref('')/*https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/25.svg*/
    const pokemonSearch = ref('')/*bulbasaur*/

    const pokemonOptions = ref(['bulbasaur', 'ivysaur', 'venusaur', 'charmander', 'charmeleon', 'charizard', 'squirtle', 'wartortle', 'blastoise', 'caterpie', 'metapod', 'butterfree', 'weedle', 'beedrill', 'kakuna'])
    const pokemonOptions2 = []
    const options = ref(pokemonOptions)

    onBeforeMount(() => {
      getAllPokemon()
    })

    const getPokemon = () => {
      api.get(`/pokemon/${pokemonSearch.value}/`)
        .then((response) => {
          console.log(response);
          console.log(response.data.types);
          pokemonId.value = response.data.id
          pokemonName.value = response.data.name
          pokemonType.value = response.data.types
          pokemonUrl.value = response.data.sprites.other.dream_world.front_default
          triggerPositive()
        })
        .catch((error) => {
          triggerNegative()
        })
    }
    const getAllPokemon = () => {
      api.get('/pokemon?limit=100000&offset=0')
        .then((response) => {
          console.log(response.data.results);
          pokemonOptions2.value = response.data.results
          console.log(pokemonOptions2);
        })
    }
    const triggerPositive = () => {
      $q.notify({
        type: 'positive',
        position: 'top',
        timeout: 1000,
        message: 'Pokemon encontrado'
      })
    }
    const triggerNegative = () => {
      $q.notify({
        type: 'warning',
        timeout: 1000,
        position: 'top',
        message: 'Pokemon não encontrado, tente novamente'
      })
    }


    return {
      pokemonSearch,
      pokemonName,
      pokemonUrl,
      pokemonId,
      pokemonType,
      triggerPositive,
      triggerNegative,
      getPokemon,
      getAllPokemon,

      pokemonOptions,
      pokemonOptions2,
      options,

      filterFn (val, update) {
        if (val === '') {
          update(() => {
            options.value = pokemonOptions.value
          })
          return
        }

        update(() => {
          const needle = val.toLowerCase()
          options.value = pokemonOptions.value.filter(v => v.toLowerCase().indexOf(needle) > -1)
        })
      }
    }
  }
}
</script>
