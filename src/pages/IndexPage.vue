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

        <q-separator class="q-my-xl" />

      </div>

  </q-page>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      text: '',
      sortSelected: 'Sort'
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

</style>