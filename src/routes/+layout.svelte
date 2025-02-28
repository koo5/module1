<script lang="ts">

    import {onMount} from "svelte";
    import Header from './Header.svelte';
    import '../app.css';
    import {SvelteMap} from "svelte/reactivity";

    let {children} = $props();
    let messages = $state([]);
    let accounts = $state([]);
    let requests = $state(new SvelteMap());
    let host;

    onMount(() => {
        window.addEventListener('message', (event) => {
            //if (event.origin !== window.location.origin) return; // Validate origin
            console.log('child received message: ', event);
            host = event.source;
            messages.push(event.data);
            if (event.data.guid) {
                let resolve = requests.get(event.data.guid);
                if (resolve) {
                    resolve(event.data);
                    requests.delete(event.data.guid);
                }
            }
        });
    });

    async function send(msg) {
        msg.guid = Math.random().toString(36).substring(7);
        console.log('send: ', msg);
        await new Promise((resolve) => {
            requests.set(msg.guid, resolve);
            window.parent.postMessage(msg, '*');
        });
    }


</script>

<div class="app">
    <Header/>

    <div>
        <button onclick={
        () => {
		 send(
             {type: 'server_command', account: '0.3bojf1djrke0.832yrbdygz0.9kawulbu23t0.5qvh27b9gd5', command: 'test'}
             )
        }} >Send message to parent </button>
        <div>
            messages:
            <ol>
            {#each messages as message}
                <li>{(typeof message)}: {JSON.stringify(message)}</li>
            {/each}
                </ol>
        </div>

    </div>

    <main>
        {@render children()}
    </main>

    <footer>
        <p>
            visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to learn about SvelteKit
        </p>
    </footer>
</div>

<style>
    .app {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
    }

    main {
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: 1rem;
        width: 100%;
        max-width: 64rem;
        margin: 0 auto;
        box-sizing: border-box;
    }

    footer {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        padding: 12px;
    }

    footer a {
        font-weight: bold;
    }

    @media (min-width: 480px) {
        footer {
            padding: 12px 0;
        }
    }
</style>
