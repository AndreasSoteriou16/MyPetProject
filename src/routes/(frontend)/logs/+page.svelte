<script lang="ts">
	import { onMount } from 'svelte';
	let logs: string[] = [];
	let error = '';

	onMount(async () => {
		const res = await fetch('/api/log');
		if (res.ok) {
			logs = await res.json();
		} else {
			error = 'Failed to load logs';
		}
	});
</script>

<div class="background">
	<div class="container">
		<div class="box">
			<h1>📜 Action Log</h1>

			{#if error}
				<p class="error">{error}</p>
			{:else if logs.length === 0}
				<p>No actions have been logged yet.</p>
			{:else}
				<ul class="log-list">
					{#each logs as log}
						<li>{log}</li>
					{/each}
				</ul>
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
		max-width: 800px;
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

	.error {
		color: red;
		font-weight: bold;
		margin-bottom: 1rem;
	}

	.log-list {
		list-style: none;
		padding: 0;
		text-align: left;
		margin-top: 2rem;
	}

	.log-list li {
		background: #f9f9ff;
		margin-bottom: 0.8rem;
		padding: 1rem;
		border-radius: 8px;
		box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
		font-size: 1rem;
		color: #333;
	}
</style>
