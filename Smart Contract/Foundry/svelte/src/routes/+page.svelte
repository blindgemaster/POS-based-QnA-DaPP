<script>
    import WalletConnect from "$lib/WalletConnect.svelte";
    import CreateQuestion from "../lib/CreateQuestion.svelte";
    import contractAbi from "../contracts/Quiz.json";
    import QuestionList from "../lib/QuestionList.svelte";
    const contractAddress = "0xf66Cb9feF4f9110C659B71551E17bEDb564956ee";
    export let web3Props = {
        provider: null,
        signer: null,
        account: null,
        chainId: null,
        contract: null,
    };
</script>

<h1>Welcome to Trial</h1>
{#if !web3Props.account}
    <WalletConnect bind:web3Props {contractAddress} {contractAbi} />
{:else}
    <p1>Connected Account : {web3Props.account}</p1>
{/if}
<CreateQuestion bind:web3Props />
<QuestionList bind:web3Props />

<style>
    body {
        background-color: #410505;
        font-family: Roboto, sans-serif;
        font-size: 16px;
        color: #333;
        margin: 0;
        padding: 0;
    }
    h1 {
        font-size: 60px;
        font-weight: 700;
        line-height: 1.2;
        color: rgb(255, 255, 255);
        background-color: #000000;
        margin-top: 0;
        margin-bottom: 10px;
        padding: 10px 20px;
        border-radius: 5px;
        text-align: center;
    }
    .text-center {
        text-align: center;
    }
    .text-white {
        color: #ffffff;
    }
    .bg-primary {
        background-color: #181818;
    }
    .py-5 {
        padding-top: 5rem;
    }
    .mt-5 {
        margin-top: 5rem;
    }
    p1 {
        font-family: Roboto;
        line-height: 1.5;
        border: 1px solid #ccc;
        box-shadow: 0 1px 3px 0px rgba(00, 00, 00, 0.5);
    }
</style>
