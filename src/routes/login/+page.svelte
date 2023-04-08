<script lang="ts">
    import { Button, Label, Input } from 'flowbite-svelte';
    import { firebaseConfig } from "$lib/constants";
    import { initializeApp } from "firebase/app";
    import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut } from "firebase/auth";
    import { getDoc, doc, getFirestore } from "firebase/firestore";
    import { goto } from "$app/navigation";

    let currentForm: null | 'login' | 'signup' = null;

    const app = initializeApp(firebaseConfig);

    const db = getFirestore(app);

    const auth = getAuth();

    signOut(auth);

    function buttonPressed(id) {
        switch (id) {
            case 'login': {
                currentForm = 'login';
            } break;
            case 'signup': {
                currentForm = 'signup';
            } break;
        }
    }

    function login() {
        const email = document.getElementById('login-email') as HTMLInputElement;
        const password = document.getElementById('login-password') as HTMLInputElement;

        signInWithEmailAndPassword(auth, email.value, password.value)
            .then((userCredential) => completeLogin(userCredential))
            .catch((error) => {
                const errorCode = error.code;
                const errorMessage = error.message;
                console.log(error);
                alert(errorMessage);
        });
    }

    function signup() {
        const email = document.getElementById('signup-email') as HTMLInputElement;
        const password = document.getElementById('signup-password') as HTMLInputElement;
        const passwordRepeat = document.getElementById('signup-password-repeat') as HTMLInputElement;

        if (password.value !== passwordRepeat.value) {
            alert('Passwords do not match');
            return;
        }

        createUserWithEmailAndPassword(auth, email.value, password.value)
            .then((userCredential) => completeLogin(userCredential))
            .catch((error) => {
                const errorCode = error.code;
                const errorMessage = error.message;
                console.log(error);
                alert(errorMessage);
            });
    }

    function completeLogin(userCredential) {
        const user = userCredential.user;
        window.location.href = '/';
    }
</script>

<html lang="en">
    <head>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/fork-awesome@1.2.0/css/fork-awesome.min.css" integrity="sha256-XoaMnoYC5TH6/+ihMEnospgm0J1PM/nioxbOUdnM8HY=" crossorigin="anonymous">
        <title>Log into Cranberry</title>
    </head>
    <body>
        <div class="buttons">
            <div class="button-wrapper">
                <Button color="purple" disabled={currentForm === 'login'} on:click={() => { currentForm = 'login' }}>Log in</Button>
            </div>
            <div class="button-wrapper">
                <Button color="purple" disabled={currentForm === 'signup'} on:click={() => { currentForm = 'signup' }}>Sign up</Button>
            </div>
        </div>
        <div class="form login" class:hidden={currentForm !== 'login'}>
            <div class="input-wrapper">
                <Label for="login-email">E-mail</Label>
                <Input id="login-email" placeholder="E-mail"/>
            </div>
            <div class="input-wrapper">
                <Label for="login-password">Password</Label>
                <Input type="password" id="login-password" placeholder="Password" />
            </div>
            <div class="buttonContainer">
                <Button color="purple" on:click={login}>Log in</Button>
            </div>
        </div>
        <div class="form signup" class:hidden={currentForm !== 'signup'}>
            <div class="input-wrapper">
                <Label for="signup-email">E-mail</Label>
                <Input id="signup-email" placeholder="E-mail"/>
            </div>
            <div class="input-wrapper">
                <Label for="signup-password">Password</Label>
                <Input type="password" id="signup-password" placeholder="Password" />
            </div>
            <div class="input-wrapper">
                <Label for="signup-password-repeat">Password</Label>
                <Input type="password" id="signup-password-repeat" placeholder="Repeat password" />
            </div>
            <div class="buttonContainer">
                <Button color="purple" on:click={signup}>Sign up</Button>
            </div>
        </div>
    </body>
</html>

<style lang="scss">
    @use 'src/vars';

    body {
      margin: 50px 0 0;
      padding: 0;
      background-color: vars.$background-color;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      width: 100vw;

      .buttons {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: row;

        .button-wrapper {
          margin: 0 10px;
        }
      }

      .form {
        margin-top: 1%;
        background-color: vars.$background-color;
        display: flex;
        flex-direction: column;
        padding: 2em;
        border-radius: 10px;
        min-width: 40%;

        .buttonContainer {
          margin-top: 10px;
        }
      }
    }
    div.hidden {
      display: none;
    }

</style>
