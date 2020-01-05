<template>
  <q-page padding>
    <div class="row q-pa-sm q-gutter-md">
      <div class="col">
        <q-input dense
          v-model="addDescription" 
          label="Add todo item"
          ref="focusAdd"
          @blur="clearAddTodo"
          @focus="focusAddTodo"
          @keyup.enter="addTodo" />
      </div>
      <q-btn round color="primary" icon="add" size="md"
        @click="addTodo"
        :disabled="!addDescription.trim()" />
    </div>
    <div class="row q-pa-sm">
      <div class="col">
        <q-list bordered>
          <q-item-label header>My Todo List</q-item-label>
          <q-scroll-area style="height: 30vh;">
            <q-item name="todo"
              v-for="(todo, index) in todos"
              :key=index>
              <q-item-section side top>
                <q-checkbox 
                  :value="todo.completed"
                  @click.native="toggle(index)" />
              </q-item-section>

              <q-item-section>
                <q-item-label>
                  <q-input dense
                    v-model="updateDescription" 
                    v-if="editIndex === index"
                    ref="focusEdit"
                    @keyup.enter="saveTodo(index)" />
                  <span 
                    v-else 
                    :class="{'text-strike' : todo.completed }">
                    {{todo.description}}
                  </span>
                </q-item-label>
                <q-item-label caption
                  v-if="editIndex !== index">
                  Created on {{todo.dateCreated.toLocaleDateString()}}
                </q-item-label>
              </q-item-section>

              <q-item-section side>
                <div class="row q-gutter-sm">
                  <q-btn round color="primary" icon="save" size="md" 
                    v-if="editIndex === index"
                    @click="saveTodo(index)"
                    :disable="!updateDescription.trim()"/>
                  <q-btn round color="primary" icon="edit" size="md" 
                    v-else 
                    @click="editTodo(index)"
                    :disable="todo.completed ? true : false" />
                  <q-btn round color="negative" icon="not_interested" size="md" 
                    v-if="editIndex === index" 
                    @click="cancelEdit(index)"/>
                  <q-btn round color="negative" icon="delete" size="md" 
                    v-else 
                    @click="deleteTodo(index)"/>
                  <q-btn round color="warning" icon="archive" size="md" 
                    @click="archiveTodo(index)"
                    :disable="editIndex === index"/>
                </div>
              </q-item-section>
            </q-item>
          </q-scroll-area>
        </q-list>
      </div>
    </div>
    <div class="row q-pa-sm">
      <div class="col">
        <q-list bordered>
          <q-item-label header>Archived List</q-item-label>
          <q-scroll-area style="height: 30vh;">
            <q-item name="archive"
              v-for="(archive, index) in archives"
              :key=index>
              <q-item-section side top>
                <q-checkbox 
                  v-model="archive.completed" 
                  :disable="archive.completed ? true : false"/>
              </q-item-section>

              <q-item-section>
                <q-item-label>
                  <span
                    :class="{'text-strike' : archive.completed }">
                    {{archive.description}}
                  </span>
                </q-item-label>
                <q-item-label caption>
                  Created on {{archive.dateCreated.toLocaleDateString()}}
                </q-item-label>
              </q-item-section>

              <q-item-section side>
                <div class="row q-gutter-sm">
                  <q-btn round color="primary" icon="unarchive" size="md" 
                    @click="unarchiveTodo(index)"/>
                </div>
              </q-item-section>
            </q-item>
          </q-scroll-area>
        </q-list>
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      addDescription: "",
      updateDescription: "",
      editIndex: -1,
      todos: [
        { description: "Fix interface", completed: false, dateCreated: new Date("12/11/2019") },
        { description: "Fix focus add", completed: false, dateCreated: new Date("12/11/2019") },
        { description: "Backend API", completed: false, dateCreated: new Date("12/11/2019") },
      ],
      archives: [
        { description: "Add archive", completed: true, dateCreated: new Date("12/09/2019") },
      ],
    }
  },
  methods: {
    addTodo() {
      let todo = {
        description: this.addDescription,
        completed: false,
        dateCreated: new Date(),
      }

      this.todos.unshift(todo);
      this.addDescription = "";
      this.editIndex = -1;

      this.$q.notify({
        message: "Successfully added",
        color: "secondary",
      });
    },
    editTodo(index, event) {
      this.updateDescription = this.todos[index].description;
      this.editIndex = index;
      this.focusOnActiveInput();
    },
    deleteTodo(index) {
      this.editIndex = -1;

      this.$q.dialog({
        title: 'Confirm',
        message: "Are you sure you want delete?",
        cancel: true,
      }).onOk(() => {
        this.todos.splice(index, 1);
      });
    },
    saveTodo(index) {
      this.todos[index].description = this.updateDescription.trim();
      this.updateDescription = "";
      this.editIndex = -1;

      this.$q.notify({
        message: "Successfully updated",
        color: "secondary",
      });
    },
    cancelEdit(index) {
      if (this.editIndex > -1)
      {
        this.updateDescription = "";
        this.editIndex = -1;
      }
    },
    archiveTodo(index) {
      let archive = this.todos.splice(index, 1);

      if (archive.length)
      {
        this.archives.unshift(archive[0]);
        this.editIndex = -1;
  
        this.$q.notify({
          message: "Successfully updated",
          color: "secondary",
        });
      }
    },
    unarchiveTodo(index) {
      let archive = this.archives.splice(index, 1);

      if (archive.length)
      {
        this.todos.push(archive[0]);
        this.editIndex = -1;

        this.$q.notify({
          message: "Successfully updated",
          color: "secondary",
        });
      }
    },
    toggle(index) {
      this.todos[index].completed = !this.todos[index].completed;
    },
    clearAddTodo() {
      setTimeout(() => {
        this.addDescription = "";
      }, 300);
    },
    focusAddTodo() {
      this.editIndex = -1;
    },
    focusOnActiveInput(mode) {
      this.$nextTick(() => {
        let index = this.$refs.focusEdit.length - 1;
        this.$refs.focusEdit[index].$el.focus();
      });
    }
  }
}
</script>
