<script>
	import { onMount } from "svelte";
	import "bootstrap/dist/css/bootstrap.min.css";
	let todos = [];
	let selectedTodo = null; // To keep track of the selected todo
  
	onMount(async () => {
	  try {
		const response = await fetch("https://api.recruitly.io/api/candidateshare/hire7fb9dbc603bb493ba102df5e00091710?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77");
		const data = await response.json();
		console.log(data.candidates);
		if (typeof data === "object" && data !== null && Array.isArray(data.candidates)) {
		  todos = data.candidates.map(item => ({
			reference: item.reference,
			label: item.label,
			email: item.email,
			mobile: item.mobile,
			id :item.id,
			ownerName:item.ownerName,
			headline:item.headline,
			createdOn:item.createdOn

 }));
		} else {
		  console.error("API response is not in the expected format");
		}
	  } catch (error) {
		console.error(error);
	  }
	});
  
	function allowDrop(event) {
	  event.preventDefault();
	}
  
	function dragStart(event, todoIndex) {
	  event.dataTransfer.setData("text/plain", todoIndex.toString());
	}
  
	function drop(event, targetIndex) {
	  event.preventDefault();
	  const todoIndex = parseInt(event.dataTransfer.getData("text/plain"), 10);
	  const draggedTodo = todos[todoIndex];
	  todos.splice(todoIndex, 1);
	  todos.splice(targetIndex, 0, draggedTodo);
	}
  
	function showDetails(todo) {
	  selectedTodo = todo;
	}
  </script>
  
  
  
 
  
  <div class="table-responsive">
	<div class="table">
	  <div class="horizontal-table">
		<div class="card" on:drop="{event => drop(event, 1)}" on:dragover="{allowDrop}">
		  <label class="card-title text-primary">Review Pending</label>
		</div>
		<div class="card" on:drop="{event => drop(event, 1)}" on:dragover="{allowDrop}">
		  <label class="card-title text-primary">Approved</label>
		</div>
		<div class="card" on:drop="{event => drop(event, 1)}" on:dragover="{allowDrop}">
		  <label class="card-title text-primary">Rejected</label>
		</div>
		<div class="card" on:drop="{event => drop(event, 0)}" on:dragover="{allowDrop}">
		  <label class="card-title text-primary">Shared</label>
		  {#each todos as todo, index}
			<div class="vertical-table" draggable="true" on:dragstart="{event => dragStart(event, index)}">
			  <div class="card">
				<p>{todo.reference}</p>
				<p><a href="#Candidate_share" on:click="{() => showDetails(todo)}">{todo.label}</a></p>
				<p>{todo.email}</p>
				<p>{todo.mobile}</p>
			  </div>
			</div>
		  {/each}
		</div>
  
		<div class="card" on:drop="{event => drop(event, 1)}" on:dragover="{allowDrop}">
		  <label class="card-title text-primary">Viewed</label>
		</div>
		<div class="card" on:drop="{event => drop(event, 1)}" on:dragover="{allowDrop}">
		  <label class="card-title text-primary">Info Request</label>
		</div>
	  </div>
	</div>
  </div>
  
 
  {#if selectedTodo}
  <div class="popup">
	<h1 class="left-align" style="color: darkblue;">Name:</h1>
	<p class="popup-text-top" style=" font: 1.3em sans-serif;"> {selectedTodo.label}</p>
	<h1 class="left-align" style="color: darkblue;">Reference:</h1>
	<p class="popup-text" style=" font: 1.3em sans-serif;"> {selectedTodo.reference}</p>
	<h1 class="left-align" style="color: darkblue;">ID:</h1>
	<p class="popup-text" style=" font: 1.3em sans-serif;"> {selectedTodo.id}</p>
	<h1 class="left-align" style="color: darkblue;">ownerName:</h1>
	<p class="popup-text" style=" font: 1.3em sans-serif;"> {selectedTodo.ownerName}</p>
	<h1 class="left-align" style="color: darkblue;">headline:</h1>
	<p class="popup-text"style=" font: 1.3em sans-serif;"> {selectedTodo.headline}</p>
	<h1 class="left-align" style="color: darkblue;">createdOn:</h1>
	<p class="popup-text-bottom"style=" font: 1.3em sans-serif;"> {selectedTodo.createdOn}</p>
	<button class="btn btn-primary" on:click="{() => selectedTodo = null}">Close</button>
  </div>
  {/if}
  
 
  
  <style>




     
	
	.left-align{
  text-align: left;
  font: 1.5em sans-serif;
}

	
		.spacer {
	margin: 0 2px;
  }
  
	.card {
	  margin: 10px;
	  padding: 10px;
	  border: 1px solid #ccc;
	  border-radius: 5px;
	  width: 150px;
	}
  
	.horizontal-table {
	  display: flex;
	  flex-direction: row;
	  overflow-x: auto;
	}
  
	.vertical-table {
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	}
  
	.popup {
	  position: fixed;
	  top: 50%;
	  left: 50%;
	  transform: translate(-50%, -50%);
	  background-color: #f6f3f4;
	  padding: 5px;
	  border-radius: 5px;
	  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
	  z-index: 999;
	  height: 80vh;
	  width: 60vh;
	}
	
  </style>