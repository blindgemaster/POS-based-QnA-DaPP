<script>
    import { ethers } from "ethers";
    const contractAddress = "0xf66Cb9feF4f9110C659B71551E17bEDb564956ee";
    export let web3Props = {
        provider: null,
        signer: null,
        account: null,
        chainId: null,
        contract: null,
    };

    $: question = "";

    async function createQuestion() {
        console.log(web3Props.contract);
        const contract = web3Props.contract;

        // Estimate the gas required for the transaction
        const gasEstimate = await contract.estimateGas.postQuestion(question, {
            value: ethers.utils.parseEther("0.00000000000000001"),
        });
        console.log(question);
        // Build the transaction object
        const transactionObject = {
            to: contractAddress,
            value: ethers.utils.parseEther("0.00000000000000001"),
            gasLimit: gasEstimate,
            data: contract.interface.encodeFunctionData("postQuestion", [
                question,
            ]),
        };

        // Sign and send the transaction
        const signedTransaction = await web3Props.signer.sendTransaction(
            transactionObject
        );
        await signedTransaction.wait();

        console.log("Transaction sent");
    }
</script>

<div class="wrapper">
    <span class="input-lable">question: </span>
    <input bind:value={question} />
    <button on:click={createQuestion}>Submit</button>
</div>

<style>
    .wrapper {
        width: 500px;
        margin: 0 auto;
        padding: 10px;
        border: 1px solid black;
        background-color: lightgray;
    }

    .input-lable {
        font-size: 18px;
        margin-bottom: 10px;
    }

    input {
        width: 400px;
        margin-bottom: 10px;
        border: none;
        outline: none;
        background-color: white;
        color: black;
        padding: 10px;
    }

    button {
        background-color: rgb(14, 3, 44);
        color: white;
        padding: 10px;
        border: none;
        cursor: pointer;
    }
</style>
