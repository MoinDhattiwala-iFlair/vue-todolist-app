<template>
  <v-container>
    <v-row>
      <v-col>
        <v-card elevation="2" :loading="isLoading">
          <v-toolbar justify="center" color="light-green">
            <v-toolbar-title class="heading white--text">ToDo List</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn icon>
              <v-icon color="white" @click="getList">mdi-calendar-sync</v-icon>
            </v-btn>
            <v-btn icon>
              <v-icon color="white" @click="addNewItem">mdi-calendar-plus</v-icon>
            </v-btn>
            <template v-slot:extension>
              <v-tabs
                v-model="tabs"
                background-color="transparent"
                color="white"
                grow
                icons-and-text
              >
                <v-tabs-slider color="purple"></v-tabs-slider>
                <v-tab href="#tab-1">
                  Pending ({{ items[0].length }})
                  <v-icon>mdi-calendar-clock</v-icon>
                </v-tab>

                <v-tab href="#tab-2">
                  Completed ({{ items[1].length }})
                  <v-icon>mdi-calendar-check</v-icon>
                </v-tab>

                <v-tab href="#tab-3">
                  Trashed ({{ items[2].length }})
                  <v-icon>mdi-calendar-remove</v-icon>
                </v-tab>
              </v-tabs>
            </template>
          </v-toolbar>
          <v-tabs-items v-model="tabs">
            <v-tab-item v-for="i in 3" :key="i" :value="'tab-' + i">
              <v-card flat>
                <v-list v-for="item in items[i - 1]" :key="item.id">
                  <item
                    :item="item"
                    v-on:refreshItems="getList"
                    v-on:showAlert="showNotification"
                    v-on:updateItem="updateItem"
                  ></item>
                </v-list>
              </v-card>
            </v-tab-item>
          </v-tabs-items>
        </v-card>
      </v-col>
    </v-row>
    <add-item
      :dialog.sync="dialog"
      :item="item"
      v-on:refreshItems="getList"
      v-on:showAlert="showNotification"
    ></add-item>
    <v-snackbar v-model="snackbar.active" :timeout="snackbar.timeout" class="text-center">
      {{ snackbar.text }}
      <template v-slot:action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar.active = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
import Item from "./Item";
import AddItem from "./AddItem";
import axios from "axios";
export default {
  components: {
    Item,
    AddItem,
  },
  data: () => ({
    tabs: null,
    isLoading: false,
    dialog: false,
    items: [],
    item: {},
    snackbar: {
      active: false,
      timeout: 2000,
      text: "",
    },
  }),
  methods: {
    getList() {
      this.isLoading = true;
      axios
        .get("http://127.0.0.1:8000/api/item")
        .then((result) => {
          if (result.status == 200) {
            this.items = result.data;
          }
          this.isLoading = false;
        })
        .catch((err) => {
          console.log(err);
          this.isLoading = false;
        });
    },
    showNotification(msg) {
      this.snackbar.text = msg;
      this.snackbar.active = true;
    },
    addNewItem() {
      this.item = { id: 0, name: "" };
      this.dialog = true;
    },
    updateItem(item) {
      this.item = item;
      this.dialog = true;
    },
  },
  created() {
    this.getList();
  },
};
</script>
