<script>
    import { base } from '$app/paths';
    import { onMount } from 'svelte';
    import BarHorizontal from '../../lib/BarHorizontal.svelte';
    import * as d3 from 'd3';

    let locData = [];

    onMount(async () => {
        locData = await d3.csv(`${base}/loc.csv`, row => ({
            ...row,
            line: Number(row.line),
            length: Number(row.length),
            depth: Number(row.depth)
        }));
    });

    $: data = d3.rollups(
        locData,
        v => d3.sum(v, d => d.length),
        d => d.type
    ).map(([type, count]) => ({ label: type, value: count }));

</script>

<section> 
    <h1>Meta Analysis</h1>
    <BarHorizontal {data} />
</section>