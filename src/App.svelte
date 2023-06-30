<script>
	import { onMount, createEventDispatcher } from "svelte";
	import { afterUpdate } from 'svelte';
	import "bootstrap/dist/css/bootstrap.min.css";
	let candidates = [];
	let modalOpen = false;
	let selectedCandidate = null;
	let gridView = false; // Add gridView variable to track the view mode
  
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
			id: candidate.id,
			reference: candidate.reference,
			fullName: candidate.fullName,
			mobile: candidate.mobile,
			email: candidate.email,
			address: candidate.address,
			employer: candidate.employer,
			jobTitle: candidate.jobTitle,
			linkedIn: candidate.linkedIn,
			createdOn: candidate.createdOn,
			ownerId: candidate.ownerId,
			ownerName: candidate.ownerName,
			timeZone: candidate.timeZone
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
  
	function toggleViewMode() {
	  gridView = !gridView;
	}
</script>

<button class="list-view-button" on:click="{toggleViewMode}">
  {#if gridView}
    Grid View
  {:else}
    List View
  {/if}
</button>

{#if gridView}
<div class="table-responsive">
	
	
	<table class="table">
	  <thead>
		<tr>
		  <th class="card-title text-primary">ID</th>
		  <th class="card-title text-primary">Full Name</th>
		  <th class="card-title text-primary">Reference</th>
		  <th class="card-title text-primary">email</th>
		  <th class="card-title text-primary">Mobile</th>
		</tr>
	  </thead>
	

	  <tbody>
		{#each candidates as candidate, index}
		<tr>
			<td>{candidate.id}</td>
			<td>{candidate.fullName}</td>
			<td>{candidate.reference}</td>
		  <td>{candidate.email}</td>
		  <td>{candidate.mobile}</td>
		</tr>
		{/each}
	  </tbody>
	</table>
  </div>
  
{:else}
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
              <p style="color: blue;" class="why-recruitly-anchor"><a on:click="{() => openModal(candidate)}">{candidate.fullName}</a></p>
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
{/if}

{#if modalOpen}
  <div class="modal">
    <div class="modal-content">
		<div class="mb-3">
      <h3 style="color: blue;"> {selectedCandidate.fullName}</h3>
	</div>
	<div class="mb-3">
      <p >ID:{selectedCandidate.id}</p></div>
	  <div class="mb-3">
      <p>Mobile: {selectedCandidate.mobile}</p></div>
	  <div class="mb-3">
      <p>Email: {selectedCandidate.email}</p></div>
	  <div class="mb-3">
      <p>Employer: {selectedCandidate.employer}</p></div>
	  <div class="mb-3">
      <p>Job Title:{selectedCandidate.jobTitle}</p></div>
	  <div class="mb-3">
      <p>LinkedIn: {selectedCandidate.linkedIn}</p></div>
	  <div class="mb-3">
      <p>CreatedOn: {selectedCandidate.createdOn}</p></div>
	  <div class="mb-3">
      <p>OwnerId: {selectedCandidate.ownerId}</p></div>
	  <div class="mb-3">
      <p>TimeZone: {selectedCandidate.timeZone}</p></div>
      
      <button style="color: blue;" on:click="{closeModal}">Close</button>
    </div>
  </div>
{/if}

<style>

	
	
  .why-recruitly-anchor {
    cursor: pointer;
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
  
  /* Additional styles for the modal */
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 70%;
    margin-top: -200vh;
    margin-left: 60vh;
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
