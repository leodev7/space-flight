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
.container
  max-width: 960px
  margin: 3rem auto 0
</style>