<script>
  import { fade } from "svelte/transition";

  export let planModal;

  let disabled = false;
  async function handleSubmit(e) {
    const formData = new FormData(e.target);

    const data = {};
    for (let field of formData) {
      const [key, value] = field;
      data[key] = value;
    }

    try {
      disabled = true;
      fetch("http://localhost:8080/courses.json", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({data}),
      })
      alert("Added new plan!");
    } catch(error) {
      console.log(error)
    } finally {
      disabled = false;
    }
  }
</script>

<div transition:fade={{ duration: 200 }}>
  <input type="button" value="Close" on:click={() => (planModal = false)} />
  <form on:submit|preventDefault={handleSubmit}>
    <h2>Add a new course</h2>
    <label for="name">Name for course *</label>
    <input
      type="text"
      name="name"
      id="name"
      placeholder="Add name for course"
      required
    />
    <label for="name">Tag</label>
    <input type="text" name="tag" id="tag" placeholder="Add tag for course" />
    <span>Tag color</span>
    <select name="tagColor" id="tagColor">
      <option value="" selected hidden>Choose tag color</option>
      <option value="yellow">Yellow</option>
      <option value="light-blue">Light blue</option>
      <option value="red">Red</option>
      <option value="green">Green</option>
    </select>
    <label for="details">Details for the course *</label>
    <input
      type="text"
      name="details"
      id="details"
      placeholder="Add descriptive details for the course"
      required
    />
    <label for="startDate">Start date for course</label>
    <input type="date" name="startDate" id="startDate" />
    <label for="endDate">End date for course</label>
    <input type="date" name="endDate" id="endDate" />
    <label for="imgSrc">Image for course</label>
    <input
      type="text"
      name="imgSrc"
      id="imgSrc"
      placeholder="Add image link for the course"
    />
    <button type="submit" {disabled}>Add plan</button>
  </form>
</div>

<style>
  div {
    position: fixed;
    top: 0;
    left: 0;
    background-color: rgba(244, 244, 244, 0.5);
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  input[type="button"] {
    position: absolute;
    top: 20%;
    right: 20%;
  }

  form {
    display: flex;
    flex-direction: column;
    width: 80%;
    max-width: 780px;
    background-color: #fff;
    padding: 1rem;
    border-radius: 5px;
    border: 1px solid #e8e8e8;
  }

  button[type="submit"] {
    padding: 0.5rem 1rem;
    color: #fff;
    background: var(--btn-green);
    border-radius: 5px;
  }
</style>
