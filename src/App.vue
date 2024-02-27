<script setup>
// vue3 컴포지션 api 가져오기(데이터를 반응형으로 연동해줌)
// 객체구조분해로 필요한 함수 불러오기
import { reactive, computed } from 'vue';

// reactive함수: 객체 데이터에 반응성을 주입하여 값이 변경되면 리렌더링이 되어 화면이 갱신됨
const data = reactive({
  // 문자열 초기화
  newItem: '',
  items: [],
});

// computed함수: data.items가 변경될때만[화면이 업데이트 될 때만] 함수 실행(계산결과를 캐싱하여 성능 최적화)
const totalItems = computed(() => data.items.length);

// 할일 배열에서 할일의 완료가 true인 요소의 배열 길이 리턴: 할일 완료 요소의 배열 길이 구하기
// 랜더링간의 새로운 변수가 생성되며 변경된 값이 변수에 각각 할당됨
// 완료된 요소를 새로운 배열로 만들고 갯수를 구해줌
const isComplete = computed(
  () => data.items.filter((item) => item.completed).length
);

// 할일추가: data.newItem은 입력필드와 연결되있으므로 빈값이 아닐경우 추가
// 추가된 데이터 마다 고유 id 추가하여 데이터 구분 가능하도록 해줌
const addItem = () => {
  if (data.newItem !== '') {
    data.items.push({
      id: data.items.length + 1,
      text: data.newItem,
      completed: false,
    });
    // 할일추가후 입력필드 빈칸으로 초기화
    data.newItem = '';
  }
};
// 할일 삭제
const deleteItem = (id) => {
  // 해당 id에 맞는 아이템을 찾아서 반환
  const itemToDelete = data.items.find((item) => item.id === id);

  // 해당 아이템의 인덱스를 찾아서 삭제
  const index = data.items.indexOf(itemToDelete);
  data.items.splice(index, 1);
};
</script>

<template>
  <!-- 사실상 헤더푸터가 없음: id 사용하지 않음(스킵네비게이션도 고려하지 않음) -->
  <main class="app">
    <h1>Simple Todo list</h1>
    <div class="todo_count">
      <!-- 템플릿 내부에 데이터 표현시 {{ }} 사용 -->
      완료: {{ isComplete }} / 할 일: {{ totalItems }}
    </div>
    <div class="todo_add">
      <!-- v-model 디렉티브는 폼요소와 데이터를 양방향으로 연결 -->
      <!-- v-on:이벤트명으로 이벤트 연결 -->
      <!-- 핵심 부분 -->
      <input
        type="text"
        v-model="data.newItem"
        v-on:keyup.enter="addItem()"
        placeholder="할 일을 입력하세요"
        title="할 일을 입력하세요"
      />
      <button type="submit" class="add_btn" v-on:click="addItem()">Add</button>
    </div>
    <ul class="todo_list">
      <!-- v-bind 디렉티브는 폼요소와 데이터를 단방향으로 연결하여 가져오기만함 -->
      <!-- 리스트는 고유 id를 key속성에 연결하여 리스트의 순서가 바뀌어도 구별가능하게함 -->
      <!-- 배열요소 개수만큼 li가 반복됨 -->
      <!-- value: 객체(배열요소), id: 객체의 고유id => 변수이름은 마음대로 지어도 됨 -->
      <!-- todo.completed가 true면 completed 문자열이 클래스명이 됨 -->
      <li
        v-for="(v, index) in data.items"
        v-bind:key="v.id"
        v-bind:class="{ completed: v.completed }"
      >
        <!-- id가 모두 달라야 하므로(각각 체크되어야 함) id속성과 todo.id 값을 단방향으로 연결 check1/check와 같이 동적으로 바뀜 -->
        <!-- 라벨클릭시 for로 연결된 체크박스가 클릭되어 true, false가 발생되며
             v-model="item.completed"에 
             의해 completed속성값이 true, false가됨 -->
        <input
          v-bind:id="`check${v.id}`"
          type="checkbox"
          v-model="v.completed"
        />
        <label v-bind:for="`check${v.id}`">{{ v.text }}</label>
        <button type="button" v-on:click="deleteItem(v.id)" class="remove_btn">
          Remove
        </button>
      </li>
    </ul>
  </main>
</template>

<style scoped>
.app {
  padding: 40px;
}
.app h1 {
  font-size: 30px;
  font-weight: 700;
  margin-bottom: 20px;
  color: black;
}
.todo_count {
  margin-bottom: 10px;
}

.todo_add input[type='text'] {
  width: calc(100% - 60px);
  height: 40px;
  padding: 0 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  flex-grow: 1;
}
.todo_add .add_btn {
  height: 40px;
  padding: 0 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-left: 10px;
  color: #fff;
  background: #333;
  border: none;
}
.todo_list {
  margin-top: 20px;
}
.todo_list li {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.todo_list label {
  width: 100%;
}
.todo_list li.completed label {
  color: #ccc;
  text-decoration: line-through;
}
.remove_btn {
  height: 32px;
  padding: 0 10px;
  background: none;
  border: 1px solid var(--point-color1);
  color: var(--point-color1);
  border-radius: 4px;
  margin-left: 20px;
}
</style>
