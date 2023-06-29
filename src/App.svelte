<script>
	import { onMount, createEventDispatcher } from "svelte";
	import { afterUpdate } from 'svelte';
	import "bootstrap/dist/css/bootstrap.min.css";
	let candidates = [];
	let modalOpen = false;
	let selectedCandidate = null;
  
	const dispatch = createEventDispatcher();
  
	async function fetchData() {
	  try {
		const response = await fetch(
		  "https://api.recruitly.io/api/candidate?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77"
		);
		const data = await response.json();
		console.log(data); // Log the data to inspect its format
  
		if (Array.isArray(data.data)) {
		  candidates = data.data.map((candidate) => ({
			id:candidate.id,
			reference: candidate.reference,
			fullName: candidate.fullName,
			mobile: candidate.mobile,
			email: candidate.email,
			address:candidate.address,
			employer:candidate.employer,
			jobTitle:candidate.jobTitle,
			linkedIn:candidate.linkedIn,
			createdOn:candidate.createdOn,
			ownerId:candidate.ownerId,
			ownerName:candidate.ownerName,
			timeZone:candidate.timeZone


			 }));
		} else {
		  console.error("Invalid data format:", data);
		}
	  } catch (error) {
		console.error("Error fetching data:", error);
	  }
	}
  
	onMount(async () => {
	  await fetchData();
	});
  
	function openModal(candidate) {
	  selectedCandidate = candidate;
	  modalOpen = true;
	}
  
	function closeModal() {
	  modalOpen = false;
	}
  </script>
 <div class="table-responsive">
	<div class="table">
	  <div class="horizontal-table">
		<div class="card">
		  <label class="card-title text-primary">Review Pending</label>
		</div>
		<div class="card">
		  <label class="card-title text-primary">Approved</label>
		</div>
		<div class="card">
		  <label class="card-title text-primary">Rejected</label>
		</div>
		<div class="card">
		  <label class="card-title text-primary">Shared</label>
		  {#each candidates as candidate, index}
          <div class="card">
            <p>{candidate.reference}</p>
            <p><a on:click="{() => openModal(candidate)}">{candidate.fullName}</a></p>
            <p>{candidate.mobile}</p>
          </div>
          {/each}
		</div>
		<div class="card">
		  <label class="card-title text-primary">Viewed</label>
		</div>
		<div class="card">
		  <label class="card-title text-primary">Info Request</label>
		</div>
	  </div>
	 
	</div>
  </div>
  
  {#if modalOpen}
	<div class="modal">
	  <div class="modal-content">
		<h3> {selectedCandidate.fullName}</h3>
		<p>ID:{selectedCandidate.id}</p>
		<p>Mobile: {selectedCandidate.mobile}</p>
		<p>Email: {selectedCandidate.email}</p>
		<p>employer: {selectedCandidate.employer}</p>
		<p>Job Title:{selectedCandidate.jobTitle}</p>
		<p>linkedIn: {selectedCandidate.linkedIn}</p>
		<p>createdOn: {selectedCandidate.createdOn}</p>
		<p>ownerId: {selectedCandidate.ownerId}</p>
		<p>timeZone: {selectedCandidate.timeZone}</p>
		
		<button style="color: blue;"   on:click="{closeModal}">Close</button>
	  </div>
	</div>
  {/if}
  
  <style>

	
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
  
	/* Additional styles for the modal */
	.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 70%;
   margin-top:-200vh ; 
   margin-left:60vh ; 
   height: 500vh;
   
    display: flex;
    align-items: center;
    justify-content: center;
  
    z-index: 999;
  }

  .modal-content {
    background-color: #fff;
    padding: 20px;
    border-radius: 5px;
   
    max-width: 80%;
    max-height: 800%;
    overflow: auto;
  }
  </style>
  