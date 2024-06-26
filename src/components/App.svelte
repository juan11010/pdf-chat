<script>
    import { Alert } from "flowbite-svelte";
    import { APP_STATUS, appStatus } from "../store.ts";
    import Upload from './Upload.svelte'
    import Loading from './Loading.svelte'
    import Chat from './Chat.svelte'
</script>

{#if $appStatus === APP_STATUS.INIT}
    <Upload />
{:else if $appStatus === APP_STATUS.LOADING}
    <Loading />
{:else if $appStatus === APP_STATUS.ERROR}
    <Alert>
        <span class="font-medium">Something went wrong!</span>
        There is an app error
    </Alert>
{:else if $appStatus === APP_STATUS.CHAT_MODE}
    <Chat />
{:else}
    <Alert>
        <span class="font-medium">Unknown action</span>
        This app status is not recognized
    </Alert>
{/if}
