<script lang="ts">
    import Input from "$lib/components/Input.svelte";
    import Button from "$lib/components/Button.svelte";
    import SidebarWrapper from "$lib/components/sidebar/SidebarWrapper.svelte";
    import SidebarComponent from "$lib/components/sidebar/SidebarComponent.svelte";
    import Message from "$lib/components/Message.svelte";
    import {firebaseConfig} from "$lib/constants";
    import {initializeApp} from "firebase/app";
    import {addDoc, collection, getDocs, getFirestore, onSnapshot, query, Timestamp, where} from "firebase/firestore";
    import {getAuth, onAuthStateChanged} from "firebase/auth";
    import {onMount, afterUpdate} from "svelte";

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    const auth = getAuth(app);

    let messages = null;
    let currentGroup;
    let unsubscribe = () => {};

    function ready(): Promise<void> {
        return new Promise((resolve, reject) => {
            onMount(() => {
                onAuthStateChanged(auth, user => {
                    if (!user) {
                        reject();
                        try {
                            window.location.href = "/login";
                        } catch (error) {
                            console.log(error);
                        }
                    } else {
                        resolve();
                    }
                });
            });
        });
    }

    const scrollToBottom = async (node) => {
        node.scroll({ top: node.scrollHeight, behavior: 'smooth' });
    };

    afterUpdate(() => {
        scrollToBottom(document.querySelector('.messages'));
    });

    async function getGroups(): Promise<{id: string, name: string}[]> {
        let groups = [];
        let groupsQuerySnapshot = await getDocs(query(collection(db, 'groups'), where('members', 'array-contains', auth.currentUser.uid)));
        for (let doc of groupsQuerySnapshot.docs) {
            groups.push({
                id: doc.id,
                name: doc.data().name
            });
        }
        return groups;
    }

    async function selectGroup(groupID): Promise<{content: string, author: string, timestamp: Timestamp, byMe: boolean}[]> {
        unsubscribe();
        currentGroup = groupID;
        unsubscribe = onSnapshot(query(collection(db, 'groups', groupID, 'messages')), async querySnapshot => {
            messages = await loadMessages(querySnapshot);
        });
        async function loadMessages(querySnapshot) {
            if (querySnapshot.empty) return [];
            let messages = [], authors = [];
            for (let doc of querySnapshot.docs) {
                if (!authors.includes(doc.data().author)) authors.push(doc.data().author);
                messages.push({
                    content: doc.data().content,
                    author: doc.data().author,
                    timestamp: doc.data().timestamp,
                    byMe: doc.data().author == auth.currentUser.uid,
                });
            }
            let authorsDocs = await getDocs(query(collection(db, 'users'), where('__name__', 'in', authors)));
            let authorNamesAndAvatars = [];
            for (let doc of authorsDocs.docs) {
                authorNamesAndAvatars.push([doc.id, {name: doc.data().name, avatar: doc.data().avatar ? doc.data().avatar : "https://cdn.pixabay.com/photo/2015/10/05/22/37/blank-profile-picture-973460_960_720.png"}]);
            }
            for (const message of messages) {
                for (const author of authorNamesAndAvatars) {
                    if (message.author == author[0]) {
                        message.author = author[1].name;
                        message.image = author[1].avatar
                    }
                }
            }
            messages.sort((a, b) => a.timestamp.seconds - b.timestamp.seconds);
            return messages;
        }
    }

    function sendMessage() {
        addDoc(collection(db, `/groups/${currentGroup}/messages/`), {
            content: (document.querySelector('#message-input') as HTMLInputElement).value,
            author: auth.currentUser.uid,
            timestamp: Timestamp.now()
        });
    }
</script>

<html lang="en">
    <head>
        <title>Cranberry</title>
    </head>
    <body>
        {#await ready() then promise}
            <SidebarWrapper>
                {#await getGroups()}
                    <p>Loading Groups...</p>
                    {:then groups}
                        {#each groups as group}
                            <SidebarComponent text={group.name} on:click={() => {selectGroup(group.id)}} />
                        {/each}
                    {:catch error}
                        <p>Error: {error}</p>
                {/await}
            </SidebarWrapper>
            <div class="chat">
                <div class="messages">
                    {#if messages === null}
                        <div class="loader">
                            <img src="/src/lib/assets/loader.svg" alt="A loading spinner">
                            <span>Please select a group</span>
                        </div>
                        {:else if messages.length === 0}
                            <div class="loader">
                                <img src="/src/lib/assets/loader.svg" alt="A loading spinner">
                                <span>No messages yet</span>
                            </div>
                        {:else}
                            {#each messages as message}
                                <Message by={message.byMe} to={!message.byMe} author={message.author} text={message.content} image={message.image} />
                            {/each}
                    {/if}
                </div>
                <div class="inputs">
                    <div class="input-wrapper">
                        <Input placeholder="Enter a message" id="message-input" keypressFunction={(e) => {
                            if (e.key === "Enter") {
                                sendMessage();
                                e.target.value = "";
                            }
                        }} />
                    </div>
                    <div class="button-wrapper">
                        <Button on:click={() => {sendMessage()}} >Send</Button>
                    </div>
                </div>
            </div>
        {/await}
    </body>
</html>

<style lang="scss">
  @use 'src/vars';
    body {
      padding: 0;
      margin: 0;
      display: flex;
      flex-direction: row;
      width: 100vw;
      height: 100vh;

      .chat {
        display: flex;
        flex-direction: column;
        flex: 1;
        margin: 1px 0;

        .messages {
          flex: 1;
          overflow-x: hidden;
          overflow-y: scroll;

          div.loader {
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;

            img {
              animation: rotate infinite 3s;
              width: 5em;
              height: auto;
            }

            span {
              font-family: vars.$main-font;
            }
          }
        }

        .inputs {
          display: flex;
          flex-direction: row;

          .input-wrapper {
            flex: 1;
          }

          .button-wrapper {
            margin: 0 10px;
          }
        }
      }
    }

    @keyframes rotate {
      0% {
        transform: rotate(0deg);
      }
      25% {
        transform: rotate(90deg);
      }
      50% {
        transform: rotate(180deg);
      }
      75% {
        transform: rotate(270deg);
      }
      100% {
        transform: rotate(360deg);
      }
    }
</style>