<script lang="ts">
    export let disabled: boolean = false;
    export let fullWidth: boolean = false;
</script>

<div on:click class:fullWidth={fullWidth}>
    <span><slot /></span>
</div>

<style lang="scss">
    @use 'src/vars';

    div {
      width: fit-content;
      padding: .75em 2.5em;
      border-radius: 10px;
      text-align: center;
      background-color: vars.$primary-color;

      &.fullWidth {
        width: 100%;
      }

      &:hover {
        cursor: pointer;
        filter: brightness(.9);
      }

      span {
        font-family: vars.$main-font;
        color: vars.$primary-bg-text-color;
      }
    }
</style>