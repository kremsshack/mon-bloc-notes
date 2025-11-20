<template>
  <li
    class="note-item"
    tabindex="0"
    @keydown.delete="supprimer"
    @dblclick="activerEdition"
  >
    <template v-if="enEdition">
      <input
        v-model="texteTemp"
        @keyup.enter="validerEdition"
        @blur="validerEdition"
        class="edit-input"
        autofocus
      />
    </template>

    <template v-else>
      <span>{{ note.texte }}</span>
      <button @click="supprimer">Supprimer</button>
    </template>
  </li>
</template>

<script>
import { ref } from "vue";

export default {
  props: { note: Object },
  emits: ["delete", "edit"],

  setup(props, { emit }) {
    const enEdition = ref(false);
    const texteTemp = ref("");

    function activerEdition() {
      enEdition.value = true;
      texteTemp.value = props.note.texte;
    }

    function validerEdition() {
      if (texteTemp.value.trim() === "") {
        enEdition.value = false;
        return;
      }
      emit("edit", { id: props.note.id, texte: texteTemp.value });
      enEdition.value = false;
    }

    function supprimer() {
      emit("delete", props.note.id);
    }

    return { enEdition, texteTemp, activerEdition, validerEdition, supprimer };
  },
};
</script>

<style scoped>
.note-item {
  background: #202020;
  padding: 14px 16px;
  border-radius: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: 1px solid #333;
  transition: 0.2s;
}

.note-item:hover {
  background: #2a2a2a;
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
}

button {
  background: #ef4444;
  border: none;
  color: white;
  padding: 6px 10px;
  border-radius: 8px;
  cursor: pointer;
  transition: 0.2s;
}

button:hover {
  background: #dc2626;
  cursor: pointer;
}

.edit-input {
  width: 100%;
  padding: 10px;
  border-radius: 10px;
  border: none;
  background: #101010;
  color: white;
}
</style>
