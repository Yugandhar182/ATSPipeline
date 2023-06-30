<script>
	import { onMount, createEventDispatcher } from "svelte";
	import { afterUpdate } from 'svelte';
	import "bootstrap/dist/css/bootstrap.min.css";
	let applicants = [];
	let Details=[];
	let modalOpen = false;
	let selectedCandidate = null;
	let gridView = false; // Add gridView variable to track the view mode
  
	const dispatch = createEventDispatcher();
  
	async function fetchCandidateDetails(candidateId) {
	  try {
	    const response = await fetch(
	      `https://api.recruitly.io/api/candidate/${candidateId}?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77`
	    );
	    const data = await response.json();
	    console.log(data); // Log the fetched candidate details
  
	    if (typeof data === "object" && data !== null) {
	      selectedCandidate = {
	        ...selectedCandidate,
	        employer: data.employer,
	        jobTitle: data.jobTitle,
	        linkedIn: data.linkedIn,
	        createdOn: data.createdOn,
	        ownerId: data.ownerId,
	        timeZone: data.timeZone,
	      };
	    } else {
	      console.error("Invalid candidate details format:", data);
	    }
	  } catch (error) {
	    console.error("Error fetching candidate details:", error);
	  }
	}
  
	function openModal(candidate) {
	  selectedCandidate = candidate;
	  modalOpen = true;
	  fetchCandidateDetails(candidate.id); // Fetch candidate details based on the ID
	}


  
	function closeModal() {
	  modalOpen = false;
	}
  
	function toggleViewMode() {
	  gridView = !gridView;
	}
  
	onMount(async () => {
	  try {
	    const response = await fetch("https://api.recruitly.io/api/candidateshare/hire7fb9dbc603bb493ba102df5e00091710?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77");
	    const data = await response.json();
	    console.log(data.candidates);
	    if (typeof data === "object" && data !== null && Array.isArray(data.candidates)) {
	      Details = data.candidates.map(item => ({
	        reference: item.reference,
	        label: item.label,
	        email: item.email,
	        mobile: item.mobile,
	        id:item.id
	      }));
	    } else {
	      console.error("API response is not in the expected format");
	    }
	  } catch (error) {
	    console.error(error);
	  }
	});
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
        <th class="card-title text-primary">Email</th>
        <th class="card-title text-primary">Mobile</th>
      </tr>
    </thead>
    <tbody>
      {#each Details as Detail, index}
      <tr>
        <td>{Detail.id}</td>
		<a style="color: blue; cursor: pointer;" on:click="{() => openModal(Detail)}">{Detail.label}</a>
        <td>{Detail.reference}</td>
        <td>{Detail.email}</td>
        <td>{Detail.mobile}</td>
      </tr>
      {/each}
    </tbody>
  </table>
</div>
{:else}
<div class="table-responsive">
  <div class="table">
    <div class="horizontal-table">
      <div class="card review-pending">
        <label class="card-title text-primary">Review Pending</label>
      </div>
      <div class="card approved">
        <label  style="color: darkgreen;"  class="card-title text-primary">Approved</label>
      </div>
      <div class="card rejected">
        <label style="color:darkred;" class="card-title text-primary">Rejected</label>
      </div>
      <div class="card shared">
        <label class="card-title text-primary">Shared</label>
        {#each Details as detail, index}
        <div class="card">
          <p>{detail.reference}</p>
          <p style="color: blue;" class="why-recruitly-anchor"><a on:click="{() => openModal(detail)}">{detail.label}</a></p>
          <p>{detail.mobile}</p>
          <p>{detail.email}</p>
        </div>
        {/each}
      </div>
      <div class="card viewed">
        <label class="card-title text-primary">Viewed</label>
      </div>
      <div class="card info-request">
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
      <h3 style="color: blue;">{selectedCandidate.label}</h3>
    </div>
    <div class="mb-3">
      <p1>ID:</p1>  
	  <p2> {selectedCandidate.id}</p2>
	
    </div>
    <div class="mb-3">
      <p1>Mobile: </p1>
	  <p2>{selectedCandidate.mobile}</p2>
    </div>
    <div class="mb-3">
      <p1>Email: </p1>
	  <p2>{selectedCandidate.email}</p2>
    </div>
    <div class="mb-3">
      <p1>Employer: </p1>
	  <p2>{selectedCandidate.employer}</p2>
    </div>
    <div class="mb-3">
      <p1>Job Title: </p1>
	  <p2>{selectedCandidate.jobTitle}</p2>
    </div>
    <div class="mb-3">
      <p1>LinkedIn: </p1>
	  <p2>{selectedCandidate.linkedIn}</p2>
    </div>
    <div class="mb-3">
      <p1>Created On:</p1>
	  <p2> {selectedCandidate.createdOn}</p2>
    </div>
    <div class="mb-3">
      <p1>Owner ID:</p1>
	  <p2> {selectedCandidate.ownerId}</p2>
    </div>
    <div class="mb-3">
      <p1>Time Zone: </p1>
	  <p2>{selectedCandidate.timeZone}</p2>

    </div>
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

.review-pending {
 
  width: 30%;
}

.approved {

  width: 30%;
}

.rejected {
 
  width: 30%;
}

.shared {

 
  width: 40%;
}

.viewed {

  width: 30%;
  
}

.info-request {


  width: 30%;
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
