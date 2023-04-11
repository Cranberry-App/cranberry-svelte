<script lang="ts">
    export let disabled: boolean = false;
    export let placeholder: string;
    export let type: string = "text";
    export let label: string|undefined = undefined;
    export let id: string;
    export let status: "success"|"error"|"default" = "default";
    export let statusText: string|undefined = undefined;
    export let keypressFunction: Function = (...args) => {};
</script>

<div class:error={status === "error"} class:success={status === "success"}>
    {#if label}
        <label for={id}>{label}</label>
    {/if}
    <input type={type} placeholder={placeholder} disabled={disabled} id={id} on:keypress={keypressFunction} />
    {#if status !== "default" && statusText}
        <span class="statusText">{statusText}</span>
    {/if}
</div>

<style lang="scss">
  @use 'src/vars';
    div {
      width: 100%;
      position: relative;
      display: flex;
      flex-direction: column;
      justify-content: center;

      &.error {
        input {
          border-color: red;
        }
        span.statusText {
          color: red;
        }
      }

      &.success {
        input {
          border-color: green;
        }
        span.statusText {
          color: green;
        }
      }

      label {
        font-family: vars.$secondary-font;
        font-weight: 600;
        margin-bottom: 5px;
      }

      input {
        font-size: 1.15em;
        border: solid 1.5px #e0e0e0;
        border-radius: 5px;
        padding: 0.5em 1em;
        font-family: vars.$main-font;

        &:focus {
          outline: solid black 1px;
        }
      }

      span.statusText {
        font-family: vars.$secondary-font;
        margin: 3px 5px;
        font-size: .8em;
      }
    }
</style>