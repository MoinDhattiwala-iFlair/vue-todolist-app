<template>
  <v-list-item>
    <v-list-item-action>
      <v-checkbox
        v-model="item.completed"
        @change="updateCheck"
        :disabled="item.deleted_at !== null"
        color="primary"
      ></v-checkbox>
    </v-list-item-action>
    <v-list-item-content>
      <v-list-item-title :class="[item.completed ? 'completed' : '']">{{
        item.name
      }}</v-list-item-title>
    </v-list-item-content>
    <v-list-item-action>
      <v-row>
        <div v-if="item.deleted_at === null">
          <button
            @click="$emit('updateItem', item)"
            icon
            class="mr-4"
            :disabled="item.deleted_at !== null"
          >
            <v-icon>mdi-calendar-edit</v-icon>
          </button>
          <button @click="removeItem" icon>
            <v-icon>mdi-calendar-minus</v-icon>
          </button>
        </div>
        <div v-else>
          <button @click="restoreItem" icon>
            <v-icon>mdi-calendar-refresh</v-icon>
          </button>
        </div>
      </v-row>
    </v-list-item-action>
  </v-list-item>
</template>
<script>
import axios from "axios";
export default {
  name: "item",
  props: ["item"],
  methods: {
    updateCheck() {
      axios
        .put(
          "https://jopay-laravel-todolist-app.herokuapp.com/api/item/" + this.item.id,
          {
            item: this.item,
          }
        )
        .then((result) => {
          if (result.status == 200) {
            this.$emit("refreshItems");
            this.$emit("showAlert", "Item updated successfully.");
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    removeItem() {
      axios
        .delete(
          "https://jopay-laravel-todolist-app.herokuapp.com/api/item/" + this.item.id
        )
        .then((result) => {
          if (result.status == 200) {
            this.$emit("refreshItems");
            this.$emit("showAlert", "Item deleted successfully.");
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    restoreItem() {
      axios
        .get(
          "https://jopay-laravel-todolist-app.herokuapp.com/api/item/restore/" +
            this.item.id
        )
        .then((result) => {
          if (result.status == 200) {
            this.$emit("refreshItems");
            this.$emit("showAlert", "Item restored successfully.");
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>
<style scoped>
.completed {
  text-decoration: line-through;
}
</style>
