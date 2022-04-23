<script>
  import { fly } from "svelte/transition";
	import { onMount } from "svelte";
	import { courses } from "./courses.js";

	const withoutDatesCourses = [];
	const pastCourses = [];

	const todaysDate = new Date();

	const currentCourses = courses.filter(course => {
		if ((course.startDate && course.endDate) && (course.endDate > todaysDate)) {
			return course;
		} else if (!course.startDate && !course.endDate) {
			withoutDatesCourses.push(course);
		} else if (course.endDate < todaysDate) {
			pastCourses.push(course);
		}
	});

	let menu = [
		{ entry: "Current", list: currentCourses.sort((a, b) => a.startDate - b.startDate) },
		{ entry: "Past", list: pastCourses.sort((a, b) => a.startDate - b.startDate) },
		{ entry: "Without date", list: withoutDatesCourses.sort((a, b) => a.startDate - b.startDate) }
	];

	function formatDate(value) {
		let date = new Date(value);
    return date.getDate() + " " + date.toLocaleString('default', { month: 'short' });
	}

	let searchCourses = "";
	let courseSelections = [];

	$: selectedMenu = menu[0];

	let load = false;
	onMount(() => load = true);
</script>

<svelte:head>
	<title>Itslearning test case</title>
</svelte:head>

<main>
	<h1>Plans</h1>

	<div>
		<input type=radio bind:group={selectedMenu} id={menu[0].entry} value={menu[0]}> 
		<label for={menu[0].entry}>{menu[0].entry}</label>
		<input type=radio bind:group={selectedMenu} id={menu[1].entry} value={menu[1]}> 
		<label for={menu[1].entry}>{menu[1].entry}</label>
		<input type=radio bind:group={selectedMenu} id={menu[2].entry} value={menu[2]}>
		<label for={menu[2].entry}>{menu[2].entry}</label>

		<div>
			<img src="icons/search.svg" alt="search icon" />
			<input
				type="text"
				placeholder="Search"
				bind:value={searchCourses} />
		</div>
	</div>

	<div>
		<input type="button" value="Add plan" />
		<select>
			<option hidden selected>Actions</option>
			<option>Edit</option>
			<option>Delete</option>
		</select>
	</div>

	{#if selectedMenu.list && load}
		<ul>
			{#each selectedMenu.list.filter(
				menu => menu.name.toUpperCase().indexOf(searchCourses.toUpperCase()) !== -1) 
				as course, index}
				<!-- {@const upcoming = course.startDate > todaysDate ? true : false}
				{#if upcoming}
					<p>Hallo</p>
				{/if} -->
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
			{/each}
		</ul>
	{:else}
		<h2>No courses to view</h2>
	{/if}
</main>

<style>
	main {
		padding: 1rem;
		max-width: 1200px;
		margin: 0 auto;
	}

	h1 {
		font-size: 1.5rem;
		font-weight: 600;
	}

	/* main > div:first-of-type */

	main > div:first-of-type {
		display: flex;
		flex-direction: column;
	}

	main > div:first-of-type > div {
		position: relative;
		display: flex;
		align-items: center;
	}

	main > div:first-of-type > div > img {
		position: absolute;
		left: 0.8rem;
		width: 1rem;
		height: 1rem;
	}

	main > div:first-of-type > div > input {
		padding: 1rem 2.25rem 1rem;
		margin: 0;
		border: none;
		background: none;
		color: #837188;
	}

	main > div:first-of-type > div > input::placeholder {
		opacity: 1;
	}

	input[type=radio] {
		display: none;
	}

	label {
		position: relative;
		color: #837188;
		padding: 1rem;
		border-bottom: 1px solid #d4d4d4;
	}

	label:hover {
		cursor: pointer;
	}
	
	input[type="radio"]:checked + label {
		color: inherit;
		font-weight: 600;
	}

	input[type="radio"] + label::after,
	input[type="radio"]:checked + label::after {
		content: '';
		display: block;
		position: absolute;
		height: 0;
		top: 0;
		left: 0;
		border-left: 2px solid #000;
	}

	input[type="radio"]:checked + label::after {
		transition: height 0.2s linear;
		height: 100%;
	}

	/* main > div + div */

	main > div + div {
		padding: 1rem 0;
	}

	div > input[type="button"] {
		margin-right: 1rem;
	}

	input[type="button"] {
		padding: 0.5rem 1rem;
		color: #fff;
		background: var(--color-green);
		border-radius: 5px;
	}

	select {
		text-align: center;
		border-radius: 5px;
		background: #fff;

		-moz-appearance: none;
		-webkit-appearance: none;
		appearance: none;
		background-image: url("/icons/caret-down.svg");
		background-repeat: no-repeat;
		background-position: 90%;
		background-size: 0.8rem;
		padding-right: 2rem;
	}

	/* ul */

	ul {
		padding: 0;
		margin: 0;
	}

	li {
		display: flex;
		flex-direction: column;
		background-color: #fff;
		margin-bottom: 1rem;
		padding: 1rem;
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
		font-weight: 600;
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
		font-weight: 600;
	}

	li > div:last-child > span > img {
		width: 15px;
		height: 15px;
		margin-right: 0.5rem;
	}

	@media (min-width: 480px) {
		/* main > div:first-of-type */

		main > div:first-of-type {
			display: flex;
			flex-direction: row;
			align-items: center;

			border-bottom: 1px solid #d4d4d4;
		}

		main > div:first-of-type > div {
			margin-left: auto;
		}

		label {
			border: none;
		}

		main > div:first-of-type > div > input {
			padding: 0.4em 2.25rem 0.4em;
			background: #fff;
			margin: 0;
			border: 2px solid #ccc;
			color: #acacac;
		}

		input[type="radio"]:checked + label::after {
			border-bottom: 1px solid #000;
			border-left: none;
		}

		input[type="radio"] + label::after,
		input[type="radio"]:checked + label::after {
			width: 0;
		}

		input[type="radio"]:checked + label::after {
			transition: width 0.2s linear;
			width: 100%;
		}

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
