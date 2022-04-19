<template>
  <q-page>

      <div class="container">

        <nav class="row">
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
        </nav>

        <section class="row">
          <div class="col-12">
            <div style="display: flex; flex-direction: column; align-items: center;">

              <q-img src="img/logo-nasa.jpg" width="200px" />
              <h5 class="q-my-none">Space Flight News</h5>

            </div>
          </div>
        </section>

        <q-separator color="grey" size="2px" class="q-my-xl" />

        <section class="row justify-center q-my-xl q-py-lg" v-for="(article, index) in articles" :key="article.id">
          <div class="col-8">

            <div class="flex q-col-gutter-xl" :style="index % 2 === 0 ? 'flex-direction: row' : 'flex-direction: row-reverse'">
              <div class="col" :style="index % 2 === 0 ? '' : 'text-align: right'">
                <q-img class="rounded-borders" :src="article.imageUrl" :ratio="1" />
              </div>

              <div class="col">
                <p style="font-size: 1rem; font-weight: bold; margin: 0">{{ article.title }}</p>
                <div class="flex justify-between">
                  <p>{{ dateFormated(article.publishedAt) }}</p>
                  <q-chip dense square color="deep-orange" text-color="white" style="margin-top: 0" :label="article.newsSite" />
                </div>
                <p style="letter-spacing: 0.1em;">{{ article.summary }}</p>
                <q-btn color="primary" label="Ver mais" />
              </div>
            </div>

            <q-separator style="background: #D07017" size="2px" class="q-mt-xl" />

          </div>
        </section>

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

      articles: []
    }
  },

  mounted () {
    this.onLoadCurrentFilter()
  },

  methods: {

    onLoadCurrentFilter ()  {
      this.$axios({ method: 'get', url: 'https://api.spaceflightnewsapi.net/v3/articles' })
        .then((response) => {
          this.articles = response.data
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