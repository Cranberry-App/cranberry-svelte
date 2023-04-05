<script lang="ts">
    import {each} from "svelte/internal";

    export let items: { text: string, url: string }[];
    export let selected: number;
</script>

<html>
    {#each items as item, i}
        <a href="{item.url}" class:selected={i === selected} on:click={() => selected = i}>{item.text}</a>
    {/each}
</html>