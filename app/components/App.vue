<template>
  <Page class="page">
    <ActionBar title="My Tasks" class="action-bar" />

    <TabView
      height="100%"
      androidTabsPosition="bottom"
      selectedTabTextColor="#53ba82"
      tabTextFontSize="15"
    >
      <TabViewItem title="To Do" textTransform="uppercase">
        <!-- Positions an input field, a button, and the list of tasks in a vertical stack. -->
        <StackLayout orientation="vertical" width="100%" height="100%">
          <GridLayout columns="2*,*" rows="*" width="100%" height="25%">
            <!-- Configures the text field and ensures that pressing Return on the keyboard produces the same result as tapping the button. -->
            <TextField
              col="0"
              row="0"
              v-model="textFieldValue"
              hint="Type new task..."
              editable="true"
              @returnPress="onButtonTap"
            />
            <Button col="1" row="0" text="Add task" @tap="onButtonTap" />
          </GridLayout>

          <ListView
            class="list-group"
            for="todo in todos"
            @itemTap="onItemTap"
            style="height:75%"
            separatorColor="transparent"
          >
            <v-template>
              <Label
                id="active-task"
                :text="todo.name"
                class="list-group-item-heading"
                textWrap="true"
              />
            </v-template>
          </ListView>
        </StackLayout>
      </TabViewItem>
      <TabViewItem title="Completed" textTransform="uppercase">
        <ListView
          class="list-group"
          for="done in dones"
          @itemTap="onDoneTap"
          style="height:75%"
          separatorColor="transparent"
        >
          <v-template>
            <Label
              id="completed-task"
              :text="done.name"
              class="list-group-item-heading"
              textWrap="true"
            />
          </v-template>
        </ListView>
      </TabViewItem>
    </TabView>
  </Page>
</template>

<script>
export default {
  methods: {
    onItemTap: function(args) {
      action("What do you want to do with this task?", "Cancel", [
        "Mark completed",
        "Delete forever"
      ]).then(result => {
        console.log(result); // Logs the selected option for debugging.
        switch (result) {
          case "Mark completed":
            this.dones.unshift(args.item); // Places the tapped active task at the top of the completed tasks.
            this.todos.splice(args.index, 1); // Removes the tapped active  task.
            break;
          case "Delete forever":
            this.todos.splice(args.index, 1); // Removes the tapped active task.
            break;
          case "Cancel" || undefined: // Dismisses the dialog.
            break;
        }
      });
    },
    onDoneTap: function(args) {
      action("What do you want to do with this task?", "Cancel", [
        "Mark to do",
        "Delete forever"
      ]).then(result => {
        console.log(result);
        switch (result) {
          case "Mark to do":
            this.todos.unshift(args.item);
            this.dones.splice(args.index, 1);
            break;
          case "Delete forever":
            this.dones.splice(args.index, 1);
            break;
          case "Cancel" || undefined:
            break;
        }
      });
    },
    onButtonTap() {
      if (this.textFieldValue === "") return; // Prevents users from entering an empty string.
      console.log("New task added: " + this.textFieldValue + "."); // Logs the newly added task in the console for debugging.
      this.todos.unshift({
        name: this.textFieldValue
      }); // Adds tasks in the ToDo array. Newly added tasks are immediately shown on the screen.
      this.textFieldValue = ""; // Clears the text field so that users can start adding new tasks immediately.
    }
  },
  data() {
    return {
      dones: [],
      todos: [],
      textFieldValue: ""
    };
  }
};
</script>

<style scoped>
.home-panel {
  vertical-align: center;
  font-size: 20;
  margin: 15;
}

.description-label {
  margin-bottom: 15;
}

TextField {
  font-size: 20;
  color: #53ba82;
  margin-top: 10;
  margin-bottom: 10;
  margin-right: 5;
  margin-left: 20;
}

button {
  font-size: 20;
  font-weight: bold;
  color: white;
  background-color: #53ba82;
  height: 40;
  margin-top: 10;
  margin-bottom: 10;
  margin-right: 10;
  margin-left: 10;
  border-radius: 20px;
}

#active-task {
  font-size: 20;
  font-weight: bold;
  color: #53ba82;
  margin-left: 20;
  padding-top: 5;
  padding-bottom: 10;
}

#completed-task {
  font-size: 20;
  color: #d3d3d3;
  margin-left: 20;
  padding-top: 5;
  padding-bottom: 10;
  text-decoration: line-through;
}
</style>
