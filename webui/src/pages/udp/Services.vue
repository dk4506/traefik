<template>
  <page-default>

    <section class="app-section">
      <div class="app-section-wrap app-boxed app-boxed-xl q-pl-md q-pr-md q-pt-xl q-pb-xl">
        <div class="row no-wrap items-center q-mb-lg">
          <tool-bar-table :status.sync="status" :filter.sync="filter"/>
        </div>
        <div class="row items-center q-col-gutter-lg">
          <div class="col-12">
            <main-table
              ref="mainTable"
              v-bind="getTableProps({ type: 'udp-services' })"
              :data="allServices.items"
              :onLoadMore="handleLoadMore"
              :endReached="allServices.endReached"
              :loading="allServices.loading"
              :currentSort.sync="sortBy"
              :currentSortDir.sync="sortDir"
            />
          </div>
        </div>
      </div>
    </section>

  </page-default>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
import GetTablePropsMixin from '../../_mixins/GetTableProps'
import PaginationMixin from '../../_mixins/Pagination'
import PageDefault from '../../components/_commons/PageDefault'
import ToolBarTable from '../../components/_commons/ToolBarTable'
import MainTable from '../../components/_commons/MainTable'

export default {
  name: 'PageUDPServices',
  mixins: [
    GetTablePropsMixin,
    PaginationMixin({
      fetchMethod: 'getAllServicesWithParams',
      scrollerRef: 'mainTable.$refs.scroller',
      pollingIntervalTime: 5000
    })
  ],
  components: {
    PageDefault,
    ToolBarTable,
    MainTable
  },
  data () {
    return {
      filter: '',
      status: '',
      sortBy: 'name',
      sortDir: 'asc'
    }
  },
  computed: {
    ...mapGetters('udp', { allServices: 'allServices' })
  },
  methods: {
    ...mapActions('udp', { getAllServices: 'getAllServices' }),
    getAllServicesWithParams (params) {
      return this.getAllServices({
        query: this.filter,
        status: this.status,
        sortBy: this.sortBy,
        direction: this.sortDir,
        ...params
      })
    },
    refreshAll () {
      if (this.allServices.loading) {
        return
      }

      this.initFetch()
    },
    handleLoadMore ({ page = 1 } = {}) {
      return this.fetchMore({ page })
    }
  },
  watch: {
    'status' () {
      this.refreshAll()
    },
    'filter' () {
      this.refreshAll()
    },
    'sortBy' () {
      this.refreshAll()
    },
    'sortDir' () {
      this.refreshAll()
    }
  },
  beforeDestroy () {
    this.$store.commit('udp/getAllServicesClear')
  }
}
</script>

<style scoped lang="scss">

</style>
