<script>
    import { ethers } from "ethers";
    export let contractAddress = '';
    export let contractAbi = { abi: null};
    export let web3Props = {
        provider: null,
        signer: null,
        account: null,
        chainId: null,
        contract: null
    };

    $: account1 = "";

    async function connectWallet() {
        let provider = new ethers.providers.Web3Provider(
            window.ethereum,
            "any"
        );
        await provider.send("eth_requestAccounts", []);
        const signer = provider.getSigner();
        const account = await signer.getAddress();
        const chainId = await signer.getChainId();
        const contract = new ethers.Contract(contractAddress, contractAbi.abi, signer)
        
        web3Props = { provider, signer, account, chainId, contract };
        console.log(web3Props);
        account1 = account;
    }
</script>

<button on:click={connectWallet}>Attach Wallet</button>

<style>
    button {
        background-color: #337ab7;
        color: white;
        padding: 10px;
        border: none;
        cursor: pointer;
        font-size: 18px;
        border-radius: 5px;
        margin-bottom: 10px;
    }
</style>
