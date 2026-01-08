<template>
  <form @submit.prevent="onSubmit" class="form">
    <div class="input-group" style="margin-bottom:10px">
      <label for="nome">Nome *</label>
      <input id="nome" v-model.trim="form.nome" type="text" placeholder="Ex: Maria Oliveira" />
      <span v-if="errors.nome" class="error">{{ errors.nome }}</span>
    </div>

    <div class="input-group" style="margin-bottom:10px">
      <label for="matricula">Matrícula *</label>
      <input id="matricula" v-model.trim="form.matricula" type="text" placeholder="Ex: 2023XXXX" />
      <span v-if="errors.matricula" class="error">{{ errors.matricula }}</span>
    </div>

    <div class="input-group" style="margin-bottom:10px">
      <label for="email">E-mail Institucional *</label>
      <input id="email" v-model.trim="form.email" type="email" placeholder="nome.sobrenome@alu.ufc.br" />
      <span v-if="errors.email" class="error">{{ errors.email }}</span>
    </div>

    <div class="input-group" style="margin-bottom:16px">
      <label for="curso">Curso (opcional)</label>
      <input id="curso" v-model.trim="form.curso" type="text" placeholder="Ex: Sistemas de Informação" />
    </div>

    <div class="form-actions">
      <button type="submit" class="btn btn-primary">{{ isEditing ? 'Salvar alterações' : 'Cadastrar' }}</button>
      <button v-if="isEditing" type="button" class="btn btn-outline" @click="$emit('cancelEdit')">Cancelar</button>
      <button type="button" class="btn btn-danger" @click="resetForm">Limpar</button>
    </div>
  </form>
</template>

<script setup>
import { reactive, watch, computed } from 'vue'

const props = defineProps({
  editingMember: {
    type: Object,
    default: null
  }
})
const emit = defineEmits(['save', 'cancelEdit'])

const form = reactive({
  nome: '',
  matricula: '',
  email: '',
  curso: ''
})

const errors = reactive({
  nome: '',
  matricula: '',
  email: ''
})

const isEditing = computed(() => !!props.editingMember)

watch(() => props.editingMember, (val) => {
  if (val) {
    form.nome = val.nome || ''
    form.matricula = val.matricula || ''
    form.email = val.email || ''
    form.curso = val.curso || ''
    clearErrors()
  } else {
    resetForm()
  }
})

function validate() {
  clearErrors()
  let ok = true

  if (!form.nome) {
    errors.nome = 'O nome é obrigatório.'
    ok = false
  } else if (form.nome.length < 3) {
    errors.nome = 'Informe ao menos 3 caracteres.'
    ok = false
  }

  if (!form.matricula) {
    errors.matricula = 'A matrícula é obrigatória.'
    ok = false
  }

  if (!form.email) {
    errors.email = 'O e-mail institucional é obrigatório.'
    ok = false
  } else if (!isInstitutionalEmail(form.email)) {
    errors.email = 'O email deve terminar com @alu.ufc.br.'
    ok = false
  }

  return ok
}

function isInstitutionalEmail(v) {
  return /^[^\s@]+@alu\.ufc\.br$/.test(v)
}

function onSubmit() {
  if (!validate()) return
  emit('save', { ...form })
  resetForm()
}

function resetForm() {
  form.nome = ''
  form.matricula = ''
  form.email = ''
  form.curso = ''
  clearErrors()
}

function clearErrors() {
  errors.nome = ''
  errors.matricula = ''
  errors.email = ''
}
</script>



