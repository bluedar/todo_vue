<script setup>
import { computed, onMounted, ref, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref(""); // 입력한내용
const input_category = ref(null);

//입력값을 todos배열에 넣는함수
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createAt: new Date().getTime(),
    done: false,
    editable: false,
  });
  //입력후 초기화
  input_content.value = "";
  input_category.value = null;
  console.log("todos", todos);
};

//시간순으로 순서 정리
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createAt - b.createAt;
  })
);

// 삭제 함수
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => {
    return t !== todo;
  });
};

//이름입력하는 것을 인지하고 로컬스토리지에 저장
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

//todos가 변하는 것을 인지하고 로컬 스토리지 저장, 업데이트
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
  //deep - 속성의 프로퍼티나, 하위 뎁스값도 감시
);

//새로 열었을때 로컬 스토리지에서 불러오기 (없을때는 비워놈)
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
  console.log(todos.value);
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
        What's Up<input type="text" placeholder="여기 이름!" v-model="name" />
      </h1>
    </section>
    <section class="create-todo">
      <h2>할일 만들기</h2>
      <form id="new-todo-form" v-on:submit.prevent="addTodo">
        <h4>당신이 해야할일은?</h4>
        <input
          type="text"
          id="content"
          placeholder="해야할일을 적어주세요"
          v-model="input_content"
        />
        <!--{{ input_content }} -->
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="buisiness"
              v-model="input_category"
            />
            <span class="bubble buisiness"></span>
            <div>business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>personal</div>
          </label>
        </div>
        <!-- {{ input_category }} -->
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>할일 리스트</h3>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todos_asc"
          v-bind:class="`todo-item ${todo.done && 'done'} `"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @:click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
