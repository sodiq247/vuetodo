<script setup>
import {ref, onMounted, computed, watch } from 'vue'

const name = ref('')
const todos = ref([])


 const input_content = ref('')
const input_category = ref(null)


const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return b.createdAt - a.createdAt
}))

const addTodo = () => {

  if (input_content.value.trim() === '' || input_category.value === null){
    return
  }
  todos.value.unshift({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createAt:  new Date().getTime()
  })
}

const removeTodo = (todo) => {
todos.value = todos.value.filter((t) => t !== todo)
}

watch (todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true
})

watch ( name, (newVal) =>{
  localStorage.setItem('name', (newVal))
})

onMounted (() => {
  name.value = localStorage.getItem ('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>
<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" id="text" placeholder="Enter your name" v-model="name">
      </h2>
    </section>

    <secction class="create-todo">
      <h4>CREATE A TASK</h4>
      <form id="new-todo-form " @submit.prevent="addTodo">
      <h4>What's on your mind?</h4>
        
          <input 
          type="text" 
          name="content"
          id="content"
          placeholder="e.g. play football"
          v-model="input_content"
          
          />

          <h4>Pick a category</h4>
          <div class="options">
            <label>
              <input 
              type="radio"
              id="category"
              name="category"
              value="business"
              v-model="input_category"
               />
                <span class="business bubble"></span> 
                <div>Business</div>
            </label>

            <label>
              <input 
              type="radio"
              id="category"
              name="category"
              value="personal"
              v-model="input_category"
               />
                <span class="personal bubble"></span> 
                <div>Personal</div>
            </label>
          </div>
          <input type="submit" value="Add-Task" />
      </form>
       

       
    </secction>

    <section class="todo-list">
			<h3>TASK LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Done</button>
					</div>
				</div> 

			</div>
		</section>
  </main>

</template>

