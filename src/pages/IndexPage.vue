<template>
  <q-page>

    <div class="container">

      <q-table class="no-shadow no-border" hide-pagination row-key="id" :rows="articlesData" :columns="articlesColumns" :rows-per-page-options="[10]" @request="onLoadCurrentFilter" >

        <template v-slot:top>
          <div class="col-12">
            <div class="flex justify-end q-col-gutter-sm">

              <q-input dense outlined v-model="text" label="Search">
                <template v-slot:append>
                  <q-icon v-if="text !== ''" name="close" @click="text = ''" class="cursor-pointer" />
                  <q-icon name="search" class="cursor-pointer" />
                </template>
              </q-input>

              <q-select item-aligned dense outlined v-model="sortSelected" :options="['Mais recente', 'Menos recente']" />
            </div>
          </div>

          <div class="col-12">
            <div class="flex column flex-center">
              <q-img src="img/logo-nasa.jpg" width="200px" />
              <h5 class="q-my-none">Space Flight News</h5>
            </div>

            <q-separator color="grey" size="2px" class="q-mt-xl" />
          </div>
        </template>

        <template v-slot:body="props">
          <q-tr class="row justify-center q-my-xl q-pa-lg" :props="props" :style="props.rowIndex % 2 === 0 ? 'background: #FFEBEE' : ''">
            <div class="flex q-col-gutter-xl" :style="props.rowIndex % 2 === 0 ? 'flex-direction: row' : 'flex-direction: row-reverse'">
              <div class="col" :style="props.rowIndex % 2 === 0 ? '' : 'text-align: right'">
                <q-img class="rounded-borders" :src="props.row.imageUrl" :ratio="4/3">
                  <template v-slot:error>
                    <div class="absolute-full flex flex-center bg-negative text-white">
                      Erro no servidor da imagem
                    </div>
                  </template>
                </q-img>
              </div>

              <div class="col">
                <p style="font-size: 1rem; font-weight: bold; margin: 0">{{ props.row.title }}</p>
                <div class="flex justify-between">
                  <p>{{ dateFormated(props.row.publishedAt) }}</p>
                  <q-chip dense square color="deep-orange" text-color="white" style="margin-top: 0" :label="props.row.newsSite" />
                </div>
                <p style="letter-spacing: 0.1em;">{{ props.row.summary }}</p>
                <q-btn color="primary" label="Ver mais" />
              </div>
            </div>
          </q-tr>
        </template>

        <template v-slot:no-data>
          <div class="row full-width">
            <div class="q-mt-xs q-px-xs col-12">
              <p>Nenhum registro encontrado</p>
            </div>
          </div>
        </template>

      </q-table>

    </div>

  </q-page>
</template>

<script>
import { date } from 'quasar'

export default {
  name: 'IndexPage',
  data () {
    return {
      text: '',
      sortSelected: 'Sort',

      articlesColumns:[],
      articlesData: []
    }
  },

  mounted () {
    this.onLoadCurrentFilter()
  },

  methods: {

    onLoadCurrentFilter () {
      this.$axios({ method: 'get', url: 'https://api.spaceflightnewsapi.net/v3/articles' })
        .then((response) => {
          this.articlesData = response.data
        })
        .catch((err) => {
          console.log(err)
        })
    },

    dateFormated (value) {
      return date.formatDate(value, 'DD/MM/YYYY - HH:mm:ss')
    }
  }
}
</script>

<style lang="sass">

body
  font-family: 'Roboto Condensed'
  color: #1E2022

.container
  max-width: 960px
  margin: 3rem auto 0

</style>