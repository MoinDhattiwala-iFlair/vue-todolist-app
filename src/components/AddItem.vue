<template>
  <v-row justify="center">
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">New Item</span>
        </v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid" lazy-validation @submit.prevent="validate">
            <v-text-field
              v-model="item.name"
              :rules="nameRules"
              class="mb-2"
              label="Name"
              required
            ></v-text-field>

            <v-btn :disabled="!valid" color="success" class="mr-4" type="submit">
              Save
            </v-btn>

            <v-btn color="error" class="mr-4" @click="reset"> Reset </v-btn>

            <v-btn color="warning" @click="$emit('update:dialog', false)"> Close </v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-row>
</template>
<script>
import axios from "axios";
export default {
  name: "add-item",
  props: ["dialog", "item"],
  data: () => ({
    /* item: {
      name: "",
    }, */
    valid: true,
    nameRules: [(v) => !!v || "Name is required"],
  }),
  methods: {
    validate() {
      if (this.$refs.form.validate()) {
        let api = null;
        if (this.item.id === 0) {
          api = axios.post("https://jopay-laravel-todolist-app.herokuapp.com/api/item", {
            item: this.item,
          });
        } else {
          api = axios.put(
            "https://jopay-laravel-todolist-app.herokuapp.com/api/item/" + this.item.id,
            {
              item: this.item,
            }
          );
        }
        api
          .then((result) => {
            if (result.status == 201 || result.status == 200) {
              this.$refs.form.reset();
              this.$emit("update:dialog", false);
              this.$emit("refreshItems");
              if (result.status == 201) {
                this.$emit("showAlert", "New item added successfully.");
              } else if (result.status == 200) {
                this.$emit("showAlert", "Item updated successfully.");
              }
            }
          })
          .catch((err) => {
            console.log(err);
          });
      }
    },
    reset() {
      this.$refs.form.reset();
    },
  },
};
</script>
