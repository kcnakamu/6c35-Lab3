<script>
  export let data = {};
  import { base } from "$app/paths";
</script>

<article>
    <h2>{data.title}</h2>
    <img src="{base}/{data.image}" alt="Project Image">
    <p>{data.description}</p>
</article>

<style>
img {
  max-width: 100%;
}

article {
  display: grid;
  grid-template-rows: subgrid;
  grid-row: span 3;
}

h2 {
  margin: 0;
}

</style>