<template>
  <div class="listNotes">
    <ul>
      <li v-for="(row, index) in notes" :key="index">
        <button class="btn-note" @click="editNote(row.id)">
          <label>{{row.title}}</label>
          <span>{{row.description}}</span>
        </button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "ListNotes",
  data() {
    return {
      notes: []
    };
  },
  methods: {
    editNote(id) {
      let dataForm = this.notes.find(note => note.id === id);
      dataForm.mode = "update";
      this.$root.$emit("emitForm", dataForm);
    },
    getdata() {
      axios
        .get("http://localhost/wegodev-notes/note")
        .then(res => {
          this.notes = res.data;
        })
        .catch(err => {
          console.error(err);
        });
    }
  },
  mounted() {
    this.getdata();
    this.$root.$on("emitRemoveNote", data => {
      let noteIndex = this.notes.findIndex(note => note.id === data.id);
      this.notes.splice(noteIndex, 1);
    });
    this.$root.$on("emitUpdateNote", data => {
      let noteIndex = this.notes.findIndex(note => note.id === data.id);
      this.notes[noteIndex].title = data.title;
      this.notes[noteIndex].description = data.description;
    });
    this.$root.$on("emitSubmitNote", data => {
      this.notes.unshift({
        id: data.id,
        title: data.title,
        description: data.description
      });
      this.editNote(data.id);
    });
  }
};
</script>

<style>
ul {
  list-style-type: none;
  padding: 0;
  margin: 0px;
}

.btn-note {
  text-align: left;
  border: none;
  border-bottom: 1px solid gainsboro;
  padding: 0px 15px;
  cursor: pointer;
  outline: none;
  background: #f7f7f7;
  height: 150px;
  width: 100%;
}

.btn-note:hover {
  background: #eaeaea;
}

.btn-note label {
  display: block;
  color: #444444;
  font-size: 1.5em;
  margin-bottom: 15px;
}

.btn-note span {
  color: #545454;
}
</style>