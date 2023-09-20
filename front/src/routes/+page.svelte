<script>
    import WalletConnect from '$lib/WalletConnect.svelte';
    export let web3Props = {
        provider: null,
        signer: null,
        account: null,
        chainId: null
    };
</script>


<h1>Welcome to Trial</h1>
{#if !web3Props.account}
  <WalletConnect bind:web3Props/>
{:else}
  <p>Your Account: {web3Props.account}</p>
{/if}

