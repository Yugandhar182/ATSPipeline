<script>
  import { onMount, createEventDispatcher } from "svelte";
  import { afterUpdate } from 'svelte';
  import "bootstrap/dist/css/bootstrap.min.css";
  let applicants = [];
  let Details = [];
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
      console.table(data.table); // Log the fetched candidate details

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
          id: item.id
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
  <table class="table table-bordered">
    <thead>
      <tr>
        <th class="table-danger">ID</th>
        <th class="table-danger">Full Name</th>
        <th class="table-danger">Reference</th>
        <th class="table-danger">Email</th>
        <th class="table-danger">Mobile</th>
      </tr>
    </thead>
    <tbody>
      {#each Details as Detail, index}
      <tr>
        <td>{Detail.id}</td>
        <td>
          <a style="color: blue; cursor: pointer;" on:click="{() => openModal(Detail)}">{Detail.label}</a>
        </td>
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
        <label style="color: darkgreen;" class="card-title .text-success">Approved</label>
      </div>
      <div class="card rejected">
        <label style="color: darkred;" class="card-title .text-danger">Rejected</label>
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
    <div class="modal-header">
      <div class="mb-3">
        <h3 style="color: blue;">{selectedCandidate.label}</h3>
      </div>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">ID:</p1>
      <p2 style="color: darkgreen;"> {selectedCandidate.id}</p2>

    </div>
    <div class="mb-3">
      <p1 style="color: blue;">Mobile: </p1>
      <p2 style="color: darkgreen;">{selectedCandidate.mobile}</p2>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">Email: </p1>
      <p2 style="color: darkgreen;">{selectedCandidate.email}</p2>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">Employer: </p1>
      <p2 style="color: darkgreen;">{selectedCandidate.employer}</p2>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">Job Title: </p1>
      <p2 style="color: darkgreen;">{selectedCandidate.jobTitle}</p2>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">LinkedIn: </p1>
      <p2 style="color: darkgreen;">{selectedCandidate.linkedIn}</p2>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">Created On:</p1>
      <p2 style="color: darkgreen;"> {selectedCandidate.createdOn}</p2>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">Owner ID:</p1>
      <p2 style="color: darkgreen;"> {selectedCandidate.ownerId}</p2>
    </div>
    <div class="mb-3">
      <p1 style="color: blue;">Time Zone: </p1>
      <p2 style="color: darkgreen;">{selectedCandidate.timeZone}</p2>

    </div>
    <button style="  background-color: rgb(237, 78, 117);" on:click="{closeModal}">Close</button>
  </div>
</div>
{/if}

<style>

.list-view-button {
    background-color: rgb(154, 210, 236);
  }
  table {
    width: 150vh; /* Set the desired width */
    height: 600px; /* Set the desired height */
  }

  .modal-header {
    height: 40px; /* Set the desired height for the modal header */
  }

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

  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 90%;
    margin-top: -250vh;
    margin-left: 60vh;
    height: 600vh;
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
