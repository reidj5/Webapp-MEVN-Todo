<template>
  <div id="app">
    <div class="box">
      <h1 class="subtitle has-text-centered" id="title">To Do List</h1>
    </div>
    <div class="box">
      <hr />
      <div class="field has-addons">
        <div class="control is-expanded is-info">
          <input
            class="input"
            v-model="description"
            type="text"
            placeholder="What needs done?"
            @keyup.enter="addItem"
          />
        </div>
        <div class="control">
          <button class="button is-info" id="btn" @click="addItem" :disabled="!description" 
            >Add</button
          >
        </div>
      </div>
      <div class="notification" v-for="(item, i) in items" :key="item._id">
        <div class="columns">
          <input
            class="column input"
            v-if="isSelected(item)"
            v-model="editedDescription"
          />
          <p v-else class="column">
            <span class="tag is-info">{{ i + 1 }}</span>
            {{ item.description }}
          </p>
          <div class="column is-narrow">
            <span
              class="icon has-text-primary"
              @click="isSelected(item) ? unselect() : select(item)"
            >
              <i class="material-icons">{{
                isSelected(item) ? "close" : "edit"
              }}</i>
            </span>

            <span
              class="icon has-text-info"
              @click="
                isSelected(item) ? updateItem(item, i) : removeItem(item, i)
              "
            >
              <i class="material-icons">{{
                isSelected(item) ? "save" : "delete"
              }}</i>
            </span>
            <span class="icon" @click="completeItem(item, i)"
              >
              <i class="fas fa-check-double"></i>
            </span>
   
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      items: [],
      description: "",
      completed: true,
      editedDescription: "",
      selected: {},
    };
  },
  async mounted() {
    const response = await axios.get("api/bucketListItems/");
    this.items = response.data;
  },
  methods: {
    async addItem() {
      const response = await axios.post("api/bucketListItems/", {
        description: this.description,
        completed: false,
      });
      this.items.push(response.data);
      this.description = "";
    },
    async removeItem(item, i) {
      await axios.delete("api/bucketListItems/" + item._id);
      this.items.splice(i, 1);
    },
    select(item) {
      this.selected = item;
      this.editedDescription = item.description;
    },
    isSelected(item) {
      return item._id === this.selected._id;
    },
    unselect() {
      this.selected = {};
      this.editedDescription = "";
    },
    async updateItem(item, i) {
      const response = await axios.put("api/bucketListItems/" + item._id, {
        description: this.editedDescription,
      });
      this.items[i] = response.data;
      this.unselect();
    },
    async completeItem(item, i) {
      const response = await axios.put("api/bucketListItems/" + item._id, {
        completed: true,
      });
      this.item[i] = response.data;
      this.unselect();
    }
  },
};
</script>

<style>
#app {
  margin: auto;
  margin-top: 3rem;
  max-width: 700px;
}

.icon {
  cursor: pointer;
}

.box {
  background-color: rgb(194, 222, 250);
  box-shadow: 0 0.5em 1em -0.125em;
}

#title {
  color: white;
  text-shadow: 0 1px 0 #ccc, 0 2px 0 #c9c9c9, 0 3px 0 #bbb, 0 4px 0 #b9b9b9,
    0 5px 0 #aaa, 0 6px 1px rgba(0, 0, 0, 0.1), 0 0 5px rgba(0, 0, 0, 0.1),
    0 1px 3px rgba(0, 0, 0, 0.3), 0 3px 5px rgba(0, 0, 0, 0.2),
    0 5px 10px rgba(0, 0, 0, 0.25), 0 10px 10px rgba(0, 0, 0, 0.2),
    0 20px 20px rgba(0, 0, 0, 0.15);
}
</style>
