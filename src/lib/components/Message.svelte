<script lang="ts">
    export let to: boolean|undefined, by: boolean|undefined;
    export let author: string;
    export let text: string;
    export let image: string;

    let direction: string;

    if (to) direction = "to";
    else if (by) direction = "by";
    else console.log("Error: Direction can't be to and by simultaneously");

</script>

<div class="message {direction}">
    <div class="imgWrapper">
        <img src={image} alt="The message author's profile picture">
    </div>
    <div class="texts">
        <h1>{author}</h1>
        <span>{text}</span>
    </div>
</div>

<style lang="scss">
  @use 'src/vars';
  $message-color: vars.$primary-color-blend-1;
  div.message {
    display: flex;
    flex-direction: row;
    width: 30em;
    height: fit-content;
    background-color: $message-color;
    align-items: start;
    padding: 10px;
    border-radius: 10px;
    position: relative;
    margin-bottom: 10px;

    .imgWrapper {
      height: 4em;
      width: 4em;
      min-width: 50px;
      margin: 10px;

      img {
        width: 100%;
        height: auto;
        border-radius: 50%;
      }
    }

    .texts {
      display: flex;
      flex-direction: column;
      flex-wrap: wrap;

      h1 {
        padding: 0;
        margin: 0;
        font-family: vars.$main-font;
      }

      span {
        overflow-wrap: break-word;
        font-family: vars.$secondary-font;
      }
    }

    &:after {
      content: '';
      position: absolute;
      top: 50%;
      width: 0;
      height: 0;
      border: 1em solid transparent;
      margin-top: -1em;
    }

    &.to {
      margin-left: 1em;

      &:after {
        left: 0;
        border-right-color: $message-color;
        border-left: 0;
        margin-left: -1em;
      }
    }

    &.by {
      margin-right: 1em;
      margin-left: auto;

      &:after {
        right: 0;
        border-left-color: $message-color;
        border-right: 0;
        margin-right: -1em;
      }
    }
  }
</style>