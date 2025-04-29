<script lang="ts">
	import { onMount } from 'svelte';
	import { currentUser } from '$lib/stores';
	import type { Pet } from '$lib/types';
	import { goto } from '$app/navigation';

	let pets: Pet[] = [];
	let error = '';
	let success = '';

	$: user = $currentUser;

	async function loadPets() {
		if (!user) {
			goto('/login');
			return;
		}

		const res = await fetch('/api/pets');
		if (res.ok) {
			const allPets = await res.json();
			pets = allPets.filter((pet: Pet) => pet.owner === user.id);
		} else {
			error = 'Failed to load pets';
		}
	}

	async function handleAction(petId: number, action: 'feed' | 'toy' | 'return') {
		if (!user) {
			error = 'You need to be logged in to perform this action';
			goto('/login');
			return;
		}

		const res = await fetch('/api/actions', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({
				userId: user.id,
				petId: petId,
				action: action
			})
		});

		if (res.ok) {
			success = `Pet successfully ${action === 'return' ? 'returned' : action + 'ed'}`;
			loadPets();
		} else {
			const errorText = await res.text();
			error = `Failed to ${action} pet: ${errorText}`;
		}
	}

	onMount(() => {
		if (!user) {
			goto('/login');
		} else {
			loadPets();
		}
	});
</script>

<div class="background">
	<div class="container">
		<div class="box">
			<h1>📋 Your Adopted Pets</h1>

			{#if success}<p class="success">{success}</p>{/if}
			{#if error}<p class="error">{error}</p>{/if}

			{#if pets.length === 0}
				<p>You haven't adopted any pets yet.</p>
			{:else}
				<div class="pets-grid">
					{#each pets as pet}
						<div class="pet-card">
							<h3>{pet.name} ({pet.type})</h3>
							<p>Happiness: {pet.happiness}/100</p>
							<p>Hunger: {pet.hunger}/100</p>
							<div class="actions">
								<button on:click={() => handleAction(pet.id, 'feed')}>Feed</button>
								<button on:click={() => handleAction(pet.id, 'toy')}>Play</button>
								<button on:click={() => handleAction(pet.id, 'return')}>Return</button>
							</div>
						</div>
					{/each}
				</div>
			{/if}
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

	.pets-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.pet-card {
		background: #f9f9ff;
		border: 1px solid #ddd;
		border-radius: 12px;
		padding: 1.5rem;
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
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

	.actions {
		display: flex;
		justify-content: center;
		gap: 0.5rem;
		margin-top: 1rem;
	}

	button {
		background-color: #2a52be;
		color: white;
		border: none;
		padding: 0.5rem 1rem;
		border-radius: 8px;
		cursor: pointer;
		transition: background 0.2s ease;
	}

	button:hover {
		background-color: #1e3d8f;
	}
</style>
