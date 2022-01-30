<script>
	import { onMount } from 'svelte';
	import auth from '$lib/services/auth';
	import { isAuthenticated, user } from '$lib/stores/auth';
	import config from '$lib/config/auth_config';
	let auth0Client;

	onMount(async () => {
		auth0Client = await auth.createClient();
		isAuthenticated.set(await auth0Client.isAuthenticated());
		user.set(await auth0Client.getUser());
	});

	async function login() {
		await auth.loginWithPopup(auth0Client);
		console.log(auth0Client);
		const user = await auth0Client.getUser();
		console.log(user);
		const claims = await auth0Client.getIdTokenClaims();
		console.log(claims);
		const access_tokent = await	auth0Client.getTokenSilently();
		doPost(access_tokent);
	}

	async function doPost(access_token) {
		const res = await fetch(config.backendUrl, {
			method: 'POST',
			body: JSON.stringify({
				item: 'item'
			}),
			headers: {
				Authorization: 'Bearer ' + access_token
			}
		});

		const text = await res.text();

		console.log(text);
	}

	function logout() {
		auth.logout(auth0Client);
	}
</script>

<nav>
	<a href="/">MovieRama</a>
	{#if $isAuthenticated}
		<h3 style="margin-left:30vw;">Hey {$user.name}!</h3>

		<img
			style="margin-left:20vw;border-radius: 20px; float:right; max-width:5rem ; height:fit-content"
			src={$user.picture}
			alt={user.name}
		/>

		<button on:click={logout}>Logout</button>
	{:else}
		<button on:click={login}>Login</button>
	{/if}
</nav>

<style>
	nav {
		display: flex;
		min-height: 10vh;
		align-items: center;
		justify-content: space-between;
	}
	a {
		font-size: 1rem;
		font-weight: bold;
		font-family: 'Poppins';
		color: black;
		text-decoration: none;
	}
	button {
		font-size: 0.7rem;
		padding: 0.5rem 1.5em;
		background: rgb(96, 110, 201);
		color: white;
		font-weight: bold;
		border: none;
		height: 50%;
		border-radius: 15px;
		cursor: pointer;
	}
</style>
