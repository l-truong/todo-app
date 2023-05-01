<template>	
  <main class="container">
		<!-- CREATE TODO ELEMENT -->
		<section class="create-todo">
			<h3>TODO APP</h3>			
			<form id="new-todo-form" @submit.prevent="addTodo">
				<div class="options">
					<input 
						type="radio" 
						name="category" 
						id="option-1" 
						value="business"
						v-model="input_category" checked />
					<label for="option-1" class="option option-1">
						<span>Business</span>						
						<span>{{ getPendingTasks("business") }} tasks</span>
					</label>				
					<input 
						type="radio" 
						name="category" 
						id="option-2" 
						value="personal"
						v-model="input_category" />
					<label for="option-2" class="option option-2">
						<span>Personal</span>
						<span>{{ getPendingTasks("personal") }} tasks</span>
					</label>
				</div>
				<div class="sumbit">
					<input 
						type="text" 
						name="content" 
						id="content" 
						placeholder="e.g. take out the trash"
						v-model="input_content" />	
					<button type="submit"><font-awesome-icon icon="plus" /></button>
				</div>
			</form>
		</section>
		
		<!-- TODO LIST -->
    	<section class="todo-list">			
			<h4>What's on your todo list?</h4>						
			<div class="list" id="todo-list">											
				<div v-for="todo in todos">					
					<Transition>			
					<div v-if="todo.category == input_category"  :class="`todo-item ${todo.done && 'done'}`">
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
							<button class="delete" @click="removeTodo(todo)">
								<font-awesome-icon icon="fa-xmark"/>
							</button>
						</div>
					</div>	
					</Transition>				
				</div>			
			</div>
		</section>	
		
		<!-- CLEAR TODO LIST -->
		<section class="clear-todo">	
			<div class="clear-options">
				<button class="clear-completed"  @click="clearCompleted()">
					Clear Completed <font-awesome-icon icon="fa-check"/>
				</button>			
				<button class="clear-all"  @click="clearAll()">
					Clear All <font-awesome-icon icon="trash"/>
				</button>
			</div>
		</section>
  </main>  
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
		todos: [],
  		input_content: '',
  		input_category: null
      }
    },
	mounted () {
		this.todos = JSON.parse(localStorage.getItem('todos')) || []
		this.input_category = JSON.parse(localStorage.getItem('category')) || 'business'
    },
	watch: {
		input_category: {
			handler: function (val, oldVal) {
				localStorage.setItem('category', JSON.stringify(this.input_category))
			},
			deep: true
        },	
		todos: {
			handler: function (val, oldVal) {	
				localStorage.setItem('todos', JSON.stringify(val))
			},
			deep: true
		}
	},
    methods: {	
		addTodo () {	
			if (this.input_content.trim() === '' || this.input_category === null) {
				return
			}
			this.todos.push({
				content: this.input_content,
				category: this.input_category,
				done: false,
				editable: false,
				createdAt: new Date().getTime()
			})
			this.input_content=""
		},
		removeTodo(todo) {
			this.todos = this.todos.filter((t) => t !== todo)
		},
		clearCompleted() {
			this.todos = this.todos.filter((t) => (t.category == this.input_category && !t.done) || t.category != this.input_category)
		},
		clearAll() {
			this.todos = this.todos.filter((t) => t.category != this.input_category)
		},
		getPendingTasks (type) {	
			if (type == null) {
				return this.todos.filter((t) => !t.done).length
			}
			return this.todos.filter((t) => !t.done && t.category == type).length
		}
    }
  }
</script>