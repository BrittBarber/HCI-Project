<template>
  <div>
    <!-- Can only have 1 parent element in a component -->
    <div class="list_list">
      <button class="addListButton" @click="addList()">
        Add List
        <font-awesome-icon icon="plus" class="rightIconOffset" />
      </button>
      <div class="list_list_items">
        <div
          v-for="(item, i ) in lists"
          :key="i"
          :class="{'list': true, 'activeList': i === selected}"
          @click="changeSelected(i)"
        >
          <div v-if="item.notification" class="notification"></div>
          <p class="listListHeader" v-if="item.name!=''">{{item.name}}</p>
          <p class="listListHeader" v-else>Untitled List</p>
        </div>
      </div>
      <div class="deleteListDiv">
        <button
          v-if="deleteListButtonDisabled"
          class="deleteListButton deleteListButtonDisabled"
          @click="removeList()"
          disabled
        >
          Delete List
          <font-awesome-icon icon="trash" class="rightIconOffset" />
        </button>
        <button v-else class="deleteListButton" @click="removeList()">
          Delete List
          <font-awesome-icon icon="trash" class="rightIconOffset" />
        </button>
      </div>
    </div>

    <div class="listContent">
      <div v-if="this.lists.length>0">
        <div class="listHeader">
          <div class="listStyleButtons">
            <div class="toggleWrapper" @click="changeListStyle()">
              <button
                :class="{'bulletButton': true, 'selectedListType': !lists[selected].checkboxes}"
              >
                <font-awesome-icon icon="circle" class="toggleIcon" />
              </button>
              <button
                :class="{'checkboxButton': true, 'selectedListType': lists[selected].checkboxes}"
              >
                <font-awesome-icon icon="check-square" class="toggleIcon" />
              </button>
            </div>
          </div>
          <div class="listNameDiv">
            <input
              type="text"
              class="listNameInput"
              v-model="lists[selected].name"
              placeholder="Enter list name here..."
            />
          </div>
          <div class="addItemDiv">
            <button class="addItemButton" @click="addItem()">
              Add Item
              <font-awesome-icon icon="plus" class="rightIconOffset" />
            </button>
          </div>
        </div>

        <div class="listItems">
          <ul class="listEntries">
            <li
              v-for="(item, index) in lists[selected].listItems"
              :key="index"
              :class="{'listItem': true, 'altListItemColor': index%2 == 0}"
            >
              <div class="bulletCheckboxDiv">
                <font-awesome-icon icon="circle" v-if="!lists[selected].checkboxes" />
                <input
                  type="checkbox"
                  v-else
                  v-model="item.completed"
                  :title="item.completed && item.completedBy != -1? 'Marked as completed by ' + users[item.completedBy-1].name : ''"
                  @click="markCompleted(index)"
                />
              </div>
              <div class="listInputDiv">
                <input
                  class="typeText"
                  type="text"
                  v-model="item.itemName"
                  :class="{'listInput': true, 'listItemChecked': item.completed && lists[selected].checkboxes}"
                  placeholder="Enter list item here..."
                  :title="'Item added by ' + users[item.createdBy-1].name"
                />
              </div>
              <div class="deleteButtonDiv">
                <button class="deleteItemButton" @click="removeItem(index)">
                  Delete Item
                  <font-awesome-icon icon="trash" class="rightIconOffset" />
                </button>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex"; // Used to get data from Vuex store

export default {
  name: "Lists",
  data() {
    // List of local data in this component
    return {
      // Variables go in here
      selected: 0
    };
  },
  computed: {
    // Computed variables
    ...mapGetters({
      lists: "getLists",
      currentUser: "getCurrentUser",
      users: "getUsers"
    }),
    deleteListButtonDisabled: function () {
      return this.lists.length <= 0;
    }
  },
  methods: {
    // Methods in this component
    changeSelected(i) {
      this.selected = i;
    },
    addItem() {
      this.lists[this.selected].listItems.push({
        itemName: "",
        createdBy: this.currentUser.id,
        completed: false,
        completedBy: -1
      });
    },
    removeItem(itemIndex) {
      this.lists[this.selected].listItems.splice(itemIndex, 1);
    },
    addList() {
      this.lists.push({
        name: "",
        checkboxes: false,
        listItems: []
      });
      this.selected = this.lists.length - 1;
    },
    removeList() {
      if (this.lists.length > 0) {
        this.lists.splice(this.selected, 1);
        if (this.selected != 0) {
          this.selected--;
        }
      }
    },
    changeListStyle() {
      this.lists[this.selected].checkboxes = !this.lists[this.selected]
        .checkboxes;
    },
    markCompleted(itemIndex) {
      if (this.lists[this.selected].listItems[itemIndex].completedBy == -1) {
        this.lists[this.selected].listItems[
          itemIndex
        ].completedBy = this.currentUser.id;
      } else {
        this.lists[this.selected].listItems[itemIndex].completedBy = -1;
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- We'll put global css in App.vue -->
<style scoped>
/* Use classes to create css */
/* If you want dynamic application of css, create a computed variable in computed section */
/* Then apply by binding it to html tag by using :class="computedClass" */
/* Include the padding and border in an element's total width and height */

button {
  background: none;
  border: none;
  color: f12711;
  font-size: 1.2vw;
}

@import url("https://fonts.googleapis.com/css?family=Alata|Roboto&display=swap");

/* ***** List of Lists ***** */

.listNameInput::placeholder {
  color: white;
  font-family: "Alata";
}

.typeText::placeholder {
  color: grey;
  font-family: "Roboto";
}

textarea,
select,
input,
button {
  outline: none;
}

.list_list {
  background: rgb(0, 155, 182);
  font-family: "Roboto";
  width: 25%;
  height: 100vh;
  float: left;
  color: white;
}

button {
  border: none;
  color: black;
  border-radius: 5vw;
  outline: none;
  padding-left: 1vw;
  padding-right: 1vw;
}

.addListButton {
  background: white;
  margin-top: 4vh;
  min-height: 20px;
  height: 5vh;
  color: white;
  font-size: 1.2vw;
  color: rgb(0, 106, 124);
}

.list_list_items {
  border-top: solid 1px #c3dde6;
  margin-top: 4vh;
  width: 100%;
  height: calc(74vh - 2px);
  overflow-y: auto;
}

.list {
  border-bottom: solid 1px #c3dde6;
  padding: 10px;
}

.activeList {
  background-color: rgb(0, 106, 124);
}

.listListHeader {
  font-weight: bold;
  font-size: 2vw;
  font-family: "Alata";
  color: white;
}

/* ***** List Editor ***** */

.listContent {
  background-color: white;
  margin-left: 25%;
  width: 75%;
  height: 100vh;
}

.listHeader {
  background-color: rgb(0, 155, 182);
  height: 12vh;
  color: white;
  padding-bottom: 1vh;
  overflow: visible;
}

/* Bullet/Checkbox */

.listStyleButtons {
  width: 15%;
  height: 12vh;
  float: left;
  text-align: center;
  color: white;
}

.toggleWrapper {
  margin-left: 20%;
  margin-top: 4vh;
  height: 5vh;
  min-height: 20px;
  width: 40%;
  min-width: 70px;
  background-color: rgb(105, 215, 236);
  border-radius: 15px;
  font-size: 0.75vw;
}
.bulletButton {
  padding: 0;
  min-height: 20px;
  min-width: 35px;
  height: 5vh;
  width: 50%;
  border-bottom-left-radius: 15px;
  border-top-left-radius: 15px;
  background: none;
  color: white;
}

.toggleIcon {
  font-size: 2vh;
}

.checkboxButton {
  padding: 0;
  min-height: 20px;
  min-width: 35px;
  height: 5vh;
  width: 50%;
  border-bottom-right-radius: 15px;
  border-top-right-radius: 15px;
  color: white;
}

.selectedListType {
  background-color: rgb(0, 106, 124);
  border-radius: 15px;
}

/* List Name Input */

.listNameDiv {
  width: 69%;
  height: 12vh;
  float: left;
  text-align: center;
  color: white;
}

.listNameInput {
  z-index: 1;
  margin-top: calc(6vh - 18px);
  font-size: 28px;
  font-weight: bold;
  padding-top: 1%;
  padding-bottom: 1%;
  background: none;
  border: none;
  text-align: center;
  width: 100%;
  color: black;
  font-family: "Alata";
  color: white;
}

/* Add Item */

.addItemDiv {
  width: 16%;
  height: 12vh;
  float: left;
  text-align: center;
}

.addItemButton {
  float: left;
  margin-top: 4vh;
  min-height: 20px;
  height: 5vh;
  color: rgb(0, 106, 124);
  font-size: 1.2vw;
  background: white;
}

/* Delete List */

.deleteListDiv {
  border-top: solid 1px #c3dde6;
  bottom: 0;
  left: 0;
  height: 13vh;
  width: 100%;
  text-align: center;
}
.deleteListButton {
  margin-top: 4vh;
  min-height: 20px;
  height: 5vh;
  background-color: white;
  color: red;
  font-size: 1.2vw;
}

/* List Items */

.listItems {
  height: 87vh;
  overflow-y: auto;
}

.listEntries {
  text-align: left;
  padding: 0;
  width: 100%;
  list-style: none;
}

.listItem {
  padding-top: 1%;
  padding-bottom: 1%;
  width: 100%;
  margin: 0;
  min-height: 20px;
  height: 4vh;
}

.altListItemColor {
  background-color: rgb(233, 232, 232);
}

.bulletCheckboxDiv {
  width: 15%;
  float: left;
  text-align: center;
  padding-top: 1vh;
}

.listInputDiv {
  width: 70%;
  float: left;
  text-align: center;
}

.listInput {
  width: 100%;
  padding-top: 0.5%;
  padding-bottom: 0.5%;
  background: none;
  border: none;
  font-size: 1.2vw;
}

.listItemChecked {
  text-decoration: line-through;
}

.deleteButtonDiv {
  width: 15%;
  float: left;
  text-align: center;
}

.deleteItemButton {
  height: 3.5vh;
  min-height: 20px;
  font-size: 1vw;
  background: none;
  color: red;
}

.deleteListButtonDisabled {
  background: rgb(140, 175, 182);
  color: rgb(0, 106, 124);
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
</style>

