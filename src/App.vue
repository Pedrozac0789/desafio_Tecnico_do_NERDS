<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import MemberForm from './components/MemberForm.vue'
import MemberList from './components/MemberList.vue'

const members = ref([])
const search = ref('')
const editingMember = ref(null)


onMounted(() => {
  const saved = localStorage.getItem('nerds-members')
  if (saved) {
    members.value = JSON.parse(saved)
  }
})


watch(members, (val) => {
  localStorage.setItem('nerds-members', JSON.stringify(val))
}, { deep: true })

const filteredMembers = computed(() => {
  const q = search.value.toLowerCase()
  if (!q) return members.value
  return members.value.filter(m =>
    m.nome.toLowerCase().includes(q) ||
    m.matricula.toLowerCase().includes(q) ||
    m.email.toLowerCase().includes(q)
  )
})

function handleSave(payload) {
  if (editingMember.value) {
    members.value = members.value.map(m =>
      m.id === editingMember.value.id ? { ...payload, id: m.id } : m
    )
    editingMember.value = null
  } else {
    members.value.unshift({ ...payload, id: crypto.randomUUID() })
  }
}

function startEdit(member) {
  editingMember.value = { ...member }
}

function cancelEdit() {
  editingMember.value = null
}

function removeMember(id) {
  members.value = members.value.filter(m => m.id !== id)
}
</script>

<template>
  <div class="container">
    <header class="header">
      <div>
        <img src="./assets/logo-NERDS.png" alt="Logo NERDS">
      </div>
      <div>
        <h1>Membros Do NERDS</h1>
        <p>Cadastro, listagem e busca de membros com validação.</p>
      </div>
      
    </header>

    <section class="grid">
      <div class="card">
        <MemberForm
          :editingMember="editingMember"
          @save="handleSave"
          @cancelEdit="cancelEdit"
        />
      </div>

      <div class="card">
        <div class="input-group" style="margin-bottom:12px">
          <label for="search">Buscar por nome, matrícula ou e-mail</label>
          <input
            id="search"
            type="text"
            v-model.trim="search"
            placeholder="Digite para filtrar..."
          />
        </div>

        <MemberList
          :members="filteredMembers"
          @edit="startEdit"
          @remove="removeMember"
        />
      </div>
   </section>
  </div>
</template>






