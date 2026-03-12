<script>
import projects from "$lib/projects.json";
import Project from "$lib/Project.svelte";
import books from "$lib/reading.json";
import ReadingItem from "$lib/ReadingItem.svelte";
import { onMount } from "svelte";

let githubData = null; // This will eventually hold our Github stats
let loading = true; // This will be true *until* the fetch's promise resolves to a value
let error = null; // If the API call resulted in an error, it will go into this variable

onMount(async () => {
    try {
        console.log("Page has been mounted!")
        let response = await fetch("https://api.github.com/users/kcnakamu");
        console.log(response);
        githubData = await response.json();
        console.log(githubData);
    } catch (err) { // if the "try" block runs into an error, cancel excecution and run this code instead
        error = err;
    }
    loading = false; // don't forget to add this line!
})

</script>

<h1>Karen Nakamura</h1>

<div class="home-top">
    <div class="introduction">
        <p>Hello! My name is Karen, and I am a Junior at MIT studying 6-4.</p>
        <p>I was born and raised in Minnesota, which is known for their lakes and the Mall of America.</p>
        <p> In highschool, I did a lot of cross country skiing because of Minnesota's snow fall, and I enjoy nature.</p>
        <img src="images/mexico.jpg" alt="Picture of me in front of Palacio de Bellas Artes" width="500x">
    </div>
    <div class="reading">
        <h2>What I'm Reading</h2>
        {#each books as b}
            <ReadingItem data={b} />
        {/each}
    </div>
</div>

{#if loading}
    <p>Loading...</p>
{:else if error}
    <p>Something went wrong: {error.message}</p>
{:else}
    <section class="github-stats">
        <h2>My GitHub Stats</h2>
        <dl>
            <dt>Followers</dt>
            <dd>{githubData.followers}</dd>
            <dt>Following</dt>
            <dd>{githubData.following}</dd>
            <dt>Public Repositories</dt>
            <dd>{githubData.public_repos}</dd>
        </dl>
    </section>
{/if}

<h2>Latest Projects</h2>
<div class="projects">
    {#each projects.slice(0, 3) as p}
        <Project data={p} />
    {/each}
</div>

<style>
    .home-top {
        display: flex;
        gap: 2em;
    }

    .reading {
        background-color: lavender;
        padding: 1em;
        min-width: 18em;
        color: black;
    }

    dl {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        padding:24px 0;
    }

    dt {
        grid-row: 1;
        font-size: 15px;
        font-weight: 500;
        margin-bottom:8px;
    }

    dd {
        grid-row: 2;
        font-size: 30px;
        font-weight: 400;
    }
</style>
