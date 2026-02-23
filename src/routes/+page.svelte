<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import books from "$lib/reading.json";
  import ReadingItem from "$lib/ReadingItem.svelte";
</script>
<nav class="Menu">
    <ul>
        <li><a href="." class="current">Home</a></li>
        <li><a href="contact">Contact</a></li>
        <li><a href="projects">Projects</a></li>
        <li><a href="resume">Resume</a></li>
        <li><a href="https://github.com/kcnakamu" target="_blank">Github Profile</a></li>
    </ul>
</nav>

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
    }
</style>
