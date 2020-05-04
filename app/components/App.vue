<template>
  <Page>
    <ActionBar title="Todo" />
    <GridLayout columns="*" rows="*, auto">
      <ScrollView class="todo-area" row="0">
        <StackLayout v-if="todos.length > 0" class="todos">
          <StackLayout v-for="(todo, i) in todos" :key="i" orientation="horizontal" class="todo">
            <Label class="content" :class="{ done: todo.isDone }" :text="todo.title" />
            <Switch class="check" v-model="todo.isDone" @checkedChange="doneTodo(todo)" />
          </StackLayout>
        </StackLayout>
        <StackLayout v-else id="empty-msg">
          <Label text="タスクがありません" />
        </StackLayout>
      </ScrollView>
      <GridLayout columns="*, auto" row="1">
        <TextField id="comment-input" col="0" v-model="content" hint="タスクを入力" />
        <Button id="submit" col="1" text="追加" @tap="add()" />
      </GridLayout>
    </GridLayout>
  </Page>
</template>

<script >
import API, { graphqlOperation } from '@aws-amplify/api';
import { createTodo, updateTodo } from '@/graphql/mutations';
import { listTodos } from '@/graphql/queries';

export default {
  data() {
    return {
      todos: [],
      content: ''
    }
  },
  created() {
    this.fetchTodos()
  },
  methods: {
    generateId () {
      return this.todos.length
    },
    async add () {
      let todo = {
        id: this.generateId(),
        title: this.content,
        isDone: false
      }
      try {
        await API.graphql(graphqlOperation(createTodo, { input: todo }))
      } catch (error) {
        console.error(error)
      } finally {
        this.resetInput()
        this.fetchTodos()
      }
    },
    async fetchTodos () {
      try {
        const res = await API.graphql(graphqlOperation(listTodos, { limit: 100 }))
        this.todos.push(...res.data.listTodos.items)
      } catch (error) {
        console.error(error)
      }
    },
    async doneTodo (todo) {
      try {
        const res = await API.graphql(graphqlOperation(updateTodo, {
          input: todo
        }))
      } catch (error) {
        console.error(error)
      }
    },
    resetInput () {
      this.content = ''
    }
  }
}
</script>

<style scoped>
ActionBar {
  color: white;
}

.todo-area {
  background: #E5E5E5;
  width: 100%;
  height: 100%;
}

.todos {
  height: 100%;
  margin: 8px;
  border-right: 1px solid #ddd;
  border-left: 1px solid #ddd;
  background-color: #E5E5E5;
  box-shadow: 0px 2px 2px 0px rgba(0, 0, 0, 0.2) inset;
}

.todo {
  padding: 4px;
}

.content {
  font-size: 20px;
  width: 80%;
}

.done {
  color: gray;
  text-decoration: line-through;
}

.check {
  width: 20%;
}

#empty-msg {
  text-align: center;
  font-size: 16px;
  margin-top: 40px;
}


#submit {
  margin: 0 auto;
}

#comment-input {
  border-width: 1px;
  border-color: #2E2E2E;
  border-radius: 8px;
  height: 100px;
}
</style>
