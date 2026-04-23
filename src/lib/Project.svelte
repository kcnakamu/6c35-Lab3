<script>
  export let data = {};
  import { base } from "$app/paths";
</script>

<article>
  {#if data.link}
    <h2>
      <a href={data.link} target="_blank" rel="noopener noreferrer" class="title-link">
        {data.title}
      </a>
    </h2>
  {:else}
    <h2>{data.title}</h2>
  {/if}

  {#if data.link}
    <a href={data.link} target="_blank" rel="noopener noreferrer">
      <img src="{base}/{data.image}" alt="Project Image">
    </a>
  {:else}
    <img src="{base}/{data.image}" alt="Project Image">
  {/if}

  <p class="proj-info">
    <span class="year">{data.year} |</span>
    {data.description}
  </p>
</article>

<style>
img {
  max-width: 100%;
}

article {
  display: grid;
  grid-template-rows: subgrid;
  grid-row: span 4;
}

h2 {
  margin: 0;
}

.year {
  font-weight: bold;
}

.proj-info {
  margin: 0;
}
</style>