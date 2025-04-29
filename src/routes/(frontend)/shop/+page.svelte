<script lang="ts">
	import { currentUser } from '$lib/stores';

	let user = $currentUser;
	let message = '';

	const prices = {
		food: 10,
		toy: 15,
		treat: 5
	};

	async function buy(item: 'food' | 'toy' | 'treat') {
		if (!user) {
			message = 'Please login to buy items.';
			return;
		}

		const res = await fetch('/api/shop', {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify({ userId: user.id, item, cost: prices[item] })
		});

		if (res.ok) {
			message = `${item} purchased!`;
			user.budget -= prices[item];
			user.inventory[item] = (user.inventory[item] || 0) + 1;
		} else {
			message = await res.text();
		}
	}
</script>

<div class="background">
	<div class="container">
		<div class="box">
			<h1>🛍️ Pet Shop</h1>

			{#if user}
				<div class="info">
					<p><strong>Budget:</strong> ${user.budget}</p>
					<p><strong>Inventory:</strong> 🥩 {user.inventory.food} 🎾 {user.inventory.toy} 🍬 {user.inventory.treat}</p>
				</div>

				<div class="shop-buttons">
					<button on:click={() => buy('food')}>Buy Food - ${prices.food}</button>
					<button on:click={() => buy('toy')}>Buy Toy - ${prices.toy}</button>
					<button on:click={() => buy('treat')}>Buy Treat - ${prices.treat}</button>
				</div>
			{:else}
				<p>Please login to see your budget and inventory.</p>
			{/if}

			{#if message}
				<p class="message">{message}</p>
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

	.info {
		font-size: 1.2rem;
		margin-bottom: 2rem;
		color: #333;
	}

	.shop-buttons {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		margin-top: 1rem;
	}

	button {
		background-color: #2a52be;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 10px;
		font-size: 1.1rem;
		cursor: pointer;
		transition: background 0.2s ease;
	}

	button:hover {
		background-color: #1e3d8f;
	}

	.message {
		margin-top: 1.5rem;
		color: green;
		font-weight: bold;
	}
</style>
