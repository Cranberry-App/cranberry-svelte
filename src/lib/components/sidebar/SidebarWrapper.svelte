<ul>
    <slot />
</ul>

<style lang="scss">
  @use 'src/vars';

    ul {
      width: 15vw;
      height: calc(100vh - 2 * 1em);
      margin: 0;
      background-color: vars.$primary-color-blend-2;
      list-style-type: none;
      padding: 1em;
    }
</style>

<script lang="ts">

</script>