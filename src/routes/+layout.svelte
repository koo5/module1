<script lang="ts">

    import {onMount} from "svelte";
    import Header from './Header.svelte';
    import '../app.css';

    let {children} = $props();
    let messages = $state([]);

    onMount(() => {
        window.addEventListener('message', (event) => {
            //if (event.origin !== window.location.origin) return; // Validate origin
            messages.push(event.data);
        });
    });


</script>

<div class="app">
    <Header/>

    <div>
        <button onclick={() => {
		window.parent.postMessage({cmd: 'core.ping', data: 'ping from child'},
		'*');
	}}>Send message to parent
        </button>
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
