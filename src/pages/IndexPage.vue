<template>
  <q-page>

    <div class="container">

      <q-dialog v-model="modal">
        <q-card>
          <q-card-section class="row items-center q-pa-none">
            <q-space />
            <q-btn icon="close" flat round dense v-close-popup />
          </q-card-section>

          <q-card-section>
            <div class="row">
              <div class="col-sm-5 col-xs-12">
                <q-img class="rounded-borders" :src="articleModal.imageUrl" :ratio="1">
                  <template v-slot:error>
                    <q-img src="img/error-image.jpeg" />
                  </template>
                </q-img>
              </div>

              <q-space class="q-mx-sm" />

              <div class="col-sm-6 col-xs-12">
                <p class="titleText">{{ articleModal.title }}</p>
                <div class="flex justify-between">
                  <p>{{ dateFormated(articleModal.publishedAt) }}</p>
                  <q-chip dense square color="deep-orange" text-color="white" style="margin-top: 0" :label="articleModal.newsSite" />
                </div>
                <p class="sumaryText">{{ articleModal.summary }}</p>
              </div>
            </div>
          </q-card-section>

          <q-card-section>
            <div class="col-12">
              <div class="flex justify-end q-gutter-sm">
                <q-btn color="dark" label="Voltar" v-close-popup />
                <q-btn color="primary" label="Ir para o site" @click="toWebsite(articleModal.url)" />
              </div>
            </div>
          </q-card-section>
        </q-card>
      </q-dialog>

      <q-table class="no-shadow no-border" :filter="filter" :filter-method="filterData" :sort-method="customSort" binary-state-sort hide-pagination row-key="id" :rows="articlesData" :columns="articlesColumns" :rows-per-page-options="[0]" @request="onLoadCurrentFilter" >

        <template v-slot:top>
          <div class="col-12">
            <div class="flex justify-end q-col-gutter-sm">

              <q-input dense outlined color="black" v-model="filter.title" label="Search">
                <template v-slot:append>
                  <q-icon v-if="filter.title !== ''" name="close" @click="filter.title = ''" class="cursor-pointer" />
                </template>
              </q-input>

              <q-select dense outlined v-model="sortSelected" :options="['desc', 'Menos recente']" map-options />
            </div>
          </div>

          <div class="col-12">
            <div class="flex column flex-center">
              <q-img src="img/logo-nasa.jpg" width="200px" />
              <h5 class="q-my-none">Space Flight News</h5>
            </div>

            <q-separator style="background: #302E53" size="2px" class="q-mt-xl" />
          </div>
        </template>

        <template v-slot:body="props">
          <q-tr class="row q-mb-xl q-pa-lg" :props="props" :style="props.rowIndex % 2 === 0 ? 'background: #ECEFF1' : ''">
            <div class="row" :style="props.rowIndex % 2 === 0 ? 'flex-direction: row' : 'flex-direction: row-reverse'">
              <div class="col-md-5 col-xs-12" :style="props.rowIndex % 2 === 0 ? '' : 'text-align: right'">
                <q-img class="rounded-borders" :src="props.row.imageUrl" style="min-width: 380px" :ratio="1">
                  <template v-slot:error>
                    <q-img src="img/error-image.jpeg" />
                  </template>
                </q-img>
              </div>
              <q-space />

              <div class="col-md-6 col-xs-12">
                <p class="titleText">{{ props.row.title }}</p>
                <div class="flex justify-between">
                  <p>{{ dateFormated(props.row.publishedAt) }}</p>
                  <q-chip dense square color="deep-orange" text-color="white" style="margin-top: 0" :label="props.row.newsSite" />
                </div>
                <p class="sumaryText">{{ props.row.summary }}</p>
                <q-btn color="primary" label="Ver mais" @click="showInfoModal(props.row)" />
              </div>
            </div>
          </q-tr>
        </template>

        <template v-slot:bottom>
          <div class="col-12 q-my-md">
            <div class="flex justify-center">
              <q-btn color="primary" label="Carregar mais" @click="loadMore()" />
            </div>
          </div>
        </template>

        <template v-slot:no-data>
          <div class="row full-width">
            <div class="q-mt-xs q-px-xs col-12">
              <p>Nenhum registro encontrado</p>
            </div>
          </div>
        </template>

      </q-table>

      <q-page-scroller position="bottom-right" :scroll-offset="150" :offset="[18, 18]">
        <q-btn fab icon="keyboard_arrow_up" color="indigo" />
      </q-page-scroller>

    </div>

  </q-page>
</template>

<script>
import { date } from 'quasar'

export default {
  name: 'IndexPage',
  data () {
    return {
      filter: {
        title: ''
      },
      sortSelected: '',
      count: 10,
      modal: false,
      articleModal: [],

      articlesColumns:[
        {
          sortable: true
        }
      ],
      articlesData: [],

      customSort (rows, sortBy, descending) {
        console.log(rows)
        console.log(sortBy)
        console.log(descending)
        const data = [...rows]

        if (sortBy) {
          data.sort((a, b) => {
            const x = descending ? b : a
            const y = descending ? a : b

            if (sortBy === 'name') {
              // string sort
              return x[ sortBy ] > y[ sortBy ] ? 1 : x[ sortBy ] < y[ sortBy ] ? -1 : 0
            }
            else {
              // numeric sort
              return parseFloat(x[ sortBy ]) - parseFloat(y[ sortBy ])
            }
          })
        }

        return data
      }
    }
  },

  mounted () {
    this.onLoadCurrentFilter()
  },

  methods: {

    onLoadCurrentFilter () {
      this.$q.loading.show()

      this.$axios({ method: 'get', url: `https://api.spaceflightnewsapi.net/v3/articles?_limit=${this.count}` })
        .then((response) => {
          this.articlesData = response.data
        })
        .catch((err) => {
          console.log(err)
        })
        .finally(() => {
          this.$q.loading.hide()
        })

    },

    dateFormated (value) {
      return date.formatDate(value, 'DD/MM/YYYY - HH:mm:ss')
    },

    filterData(rows, terms) {
      for (const term in terms) { 
        rows = rows.filter(row =>
          (row[term] + '').toLowerCase().includes(terms[term].toLowerCase())
        )
      }
      return rows
    },

    loadMore () {
      this.count += 10
      this.onLoadCurrentFilter()
    },

    showInfoModal (data) {
      this.articleModal = data
      this.modal = true
    },

    toWebsite (url) {
      window.open(url, '_blank')
    }
  }
}
</script>

<style lang="sass">

body
  font-family: 'Roboto Condensed'

.container
  max-width: 960px
  margin: 3rem auto 0

.titleText
  font-size: 1rem
  font-weight: bold
  margin: 0

.sumaryText
  letter-spacing: 0.1em

</style>