<script lang="ts">
	import { currentUser } from '$lib/stores';
	import { goto } from '$app/navigation';

	let name = '';
	let password = '';
	let error = '';

	async function handleLogin() {
		const res = await fetch('/api/auth/login', {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify({ name, password })
		});

		if (res.ok) {
			const user = await res.json();
			currentUser.set(user);
			goto('/dashboard');
		} else {
			const errorText = await res.text();
			error = errorText || 'Login failed. Please check your credentials.';
		}
	}
</script>

<div class="background">
	<div class="container">
		<div class="box">
			<h1>🔑 Login</h1>

			<form on:submit|preventDefault={handleLogin}>
				<div class="form-group">
					<label for="name">Username</label>
					<input id="name" type="text" bind:value={name} required />
				</div>

				<div class="form-group">
					<label for="password">Password</label>
					<input id="password" type="password" bind:value={password} required />
				</div>

				<button type="submit">Login</button>

				{#if error}
					<p class="error">{error}</p>
				{/if}

				<p class="link-text">
					Don't have an account? <a href="/register">Register here</a>.
				</p>
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
		max-width: 400px;
		width: 100%;
	}

	h1 {
		color: #2a52be;
		margin-bottom: 1.5rem;
		font-size: 2.2rem;
	}

	form {
		display: flex;
		flex-direction: column;
		gap: 1.2rem;
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

	input {
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
		margin-top: 1rem;
		transition: background 0.3s ease;
	}

	button:hover {
		background-color: #1e3d8f;
	}

	.error {
		color: red;
		font-size: 0.95rem;
		margin-top: 1rem;
	}

	.link-text {
		margin-top: 1.5rem;
		font-size: 0.95rem;
	}

	a {
		color: #2a52be;
		text-decoration: underline;
	}
</style>
