<template>
  <v-app id="inspire">
    <v-app-bar app clipped-right color="primary" dark>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title>Purpose driven planner</v-toolbar-title>
      <v-spacer />
    </v-app-bar>
    <v-navigation-drawer v-model="drawer" app clipped left>
      <v-list dense>
        <div v-for="(vItem, id) in vItemList" :key="id">
          <v-list-item>
            <!-- <v-list-item-action>
              <v-icon>{{vItem.icon}}</v-icon>
            </v-list-item-action>-->
            <v-list-item-content>
              <div draggable @dragend="dragEnd" @dragstart="dragStart($event, vItem, id)">
                <v-text-field v-if="vItem.type == 'textbox-outline'" outlined></v-text-field>
                <v-text-field v-if="vItem.type == 'textbox'"></v-text-field>
              </div>
              <!-- <v-list-item-title>{{vItem.label}}</v-list-item-title> -->
            </v-list-item-content>
          </v-list-item>
        </div>
      </v-list>
    </v-navigation-drawer>
    <v-list-item>
      <v-list-item-content>
        <v-list-item-title class="title">Toolbar</v-list-item-title>
        <v-list-item-subtitle>items</v-list-item-subtitle>
      </v-list-item-content>
    </v-list-item>

    <v-content>
      <v-container>
        <v-row align="center" justify="center">
          <v-col class="text-center">
            <v-card height="1000px">
              <v-container class="pa-5">
                <v-row>
                  <v-col
                    align-self="start"
                    xs="6"
                    sm="6"
                    md="6"
                    lg="4"
                    v-for="(item, id) in dropzone"
                    :key="id"
                  >
                    <v-row>
                      <v-col class="ma-0 pa-0">
                        <div
                          draggable
                          @dragend="dragEnd"
                          @dragstart="dragStartItem($event, item, id)"
                        >
                          <v-text-field
                            class="ma-0 pa-0"
                            v-model="item.input"
                            v-if="item.type == 'textbox-outline'"
                            outlined
                          ></v-text-field>
                          <v-text-field
                            class="ma-0 pa-0"
                            v-model="item.input"
                            v-if="item.type == 'textbox'"
                          ></v-text-field>
                        </div>
                      </v-col>
                      <v-col class="ma-0 pa-0 px-1" xs="1" sm="1" md="1">
                        <v-card
                          @dragover="dragover_handler"
                          @drop="dropHandler($event, id)"
                          @dragleave="dragLeave"
                          dropzone
                          color="grey"
                          style="height:55px;"
                        ></v-card>
                      </v-col>
                    </v-row>
                  </v-col>
                </v-row>
              </v-container>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-content>

    <v-footer app>
      <span>Vuetify</span>
      <v-spacer />
      <span>&copy; 2019</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  props: {
    source: String
  },
  methods: {
    insertAt(array, index, ...elementsArray) {
      array.splice(index, 0, ...elementsArray);
    },
    dragStartItem(event, vItem, id) {
      console.log("dragStartItem");
      event.dataTransfer.setData("itemType", vItem.type);
      event.dataTransfer.setData("itemId", id);
      event.dataTransfer.setData("itemContent", vItem.input || "");
      //   remove item from id position

      event.effectAllowed = "copyMove";
    },
    dragLeave(ev) {
      console.log(ev.target);
      const el = ev.target;
      el.classList.add("grey");
      el.classList.remove("success");
      ev.preventDefault();
    },
    dragover_handler(ev) {
      console.log("dragOver");
      const el = ev.target;
      el.classList.remove("grey");
      el.classList.add("success");
      ev.preventDefault();
    },
    dropHandler(event, id) {
      console.log("dropeed");
      event.target.style.backgroundColor = "red";
      const itemId = event.dataTransfer.getData("itemId");
      const type = event.dataTransfer.getData("itemType");
      const input = event.dataTransfer.getData("itemContent");
      if (itemId) {
        console.log(input);
        this.insertAt(this.dropzone, id + 1, { type, input });
        this.dropzone.splice(itemId, 1);
      } else {
        this.insertAt(this.dropzone, id + 1, { type });
      }
      this.dragLeave(event);
    },
    dragEnd(event) {
      console.log("dragEnd");
      // Remove all of the drag data
      event.dataTransfer.clearData();
    },
    dragStart(event, vItem) {
      console.log("dragStart");
      event.dataTransfer.setData("itemType", vItem.type);
      event.effectAllowed = "copyMove";
    }
  },
  data: () => ({
    dropzone: [
      {
        type: "textbox-outline",
        input: "Test input"
      }
    ],
    vItemList: [
      {
        icon: "mdi-exit-to-app",
        label: "Open Temporary Drawer",
        type: "textbox-outline"
      },
      {
        icon: "mdi-exit-to-app",
        label: "Open Temporary Drawer",
        type: "textbox"
      }
    ],
    drawer: null,
    left: false
  })
};
</script>