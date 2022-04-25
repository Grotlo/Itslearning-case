<script>
	import { onMount } from "svelte";
	import ListItem from "./components/ListItem.svelte";
	import Modal from "./components/Modal.svelte";

	let courses = [];

	const withoutDatesCourses = [],
		pastCourses = [],
		upcomingCourses = [],
		currentCourses = [];

	const todaysDate = new Date().toJSON();

	const sortIntoLists = (course) => {
		if (course.startDate && course.endDate) {
			if (course.endDate < todaysDate) pastCourses.push(course);
			if (course.startDate < todaysDate && course.endDate > todaysDate)
				currentCourses.push(course);
			if (course.startDate > todaysDate) upcomingCourses.push(course);
		} else {
			withoutDatesCourses.push(course);
		}
	};

	const sortCourses = (courses) =>
		courses.sort((a, b) => a.startDate - b.startDate);

	const filterCourse = (course, searchTerm) =>
		course.name.toUpperCase().indexOf(searchTerm.toUpperCase()) !== -1;

	let menu = [
		{
			entry: "Current",
			list: sortCourses(currentCourses),
			secondList: sortCourses(upcomingCourses),
		},
		{ entry: "Past", list: sortCourses(pastCourses) },
		{ entry: "Without date", list: sortCourses(withoutDatesCourses) },
	];

	$: selectedMenu = menu[0];

	let searchCourses = "",
		courseSelections = [];
	let planModal = false;

	let load = false;
	onMount(async () => {
		const res = await fetch("courses.json");
		const data = await res.json();

		courses = data;

		courses.forEach((course) => sortIntoLists(course));

		load = true;
	});
</script>

<svelte:head>
	<title>Itslearning test case</title>
</svelte:head>

<main>
	<h1>Plans</h1>

	<div>
		<input
			type="radio"
			bind:group={selectedMenu}
			id={menu[0].entry}
			value={menu[0]}
		/>
		<label for={menu[0].entry}>{menu[0].entry}</label>
		<input
			type="radio"
			bind:group={selectedMenu}
			id={menu[1].entry}
			value={menu[1]}
		/>
		<label for={menu[1].entry}>{menu[1].entry}</label>
		<input
			type="radio"
			bind:group={selectedMenu}
			id={menu[2].entry}
			value={menu[2]}
		/>
		<label for={menu[2].entry}>{menu[2].entry}</label>

		<div>
			<img src="icons/search.svg" alt="search icon" />
			<input type="text" placeholder="Search" bind:value={searchCourses} />
		</div>
	</div>

	<div>
		<input type="button" value="Add plan" on:click={() => (planModal = true)} />
		<select>
			<option hidden selected>Actions</option>
			<option>Edit</option>
			<option>Delete</option>
		</select>
	</div>

	{#if (selectedMenu.list || selectedMenu.secondList) && load}
		<ul>
			{#if selectedMenu.list}
				{#each selectedMenu.list.filter( (list) => filterCourse(list, searchCourses) ) as course, index}
					<ListItem {index} {course} bind:courseSelections />
				{/each}
			{/if}

			{#if selectedMenu.secondList}
				<li><span>UPCOMING PLANS</span></li>
				{#each selectedMenu.secondList.filter( (list) => filterCourse(list, searchCourses) ) as course, index}
					<ListItem {index} {course} bind:courseSelections />
				{/each}
			{/if}
		</ul>
	{:else}
		<h2>No courses to view</h2>
	{/if}

	{#if planModal}
		<Modal bind:planModal />
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
		font-weight: var(--w-bold);
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

	input[type="radio"] {
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
		font-weight: var(--w-bold);
	}

	input[type="radio"] + label::after,
	input[type="radio"]:checked + label::after {
		content: "";
		display: block;
		position: absolute;
		height: 0;
		top: 0;
		left: 0;
		border-left: 3px solid #000;
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
		background: var(--btn-green);
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
		padding: 0.5rem 2rem 0.5rem 0;
	}

	/* ul */

	ul {
		padding: 0;
		margin: 0;
	}

	li {
		list-style-type: none;
		text-align: center;
		border-bottom: 1px solid #d4d4d4;
		line-height: 0.1em;
		margin: 2rem 0;
	}

	li > span {
		padding: 0 1rem;
		background: var(--c-primary);
		font-size: 0.75rem;
		font-weight: var(--w-bold);
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
	}
</style>
