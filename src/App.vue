<template>
  <div class="app">
    <h1>Mon Bloc-Notes</h1>
    <h2>Vue 3 + Composition API + LocalStorage</h2>

    <div class="zone-ajout">
      <input
        v-model="nouvelleNote"
        placeholder="Écris une note..."
        @keyup.enter="ajouterNote"
      />
      <button @click="ajouterNote">Ajouter</button>
    </div>

    <p v-if="notes.length === 0" class="vide">Aucune note pour l'instant.</p>

    <transition-group name="fade" tag="ul" class="liste-notes">
      <NoteItem
        v-for="note in notes"
        :key="note.id"
        :note="note"
        @delete="supprimerNote"
        @edit="editerNote"
      />
    </transition-group>
  </div>
</template>

<script>
import { ref } from "vue";
import NoteItem from "./components/NoteItem.vue";

export default {
  components: { NoteItem },

  setup() {
    const nouvelleNote = ref("");
    const notes = ref([]);

    const savedNotes = localStorage.getItem("notes");
    if (savedNotes) {
      notes.value = JSON.parse(savedNotes);
    }

    function ajouterNote() {
      if (nouvelleNote.value.trim() === "") return;

      notes.value.push({
        id: Date.now(),
        texte: nouvelleNote.value,
      });

      nouvelleNote.value = "";
      localStorage.setItem("notes", JSON.stringify(notes.value));
    }

    function supprimerNote(id) {
      notes.value = notes.value.filter((note) => note.id !== id);
      localStorage.setItem("notes", JSON.stringify(notes.value));
    }
    function editerNote({ id, texte }) {
      const note = notes.value.find((note) => note.id === id);
      if (note) {
        note.texte = texte;
        localStorage.setItem("notes", JSON.stringify(notes.value));
      }
    }
    return { nouvelleNote, notes, ajouterNote, supprimerNote, editerNote };
  },
};
</script>

<style>
body {
  background: #0f0f0f;
  font-family: system-ui, sans-serif;
  margin: 0;
  padding: 0;
  color: #f2f2f2;
}

.app {
  max-width: 500px;
  margin: 50px auto;
  padding: 25px;
  background: #181818;
  border-radius: 18px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.5);
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

.zone-ajout {
  display: flex;
  gap: 8px;
}

.zone-ajout input {
  flex: 1;
  padding: 10px;
  border-radius: 10px;
  border: none;
  background: #101010;
  color: #fff;
}

.zone-ajout button {
  padding: 10px 16px;
  background: #22c55e;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  color: black;
  font-weight: bold;
  transition: 0.15s;
}

.zone-ajout button:hover {
  background: #16a34a;
}

.vide {
  margin-top: 20px;
  opacity: 0.5;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(10px);
}
.fade-enter-active {
  transition: 0.25s;
}
.fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
.fade-leave-active {
  transition: 0.25s;
}

.liste-notes {
  list-style: none;
  padding: 0;
  margin-top: 20px;
  display: grid;
  gap: 12px;
}
</style>
