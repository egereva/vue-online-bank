<template>
  <app-loader v-if="loading"/>
  <app-page v-else title="Список заявок">
    <template #header>
      <button class="btn primary" @click="modal = true">Создать</button>
    </template>

    <request-filter v-model="filter"></request-filter>

    <request-table :requests="requests"></request-table>

    <teleport to="body">
      <app-modal v-if="modal" title="Создать заявку" @close="modal = false">
        <requrst-modal @created="modal = false"></requrst-modal>
      </app-modal>
    </teleport>
  </app-page>
</template>

<script>
import {ref, computed, onMounted, watch} from 'vue'
import AppPage from '@/components/ui/AppPage'
import RequestTable from '@/components/request/RequestTable'
import AppModal from '@/components/ui/AppModal'
import RequrstModal from '@/components/request/RequrstModal'
import {useStore} from 'vuex'
import AppLoader from '@/components/ui/AppLoader'
import RequestFilter from '@/components/request/RequestFilter'
export default {
  setup() {
    const store = useStore()
    const modal = ref(false)
    const loading = ref(false)
    const filter = ref({})

    watch(filter, val => console.log(val))

    onMounted(async () => {
      loading.value = true
      await store.dispatch('request/load')
      loading.value = false
    })

    const requests = computed(() => store.getters['request/requests']
        .filter(request => {
          if (filter.value.name) {
            return  request.fio.includes(filter.value.name)
          }
          return request
        })
        .filter(request => {
          if (filter.value.status) {
            return filter.value.status === request.status
          }
          return request
        })
    )

    return {
      modal,
      requests,
      loading,
      filter
    }
  },
  components: {RequestFilter, AppLoader, RequrstModal, AppModal, RequestTable, AppPage}
}
</script>
