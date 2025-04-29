<script lang="ts">
	import { currentUser } from '$lib/stores';

	$: user = $currentUser;
</script>

<div class="background">
	<div class="container">
		<div class="box">
			<h1>🐾 Welcome to My Pet Shop</h1>

			{#if user}
				<p>Welcome!</p>

				<div class="buttons">
					<a class="button" href="/pets">Adopt a Pet</a>
					<a class="button" href="/shop">Visit the Shop</a>
				</div>
			{:else}
				<p>Please <a class="login-link" href="/login">log in</a> to adopt a pet or visit the shop.</p>
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
	}

	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 100%;
		padding: 2rem;
	}

	.box {
		background: #ffffff;
		border-radius: 16px;
		box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
		padding: 3rem 2rem;
		text-align: center;
		max-width: 500px;
		width: 100%;
	}

	h1 {
		color: #2a52be;
		margin-bottom: 1rem;
		font-size: 2.5rem;
	}

	p {
		color: #555;
		margin-bottom: 2rem;
		font-size: 1.2rem;
	}

	.buttons {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.button {
		background-color: #2a52be;
		color: white;
		padding: 0.75rem 1.5rem;
		text-decoration: none;
		font-size: 1.1rem;
		border-radius: 10px;
		transition: background 0.3s ease;
	}

	.button:hover {
		background-color: #1e3d8f;
	}

	.login-link {
		color: #2a52be;
		font-weight: bold;
		text-decoration: underline;
	}
</style>
