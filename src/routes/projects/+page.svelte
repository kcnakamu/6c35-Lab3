<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
</script>
<nav class="Menu">
    <ul>
        <li><a href=".">Home</a></li>
        <li><a href="contact">Contact</a></li>
        <li><a href="projects" class="current">Projects</a></li>
        <li><a href="resume">Resume</a></li>
        <li><a href="https://github.com/kcnakamu" target="_blank">Github Profile</a></li>
    </ul>
</nav>
<h1>{projects.length} Projects</h1>
<div class="projects">
  {#each projects as p}
      <Project data={p} />
  {/each}
</div>

