<script lang="ts">
    export let items: { text: string, url: string }[];
    export let right: { name: string, image: string, items: { text: string, url: string }[]};
    let menuHidden = true;
</script>

<html lang="en">
    <ul id="navbar">
        {#each items as item}
            <li on:click={window.location.href = item.url}><a>{item.text}</a></li>
        {/each}

        {#if right && right.name === 'menu'}
            <li class="right" on:click={() => menuHidden = !menuHidden}>
                <img src={right.image} alt="Profile image">
                <ul class="menu" class:hidden={menuHidden}>
                    {#each right.items as item}
                        <li class="menu-item" on:click={window.location.href = item.url}><a>{item.text}</a></li>
                    {/each}
                </ul>
            </li>
        {/if}
    </ul>
</html>

<style lang="scss">
    @use 'src/vars';

    ul#navbar {
      background-color: vars.$background-color;
      list-style-type: none;
      margin: 0;
      display: flex;
      flex-direction: row;
      padding: 0;
      user-select: none;

      li {
        padding: 10px 20px;
        display: flex;
        align-items: center;
        justify-content: center;

        &:hover {
          background-color: vars.$background-color-light;
          cursor: pointer;
        }

        a {
          color: white;
          text-decoration: none;
          height: 100%;
          width: 100%;
          font-family: vars.$ubuntu;
          font-size: 1.15rem;
        }

        &.right {
          padding: 5px 5px;
          margin-left: auto;

          &:hover {
            background-color: transparent;
          }

          img {
            height: 2em;
            border-radius: 50%;
          }
        }
      }
    }

    ul.menu {
      position: absolute;
      top: 2.6em;
      right: 0;
      background-color: #aa0022;
      list-style-type: none;
      margin: 0;
      display: flex;
      flex-direction: column;
      padding: 0;
      user-select: none;
      z-index: 1;

      li.menu-item {
        padding: 10px 20px;
        display: flex;
        align-items: center;
        justify-content: center;

        &:hover {
          background-color: vars.$background-color-light;
          cursor: pointer;
        }

        a {
          color: white;
          text-decoration: none;
          height: 100%;
          width: 100%;
          font-family: vars.$ubuntu;
          font-size: 1.15rem;
        }
      }

      &.hidden {
        display: none;
      }
    }
</style>