<script lang="ts">
	import { onMount } from 'svelte';
	import { currentUser } from '$lib/stores';
	import type { Pet } from '$lib/types';
	import { goto } from '$app/navigation';

	let pets: Pet[] = [];
	let filteredPets: Pet[] = [];
	let selectedType = '';
	let error = '';
	let success = '';

	$: user = $currentUser;
	$: filteredPets = selectedType
		? pets.filter(pet => pet.type === selectedType)
		: pets;

	onMount(() => {
		loadPets();
	});

	async function loadPets() {
		const res = await fetch('/api/pets');
		if (res.ok) {
			pets = await res.json();
		} else {
			error = 'Failed to load pets';
		}
	}

	async function adoptPet(petId: number) {
		if (!user) {
			goto('/login');
			return;
		}

		const res = await fetch('/api/adopt', {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify({ userId: user.id, petId })
		});

		if (res.ok) {
			success = 'Pet adopted successfully!';
			loadPets();
		} else {
			const errorText = await res.text();
			error = errorText || 'Failed to adopt pet';
		}
	}
</script>

<div class="background">
	<div class="container">
		<div class="box">
			<h1>🐾 Available Pets</h1>

			{#if success}<p class="success">{success}</p>{/if}
			{#if error}<p class="error">{error}</p>{/if}

			<div class="filter-buttons">
				<button class:selected={selectedType === ''} on:click={() => selectedType = ''}>All</button>
				<button class:selected={selectedType === 'puppy'} on:click={() => selectedType = 'puppy'}>Puppies</button>
				<button class:selected={selectedType === 'kitten'} on:click={() => selectedType = 'kitten'}>Kittens</button>
			</div>

			<div class="pets-grid">
				{#each filteredPets as pet}
					<div class="pet-card">
						<h3>{pet.name} ({pet.type})</h3>
						<p>Happiness: {pet.happiness}/100</p>
						<p>Hunger: {pet.hunger}/100</p>
						<p>Status: {pet.owner === null ? 'Available' : 'Adopted'}</p>

						{#if user && !pet.owner}
							<button on:click={() => adoptPet(pet.id)}>Adopt</button>
						{:else}
							<p><i>Already adopted</i></p>
						{/if}
					</div>
				{/each}
			</div>
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
		max-width: 1200px;
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

	.filter-buttons {
		display: flex;
		justify-content: center;
		gap: 1rem;
		margin-bottom: 2rem;
		flex-wrap: wrap;
	}

	button {
		background-color: #e0e7ff;
		color: #333;
		border: none;
		padding: 0.6rem 1.2rem;
		border-radius: 8px;
		font-size: 1rem;
		cursor: pointer;
		transition: background 0.2s ease;
	}

	button:hover {
		background-color: #b3c7ff;
	}

	button.selected {
		background-color: #2a52be;
		color: white;
	}

	.pets-grid {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.pet-card {
		background: #f9f9ff;
		border: 1px solid #ddd;
		border-radius: 12px;
		padding: 1.5rem;
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
		text-align: center;
	}

	.pet-card h3 {
		color: #333;
		margin-bottom: 0.5rem;
	}

	.pet-card p {
		color: #555;
		font-size: 1rem;
		margin: 0.25rem 0;
	}

	.pet-card button {
		background-color: #2a52be;
		color: white;
		margin-top: 1rem;
		padding: 0.6rem 1.2rem;
		border-radius: 8px;
		font-size: 1rem;
		border: none;
		cursor: pointer;
		transition: background 0.2s ease;
	}

	.pet-card button:hover {
		background-color: #1e3d8f;
	}
</style>
