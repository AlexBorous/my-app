<script context="module">
	export async function load({ fetch, params }) {
		const response = await fetch(
			`https://api.themoviedb.org/3/movie/${params.id}?api_key=${
				import.meta.env.VITE_API_KEY
			}&language=en-US`
		);
		const movieDetail = await response.json();
		if (response.ok) {
			return {
				props: { movieDetail }
			};
		}
		return {};
	}
</script>

<script>
	export let movieDetail;
	import { fly } from 'svelte/transition';
</script>

<div
	class="movie-details"
	in:fly={{ y: 50, duration: 500, delay: 500 }}
	out:fly={{ duration: 500 }}
>
	<div class="img-container">
		<img
			src={'https://image.tmdb.org/t/p/original' + movieDetail.backdrop_path}
			alt={movieDetail.title}
		/>
	</div>
	<div class="txt-container">
		<h1>{movieDetail.title}</h1>
		<p class="overview">{movieDetail.overview}</p>
		<p>
			<span>Release date:</span>
			{movieDetail.release_date}<br />
			<span>Budget:</span>
			{movieDetail.budget} $<br />
			<span>Rating:</span>
			{movieDetail.rating != undefined ? movieDetail.rating : '0'} <br />
			<span>Runtime:</span>
			{movieDetail.runtime} mins <br />
		</p>
	</div>
</div>

<style>
	h1 {
		padding: 1rem 0rem 2rem;
	}
	p {
		padding: 1rem 0rem;
	}
	.img-container {
		width: 100%;
	}
	img {
		width: 100%;
		border-radius: 1rem;
	}
	.movie-details {
		margin: 2rem 20%;
	}
	span {
		font-weight: bold;
	}
</style>
