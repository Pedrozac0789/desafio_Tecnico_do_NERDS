<template>
  <div>
    <div class="table-container">
      <table class="table">
        <thead>
          <tr>
            <th>Nome</th>
            <th>Matrícula</th>
            <th>E-mail</th>
            <th>Curso</th>
            <th style="width: 160px">Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-if="!members.length">
            <td colspan="5" style="text-align: center; color: var(--muted)">
              Nenhum membro encontrado.
            </td>
          </tr>
          <tr v-for="m in members" :key="m.id">
            <td>
              <span class="badge">{{ m.nome }}</span>
            </td>
            <td>{{ m.matricula }}</td>
            <td>{{ m.email }}</td>
            <td>{{ m.curso || "—" }}</td>
            <td>
              <div style="display: flex; gap: 8px">
                <button class="btn btn-outline" @click="$emit('edit', m)">
                  <i class="bx bx-edit"></i>
                </button>
                <button class="btn btn-danger" @click="$emit('remove', m.id)">
                  <i class="bx bx-trash"></i>
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
defineProps({
  members: {
    type: Array,
    default: () => [],
  },
});
defineEmits(["edit", "remove"]);
</script>
