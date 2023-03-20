<template>
  <q-page padding>
    <q-table
      title="Funcionários"
      :rows="funcionarios"
      :columns="columns"
      row-key="name">
      <template v-slot:top>
        <span class="text-h5">Artigos</span>
        <q-space/>
        <q-btn color="primary" label="Novo" :to="{name:'formPost'}" />
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm">
          <q-btn icon="edit" color="info" dense size="sm" @click="handleEditFuncionario(props.row.id)"/>
          <q-btn
          icon="delete" color="negative" dense size="sm" @click="handleDeleteFuncionario(props.row.id)"/>
        </q-td>
      </template>
    </q-table>

  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const router = useRouter()
    const funcionarios = ref([])
    const { list, remove } = postsService()
    const columns = [

      { name: 'id', field: 'id', label: 'id', sortable: true, align: 'left' },
      { name: 'name', field: 'name', label: 'Nome', sortable: true, align: 'left' },
      { name: 'job', field: 'job', label: 'Cargo', sortable: true, align: 'left' },
      { name: 'salary', field: 'salary', label: 'Salário', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'Ações', align: 'right' }

    ]
    const $q = useQuasar()
    onMounted(() => {
      getFuncionarios()
    })

    const getFuncionarios = async () => {
      try {
        const data = await list()
        funcionarios.value = data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeleteFuncionario = async (id) => {
      try {
        $q.dialog({
          title: 'Remover',
          message: 'Deseja deletar?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'Apagado com sucesso', icon: 'check', color: 'positive' })
          await getFuncionarios()
        })
      } catch (error) {
        $q.notify({ message: 'Erro ao apagar', icon: 'times', color: 'negative' })
      }
    }

    const handleEditFuncionario = (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      funcionarios,
      columns,
      handleDeleteFuncionario,
      handleEditFuncionario
    }
  }
})
</script>
