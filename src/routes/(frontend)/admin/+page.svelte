<script lang="ts">
	import { onMount } from 'svelte';
	import { currentUser } from '$lib/stores';
	import { goto } from '$app/navigation';

	let name = 'New Pet';
	let type = 'puppy';
	let error = '';
	let success = '';

	$: user = $currentUser;

	onMount(() => {
		if (!user) {
			goto('/login');
			return;
		}

		if (!user.isAdmin) {
			goto('/');
			return;
		}
	});

	async function addPet() {
		if (!user || !user.isAdmin) {
			error = 'You need admin permission to add pets';
			return;
		}

		const pet = {
			id: Date.now(),
			name,
			type,
			hunger: 50,
			happiness: 50,
			adoptionStatus: false,
			owner: null
		};

		const res = await fetch('/api/pets', {
			method: 'POST',
			body: JSON.stringify(pet),
			headers: {
				'Content-Type': 'application/json'
			}
		});

		if (res.ok) {
			success = 'Pet added successfully!';
			name = 'New Pet';
			type = 'puppy';
		} else {
			const errorText = await res.text();
			error = errorText || 'Failed to add pet';
		}
	}
</script>

<div class="background">
	<div class="container">
		<div class="box">
			<h1>🐾 Add a New Pet</h1>

			{#if success}<p class="success">{success}</p>{/if}
			{#if error}<p class="error">{error}</p>{/if}

			<form on:submit|preventDefault={addPet}>
				<div class="form-group">
					<label for="name">Pet Name</label>
					<input id="name" type="text" bind:value={name} required />
				</div>

				<div class="form-group">
					<label for="type">Pet Type</label>
					<select id="type" bind:value={type}>
						<option value="puppy">Puppy</option>
						<option value="kitten">Kitten</option>
					</select>
				</div>

				<button type="submit">Add Pet</button>
			</form>
		</div>
	</div>
</div>

<style>
	.background {
		background: linear-gradient(135deg, #6fb1fc, #4364f7);
		min-height: 100vh;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 2rem;
	}

	.container {
		width: 100%;
		max-width: 600px;
	}

	.box {
		background: #ffffff;
		border-radius: 16px;
		box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
		padding: 3rem 2rem;
		text-align: center;
	}

	h1 {
		color: #2a52be;
		margin-bottom: 2rem;
		font-size: 2.5rem;
	}

	form {
		display: flex;
		flex-direction: column;
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.form-group {
		text-align: left;
	}

	label {
		font-weight: bold;
		margin-bottom: 0.5rem;
		display: block;
		color: #333;
	}

	input, select {
		width: 100%;
		padding: 0.75rem;
		border-radius: 8px;
		border: 1px solid #ccc;
		font-size: 1rem;
	}

	button {
		background-color: #2a52be;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 10px;
		font-size: 1.1rem;
		cursor: pointer;
		width: 100%;
		transition: background 0.3s ease;
	}

	button:hover {
		background-color: #1e3d8f;
	}

	.success {
		color: green;
		font-weight: bold;
		margin-bottom: 1rem;
	}

	.error {
		color: red;
		font-weight: bold;
		margin-bottom: 1rem;
	}
</style>
