<template>
  <!-- preventをつけることで、デフォルトの挙動をキャンセルする -->
  <!-- 今回の場合、submitボタンを押すとページがリロードされるのをキャンセルしている -->
  <form @submit.prevent="click">
    <input type="text" v-model="text" />
    <button type="submit">追加</button>
    <ul>
      <li :class="{ done: todo.done }" v-for="(todo, i) of todos">
        <input
          :id="'todo' + i"
          type="checkbox"
          :checked="todo.done"
          @change="change(todo, $event)"
        />
        <!--        <input type="number" v-model.number="todo.priority" @change="save()" />-->
        <button type="button" @click="up(todo)">↑</button>
        <button type="button" @click="down(todo)">↓</button>
        <label :for="`todo${i}`"> {{ i + 1 }} {{ todo.text }}</label>
        <button type="button" @click="remove(i)">削除</button>
      </li>
    </ul>
  </form>
</template>

<script setup lang="ts">
interface Todo {
  text: string;
  done: boolean;
  priority: number;
}
//renderメソッドで作成された
//DOMノードもしくはReact・Vue要素の参照を保持するオブジェクト
const text = ref("");
const todos = ref<Todo[]>([]);

// const addTodo = () => {
//   todos.value.push(text.value)
//   text.value = ''
// }

function click() {
  todos.value = [
    { text: text.value, done: false, priority: 0 },
    ...todos.value,
  ];
  text.value = "";
  save();
}

function remove(i: number) {
  todos.value.splice(i, 1);
  save();
}

function change(todo: { text: string; done: boolean }, e: Event) {
  //instanceof 演算子は、特定のオブジェクトがクラスのインスタンスかをチェックするJavaScriptの演算子
  if (e.target instanceof HTMLInputElement) {
    todo.done = e.target.checked;
  }
}

function save() {
  localStorage.setItem("todos", JSON.stringify(todos.value));
}

function up(todo: Todo) {
  const i = todos.value.indexOf(todo);
  if (i > 0) {
    todos.value.splice(i, 1); // 1つ目の引数は、削除する要素の位置
    todos.value.splice(i - 1, 0, todo); //3つ引数は、splice(削除する要素の位置, 削除する要素数, 追加する要素)
    save();
  }
}

function down(todo: Todo) {
  const i = todos.value.indexOf(todo);
  if (i < todos.value.length - 1) {
    todos.value.splice(i, 1);
    todos.value.splice(i + 1, 0, todo);
    save();
  }
}

onMounted(() => {
  const json = localStorage.getItem("todos");
  if (json) {
    todos.value = JSON.parse(json);
  }
});
</script>

<style>
.done {
  text-decoration: line-through;
  color: aliceblue;
}

input[type="number"] {
  width: 2em;
}
</style>
