<template>
  <q-page padding>
   <q-form
   @submit="onSubmit"
   class="row q-col-gutter-sm"
 >
 <q-input
 outlined
 v-model="form.name"
 label="Nome"
 lazy-rules
 class="col-lg-6 col-xs-12"
 :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
/>
<q-input
 outlined
 v-model="form.job"
 label="Cargo"
 lazy-rules
 class="col-lg-6 col-xs-12"
 :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
/>
<q-input
 outlined
 v-model="form.salary"
 label="Salário"
 lazy-rules
 class="col-lg-6 col-xs-6"
 :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
/>
<div class="col-12 q-gutter-sm">
 <q-btn
 label="Salvar"
  color="primary"
  class="float-right"
  type="submit"/>

 <q-btn
 label="Cancelar"
 color="white"
 class="float-right"
 text-color="primary"
 :to="{name:'home'}"/>
</div>
</q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'

export default defineComponent({
  name: 'FormPost',
  setup () {
    const router = useRouter()
    const route = useRoute()
    const $q = useQuasar()
    const { post, getById, update } = postsService()
    const form = ref({
      name: '',
      job: '',
      salary: ''
    })

    onMounted(async () => {
      if (route.params.id) {
        getPost(route.params.id)
      }
    })

    const getPost = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
      } catch (error) {
        console.error(error)
      }
    }

    const onSubmit = async () => {
      if (form.value.id) {
        await update(form.value)
      } else {
        await post(form.value)
      }
      try {
        $q.notify({ message: 'Adicionado com sucesso', icon: 'check', color: 'positive' })
        router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
    }

    return {
      form,
      onSubmit
    }
  }
})
</script>
