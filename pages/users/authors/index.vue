<template>
  <div class="">
    <div :class="$style.header">
      <div :class="$style.title">
        Авторы
      </div>
      <div :class="$style.panel">
        <button @click.prevent="addAuthor" class="btn">
          Добавить
        </button>
      </div>
    </div>
    <TableDefault
    title="Разделы"
    :loading="loading"
    :tableOptions="tableOptions"
    @rowClick="editRow"
    @changePage="changePage"
    @searchInput="searchInput"
    @changeSort="changeSort"
    />
  </div>
</template>

<script>
import { ref, useContext, onMounted, useRouter } from '@nuxtjs/composition-api';


export default {
  name: "Users",
  components: {

  },
  setup() {
    const { store } = useContext()
    const router = useRouter()
    const columns = ref([
      {
        label: 'ID',
        field: 'id',
        type: 'text'
      },
      {
        label: 'Имя',
        field: 'name',
        type: 'text'
      },
      {
        label: 'Кол-во карточек',
        field: 'items_number',
        type: 'text'
      },
      {
        label: 'Кол-во лайков',
        field: 'like_number',
        type: 'text'
      }
    ])
    const tableOptions = ref({
      columns: columns.value,
      dataTable: [],
      paginationOptions: {
        enable: true
      },
      totalRows: null,
      perPage: 10
    })
    const paramsSearch = ref({
      searchField: '',
      page: 1,
      count: 10,
      order_by_column: '',
      order_by_mode: '',
    })
    const loading = ref(false)
    const sections = ref([])
    const editRow = (params) => {
      router.push({
        path: `/users/authors/${params.row.id}`,
        query: { title: params.row.title }
      })
    }
    const changePage = (newPage) => {
      paramsSearch.value.page = newPage
      getSections()
    }
    const getSections = async () => {
      loading.value = true
      const data = await store.dispatch('author/getAuthors', paramsSearch.value)
      sections.value = data
      tableOptions.value.dataTable = sections.value.data
      tableOptions.value.totalRows = sections.value.total
      loading.value = false
    }
    const searchInput = async (searchParams) => {
      loading.value = true
      paramsSearch.value.searchField = searchParams
      getSections()
    }
    const changeSort = async (params) => {
      console.log(params)
      paramsSearch.value.order_by_column = params[0].field
      if (paramsSearch.value.order_by_mode === '') {
        paramsSearch.value.order_by_mode = params[0].type
      } else if (paramsSearch.value.order_by_mode === 'asc') {
        paramsSearch.value.order_by_mode = 'desc'
      } else if (paramsSearch.value.order_by_mode === 'desc') {
        paramsSearch.value.order_by_mode = 'asc'
      }
      getSections()
    }
    const addAuthor = () => {
      router.push({
        path: '/users/authors/add'
      })
    }
    onMounted(() => {
      getSections()
    })

    return {
      columns,
      getSections,
      sections,
      tableOptions,
      loading,
      editRow,
      paramsSearch,
      changePage,
      searchInput,
      changeSort,
      addAuthor
    }
  }
}
</script>

<style lang="scss" module>
  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .title {
    font-size: 2.4rem;
    margin-bottom: 1rem;
    @media (max-width: 768px) {
      font-size: 1.8rem;
    }
  }
  .wrap {
    position: relative;
    flex-grow: 1;
  }
</style>