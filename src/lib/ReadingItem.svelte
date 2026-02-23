<script>
  export let data = {};
</script>

<article>
    <img src={data.image} alt={data.title}>
    <div class="details">
        <h3>{data.title}</h3>
        <p class="author">{data.author}</p>
    </div>
</article>

<style>
article {
    display: flex;
    gap: 1em;
    align-items: left;
    padding: 5px;
  }

img {
  width: 5em;
}

h3 {
    margin: 0;
}

.author {
    margin: 0;
    font-style: italic;
}


</style>