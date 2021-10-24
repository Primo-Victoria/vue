<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>Заметки</h1>
        <hr />
        <br /><br />
        <alert :message="message" v-if="showMessage"></alert>
        <button type="button" class="btn btn-success btn-sm" v-b-modal.note-modal>
          Добавить заметку
        </button>
        <br /><br />
        <table class="table table-hover">
          <thead>
            <tr>
              <th scope="col">Название</th>
              <th scope="col">Заметка</th>
              <th scope="col">Дата</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(note, index) in notes" :key="index">
              <td>{{ note.title }}</td>
              <td>{{ note.body }}</td>
              <td>{{ note.createTime }}</td>
              <td>
                <button
                  type="button"
                  class="btn btn-warning btn-sm"
                  v-b-modal.note-update-modal
                  @click="editNote(note)">>
                  Изменить
                </button>
                <button type="button" class="btn btn-danger btn-sm">
                  Удалить
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <b-modal
      ref="addNoteModal"
      id="note-modal"
      title="Добавьте новую заметку"
      hide-footer
    >
      <b-form @submit="onSubmit" @reset="onReset" class="w-100">


        <b-form-group
          id="form-title-group"
          label="Заголовок:"
          label-for="form-title-input"
        >

          <b-form-input
            id="form-title-input"
            type="text"
            v-model="addNoteForm.title"
            required
            placeholder="Введите заголовок"
          >
          </b-form-input>
        </b-form-group>



        <b-form-group
          id="form-body-group"
          label="Текст:"
          label-for="form-body-input"
        >

          <b-form-input
            id="form-body-input"
            type="text"
            v-model="addNoteForm.body"
            required
            placeholder="Введите текст"
          >
          </b-form-input>
        </b-form-group>


        <b-button type="submit" variant="primary">Сохранить</b-button>
        <b-button type="reset" variant="danger">Сбросить</b-button>
      </b-form>
    </b-modal>




    <b-modal ref="editNoteModal"
         id="note-update-modal"
         title="Изменить"
         hide-footer>


  <b-form @submit="onSubmitUpdate" @reset="onResetUpdate" class="w-100">
  <b-form-group id="form-title-edit-group"
                label="Заголовок:"
                label-for="form-title-edit-input">
      <b-form-input id="form-title-edit-input"
                    type="text"
                    v-model="editForm.title"
                    required
                    placeholder="Введите заголовок">
      </b-form-input>
    </b-form-group>


    <b-form-group id="form-body-edit-group"
                  label="Текст:"
                  label-for="form-body-edit-input">
        <b-form-input id="form-body-edit-input"
                      type="text"
                      v-model="editForm.body"
                      required
                      placeholder="Введите текст">
        </b-form-input>
    </b-form-group>
    <b-button type="submit" variant="primary">Изменить</b-button>
    <b-button type="reset" variant="danger">Отмена</b-button>
  </b-form>

    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import Alert from "../componets/Alert";


export default {
  components: { Alert },
  data() {
    return {
      notes: [],
      addNoteForm: {
        title: "",
        body: "",
        createTime: "",
      },
      message: "",
      showMessage: false,
      editForm: {
        id: "",
        title: "",
        body: "",
      },
    };
  },

  componets: {
    alert: Alert,
  },

  methods: {
    editNote(note) {
      this.editForm = note;
    },
    getNotes() {
      const path = "http://localhost:63508/api/Notes";
      axios
        .get(path)
        .then((res) => {
          this.notes = res.data;
        })
        .catch((error) => {
          // eslint-отключение следующей строки
          console.error(error);
        });
    },
    addNote(payload) {
      const path = "http://localhost:63508/api/Notes";
      axios
        .post(path, payload)
        .then(() => {
          console.log("POST запрос получился");
          this.showMessageBox("Заметка добавлена");
        })
        .catch((error) => {
          // eslint-отключение следующей строки
          console.log(error);
        });
    },
    onResetUpdate(evt) {
    evt.preventDefault();
    this.$refs.editNoteModal.hide();
    this.initForm();
    this.getNotes();
    },
    updateNote(payload, noteID) {
    const path = `http://localhost:63508/api/Notes/${noteID}`;
    const headers = {
      "Content-Type" : "application/json",
    };
    axios.put(path, payload, {headers})
      .then(() => {
        this.getNotes();
        this.showMessageBox("Заметка обновлена");
      })
      .catch((error) => {
      // eslint-отключение следующей строки
      console.error(error);
      this.getNotes();
    });
},
    onSubmitUpdate(evt) {
    evt.preventDefault();
    this.$refs.editBookModal.hide();

    const payload = {
      title: this.editForm.title,
      body: this.editForm.body,
    read,
  };
  this.updateBook(payload, this.editForm.id);
},
    initForm() {
      this.addNoteForm.title = "";
      this.addNoteForm.body = "";
      this.addNoteForm.createTime = "";
    },
    onSubmit(evt) {
      evt.preventDefault();
      this.$refs.addNoteModal.hide();
      const payload = {
        title: this.addNoteForm.title,
        body: this.addNoteForm.body,
      };
      this.addNote(payload);
      this.initForm();
    },
    onReset(evt) {
      evt.preventDefault();
      this.$refs.addNoteModal.hide();
      this.initForm();
    },
    showMessageBox(msg) {
      this.message = msg;
      this.showMessage = true;
    },
  },
  created() {
    this.getNotes();
  },
};
</script>