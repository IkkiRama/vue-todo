<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				What's up, <input type="text" id="name" placeholder="Name here">
			</h2>
		</section>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form id="new-todo-form" method="post">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. make a video"
					v-model="state.todo"
					required
					/>
				
				<h4>Pick a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							@click="state.category = 'business'"
							/>
						<span class="bubble business"></span>
						<div>Business</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category"							
							id="category2" 
							value="personal"
							@click="state.category = 'personal'"
							/>
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

				</div>

				<button type="submit" id="add" @click="addTodo">Add todo</button>
				<button type="submit" id="update" class="update" style="display:none" @click="updateTodo">Update todo</button>
			</form>
		</section>

		<section class="todo-list">
			<h2>TODO LIST</h2>
			<div class="list" id="todo-list">

				<div v-if="state.todos.length < 1">
					<h3>Tidak Ada Todo</h3>
				</div>

				<div v-else class="" v-for="todo in state.todos" :key="todo">
					<div :class="`todo-item ${todo.isComplate && 'done'}`">
						<label>
							<input type="checkbox" />
							<span :class="`bubble ${
								todo.category == 'business' 
									? 'business' 
									: 'personal'
							}`"></span>
						</label>

						<div class="todo-content">
							<p>{{ todo.activity }}</p>
						</div>

						<div class="actions">

							<button v-if="!todo.isComplate" class="primary" @click="changeStatus(todo.id)">Done</button>
							<button v-else class="primary" @click="changeStatus(todo.id)">Un Done</button>
							<button class="update" @click="editTodo(todo.id)">Edit</button>
							<button class="delete" @click="removeTodo(todo.id)">Delete</button>
						</div>
					</div>

				</div>

			</div>
		</section>

	</main>
</template>

<script setup>
import {reactive} from "vue"
const state = reactive({
	id : "",
	todo : "",
	category : "",
	todos : [],
	isComplate : false,
	todoPersonal : [],
	todoBussiness : []
})

if (localStorage.getItem("todos") !== null) {
	state.todos = JSON.parse(localStorage.getItem("todos"))
}else{
	localStorage.setItem("todos", [])
}

const saveToLocalStorage = () => localStorage.setItem("todos", JSON.stringify(state.todos))


const addTodo = (event) =>{
	event.preventDefault();
	if (state.todo.trim() == "" || state.category == "") {
		alert("Todo is empty");
		return;
	}
	let data = {
		id : state.todos.length + 1,
		activity : state.todo,
		category : state.category,
		isComplate : false
	}

	state.todos.push(data)
	saveToLocalStorage()
	state.todo = "";
	state.category = ""
	
}



const removeTodo = (todoId) => {
	if (confirm("Are you sure?")) {
		state.todos = state.todos.filter(todo => todo.id != todoId)		
	}
	saveToLocalStorage()
}


const editTodo = (todoId) => {
	const todo = state.todos.find(todo => todo.id == todoId)
	state.todo = todo.activity;
	state.category = todo.category;
	state.isComplate = todo.isComplate;
	state.id = todo.id;
	document.querySelector("#update").style.display = "block";
	document.querySelector("#add").style.display = "none";

	// const newTodo = prompt("Edit todo", todo.activity)
	// if (newTodo != null) {
	// 	todo.activity = newTodo
	// }
}

const updateTodo = (event) =>{
	event.preventDefault();
	if (state.todo.trim() == "" || state.category == "" || state.id == "") {
		alert("Todo is empty");
		return;
	}
	state.todos.splice(state.todos.findIndex(todo => todo.id == state.id), 1, {
		id : state.id,
		activity : state.todo,
		category : state.category,
		isComplate : state.isComplate
	})
	saveToLocalStorage()
}


const changeStatus= (todoId) =>{
	const todo = state.todos.find(todo => todo.id == todoId)
	todo.isComplate = !todo.isComplate
	saveToLocalStorage()
}

</script>