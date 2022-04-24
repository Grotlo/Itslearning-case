<script>
  import { fly } from "svelte/transition";

  export let index;
  export let course;
  export let courseSelections;

  function formatDate(value) {
		let date = new Date(value);
    return date.getDate() + " " + date.toLocaleString('default', { month: 'short' });
	}
</script>

<li in:fly={{ delay: index * 100, duration: 150 }}>
  <div>
    <input type="checkbox" value={course} bind:group={courseSelections} />
    <img src={course.imgSrc ? course.imgSrc : "fallback-img.avif"} alt={course.name} />
  </div>
  <div>
    <h2>
      {course.name} 
      <span style="background-color: var(--tag-{course.tagColor})">{course.tag.toUpperCase()}</span>
    </h2>
    <p>{course.details}</p>
    {#if course.startDate && course.endDate}
      <span>
        <img src="icons/calendar.svg" alt="calendar icon" />
        {formatDate(course.startDate)}-{formatDate(course.endDate)}
      </span>
    {/if}
  </div>
</li>

<style>
  /* ul */

	li {
		display: flex;
		flex-direction: column;
		background-color: #fff;
		margin-bottom: 1rem;
		padding: 0.5rem;
		border-radius: 5px;
		border: 1px solid #e8e8e8;
	}

	li > div:first-child {
		display: inline-flex;
	}

	li > div:first-child > img {
		width: 100%;
		height: 100px;
		margin: 0 1rem;
		border-radius: 5px;
		object-fit: cover;
	}

	li > div:first-child > input[type="checkbox"] {
		margin-bottom: auto;
	}

	h2 {
		font-size: 1.2rem;
		font-weight: var(--w-bold);
		margin-bottom: 0;
	}

	h2 > span {
		margin-left: 0.5rem;
		font-size: 0.8rem;
		padding: 2px 10px;
		border-radius: 2px;
	}

	li > div:last-child > p {
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;  
		overflow: hidden;
		margin-top: 10px;
	}

	li > div:last-child > span {
		display: flex;
		align-items: center;
		color: #6b546a;
		font-weight: var(--w-bold);
	}

	li > div:last-child > span > img {
		width: 15px;
		height: 15px;
		margin-right: 0.5rem;
	}

  @media (min-width: 480px) {
		/* ul */

		li {
			flex-direction: row;
		}

		li > div:first-child {
			display: inherit;
		}

		li > div:first-child > img {
			width: 200px;
			height: 150px;
		}
  }
</style>