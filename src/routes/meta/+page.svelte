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


    let rawData = [];
    let data = [];

    onMount(async () => {
        rawData = await d3.json('/lab6_example.json');
        data = d3.rollups(
            rawData,
            v => d3.sum(v, d => d.lines),
            d => d.language
        ).map(([language, lines]) => ({ label: language, value: lines }));
    });
</script>

<section> 
    <h1>Meta Analysis</h1>
    <BarHorizontal {data} />
</section>