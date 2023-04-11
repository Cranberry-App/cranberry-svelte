<script lang="ts">
    export let text: string;
</script>

<li on:click>
    <span>{text}</span>
</li>

<style lang="scss">
  @use 'src/vars';

    li {
      padding: .5em;
      border-radius: 10px;

      &:hover {
        background-color: vars.$primary-color-blend-1;
        cursor: pointer;
      }

      span {
        font-family: vars.$main-font;
        font-size: 1.1em;
      }
    }
</style>