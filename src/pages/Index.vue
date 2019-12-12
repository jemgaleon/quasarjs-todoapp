<template>
  <q-page padding>
    <div class="row q-pa-sm">
      <div class="col">
        <q-input dense
          v-model="addDescription" 
          label="Add todo item" />
      </div>
      <q-btn round color="primary" icon="add" size="md"
        @click="addTodo" />
    </div>
    <div class="row q-pa-sm">
      <div class="col">
        <q-list bordered>
          <q-item-label header>My Todo List</q-item-label>

          <q-item name="todo" v-ripple
            v-for="(todo, index) in todos"
            :key=index>
            <q-item-section side top>
              <q-checkbox 
                v-model="todo.completed" />
            </q-item-section>

            <q-item-section>
              <q-item-label>
                <q-input dense
                  v-model="updateDescription" 
                  v-if="todo.editing"
                  ref="focusEdit"
                  @blur="cancelEdit(index)"/>
                <span 
                  v-else 
                  :class="{'text-strike' : todo.completed }">
                  {{todo.description}}
                </span>
              </q-item-label>
              <q-item-label caption>
                Created on {{todo.dateCreated.toLocaleDateString()}}
              </q-item-label>
            </q-item-section>

            <q-item-section>
              <div class="row">
                <q-btn round color="primary" icon="save" size="md" 
                  v-if="todo.editing"
                  @click="saveTodo(index)"/>
                <q-btn round color="primary" icon="edit" size="md" 
                  v-else 
                  @click="editTodo(index)" 
                  :disable="todo.completed ? true : false" />
                <q-btn round color="negative" icon="not_interested" size="md" 
                  v-if="todo.editing" 
                  @click="cancelEdit(index)"/>
                <q-btn round color="negative" icon="delete" size="md" 
                  v-else 
                  @click="deleteTodo(index)"/>
                <q-btn round color="warning" icon="archive" size="md" 
                  @click="archiveTodo(index)"/>
              </div>
            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </div>
    <div class="row q-pa-sm">
      <div class="col">
        <q-list bordered>
          <q-item-label header>Archived List</q-item-label>

          <q-item name="archive" v-ripple
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

            <q-item-section>
              <div class="row">
                <q-btn round color="primary" icon="unarchive" size="md" 
                  @click="unarchiveTodo(index)"/>
              </div>
            </q-item-section>
          </q-item>
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
      todos: [
        { description: "Fix interface", completed: false, dateCreated: new Date(), editing: false },
      ],
      archives: [
        { description: "Add archive", completed: true, dateCreated: new Date(), editing: false },
      ],
    }
  },
  methods: {
    addTodo() {
      if (this.addDescription === '') {
        this.$q.notify({
          message: "Enter description.",
          color: "warning",
        });
      }
      else {
        let todo = {
          description: this.addDescription,
          completed: false,
          dateCreated: new Date(),
          editing: false,
        }

        this.todos.push(todo);
        this.addDescription = "";
        this.$q.notify({
          message: "Successfully added.",
          color: "secondary",
        });
      }
    },
    editTodo(index, event) {
      this.todos[index].editing = true;
      //this.$refs.focusEdit.$el.focus();
      console.log($refs);
    },
    deleteTodo(index) {
      this.$q.notify({
        message: "Are you sure you want delete?",
        color: "negative",
        actions: [
          { label: "Yes", handler: () => this.todos.splice(index, 1) },
          { label: "Cancel", handler: () => { /* Do nothing */ }},
        ],
      });
    },
    saveTodo(index) {
      if (this.addDescription === '') {
        this.$q.notify({
          message: "Enter description.",
          color: "warning",
        });
      }
      else {
        this.todos[index].description = this.updateDescription;
        this.todos[index].editing = false;
        this.updateDescription = "";

        this.$q.notify({
          message: "Successfully updated.",
          color: "secondary",
        });
      }
    },
    cancelEdit(index) {
      this.todos[index].editing = false;
      this.updateDescription = "";
    },
    archiveTodo(index) {
      let archive = this.todos.splice(index, 1);

      if (archive.length)
        this.archives.push(archive[0]);
    },
    unarchiveTodo(index) {
      let archive = this.archives.splice(index, 1);

      if (archive.length)
        this.todos.push(archive[0]);
    }
  }
}
</script>