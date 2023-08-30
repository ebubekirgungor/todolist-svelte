<script>
  import Card from "@smui/card";
  import Button, { Label } from "@smui/button";
  import IconButton from "@smui/icon-button";
  import Checkbox from "@smui/checkbox";
  import DataTable, { Body, Row, Cell } from "@smui/data-table";
  import Dialog, { Title, Actions } from "@smui/dialog";
  import Textfield from "@smui/textfield";

  let todos = JSON.parse(localStorage.getItem("todos")) || [];
  let add_dialog = false;
  let edit_dialog = false;
  let edit_id = 0;
  let todo_text = "";
  let update_text = "";

  function update_localstorage() {
    localStorage.setItem("todos", JSON.stringify(todos));
  }
  function add_todo() {
    if (todo_text === "") {
      return;
    }
    todos.push({
      id: Date.now(),
      text: todo_text,
      done: false,
    });
    todos = todos;
    update_localstorage();
    todo_text = "";
  }
  function edit_todo(id) {
    if (update_text === "") {
      return;
    }
    todos.find((todo) => todo.id === id).text = update_text;
    todos = todos;
    edit_dialog = false;
    update_localstorage();
  }
  function delete_todo(id) {
    todos.splice(
      todos.findIndex((todo) => todo.id === id),
      1
    );
    todos = todos;
    update_localstorage();
  }
  function checkbox_toggle() {
    setTimeout(() => update_localstorage(), 200);
  }
  function open_edit_dialog(id) {
    edit_dialog = true;
    update_text = todos.find((todo) => todo.id === id).text;
    edit_id = id;
  }
</script>

<main>
  <div class="text-center">
    <Button
      on:click={() => (add_dialog = true)}
      class="primary addbutton"
      variant="raised"
    >
      <Label>Add Todo</Label>
    </Button>
    <Card style="min-width: 390px; max-width: 700px">
      <DataTable>
        <Body>
          {#each todos as todo (todo.id)}
            <Row style="height: 57px">
              <Cell
                ><Checkbox
                  class="checkbox"
                  on:click={() => checkbox_toggle()}
                  bind:checked={todo.done}
                /></Cell
              >
              <Cell>{todo.text}</Cell>
              <Cell style="width: 0px"
                ><IconButton
                  class="material-icons"
                  on:click={() => open_edit_dialog(todo.id)}
                  >edit_square</IconButton
                ></Cell
              >
              <Cell style="width: 0px"
                ><IconButton
                  class="material-icons"
                  on:click={() => delete_todo(todo.id)}>delete</IconButton
                ></Cell
              >
            </Row>
          {/each}
        </Body>
      </DataTable>
    </Card>

    <Dialog bind:open={add_dialog}>
      <Card style="width: 400px">
        <Title class="title">Add Todo</Title>
        <div
          style="display: flex; justify-content: center; align-items: center;"
        >
          <Textfield
            variant="filled"
            class="text-field"
            bind:value={todo_text}
          />
        </div>
        <Actions>
          <Button on:click={() => add_todo()}>
            <Label>Add</Label>
          </Button>
          <Button on:click={() => (add_dialog = false)}>
            <Label>Close</Label>
          </Button>
        </Actions>
      </Card>
    </Dialog>

    <Dialog bind:open={edit_dialog}>
      <Card style="width: 400px">
        <Title class="title">Edit Todo</Title>
        <div
          style="display: flex; justify-content: center; align-items: center;"
        >
          <Textfield
            variant="filled"
            class="text-field"
            bind:value={update_text}
          />
        </div>
        <Actions>
          <Button on:click={() => edit_todo(edit_id)}>
            <Label>Edit</Label>
          </Button>
          <Button on:click={() => (edit_dialog = false)}>
            <Label>Close</Label>
          </Button>
        </Actions>
      </Card>
    </Dialog>
  </div>
</main>

<style global lang="scss">
  @use "@material/button/mixins" as button;
  @use "@material/theme/color-palette";
  :global(body) {
    background-color: #f5f5f5;
  }
  :global(.addbutton) {
    margin-top: 56px;
    margin-bottom: 56px;
  }
  :global(.checkbox) {
    margin-right: -35px;
    margin-bottom: -21px;
  }
  :global(.title) {
    margin-top: 20px;
    font-size: 1.5rem;
    pointer-events: none;
    user-select: none;
    text-align: center;
  }
  :global(.text-field) {
    width: 400px;
    margin: 28px;
    float: center;
  }
  :global(.text-center) {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  :global(.primary) {
    :global(&.mdc-button) {
      @include button.ink-color(color-palette.$purple-500);
    }
  }
  :global(.mdc-data-table) {
    border-style: none !important;
  }
  :global(.checkbox) {
    margin-right: -35px;
    margin-bottom: -1px;
  }
</style>
