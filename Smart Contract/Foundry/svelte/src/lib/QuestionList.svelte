<script>
    import { onMount } from "svelte";
    import { ethers } from "ethers";

    const contractAddress = "0xf66Cb9feF4f9110C659B71551E17bEDb564956ee";

    export let web3Props = {
        provider: null,
        signer: null,
        account: null,
        chainId: null,
        contract: null,
    };

    let questions = [];
    let answerInputs = []; // Array to store answer inputs for each question
    let questionPots = [];

    async function loadQuestions() {
        const { contract } = web3Props;

        const questionCount = await web3Props.contract.getQuestionCount();

        questions = [];
        answerInputs = []; // Clear the existing answer inputs

        for (let i = 0; i < questionCount.toNumber(); i++) {
            const questionText = await web3Props.contract.getQuestion(i);
            getQuestionPot(i);
            questions.push({
                questionText: questionText.toString(),
                answer: "",
            });
            answerInputs.push(""); // Initialize answer input for each question
        }

        questions = questions;
    }

    async function handleLoadQuestions() {
        console.log(web3Props);
        questions = []; // Clear the existing questions
        await loadQuestions();
    }

    async function addAnswer(questionIndex) {
        console.log(web3Props.contract);
        const contract = web3Props.contract;
        const newAnswer = answerInputs[questionIndex]; // Get the answer input for the specific question

        // Estimate the gas required for the transaction
        const gasEstimate = await contract.estimateGas.addAnswer(questionIndex, newAnswer, {
            value: ethers.utils.parseEther("0.00000000000000001"),
        });
        // Build the transaction object
        const transactionObject = {
            to: contractAddress,
            value: ethers.utils.parseEther("0.00000000000000001"),
            gasLimit: gasEstimate,
            data: contract.interface.encodeFunctionData("addAnswer", [
                questionIndex, newAnswer,
            ]),
        };

        // Sign and send the transaction
        const signedTransaction = await web3Props.signer.sendTransaction(
            transactionObject
        );
        await signedTransaction.wait();

        console.log("Transaction sent");

        answerInputs[questionIndex] = ""; // Clear the answer input for the specific question

        await loadQuestions();
    }
    async function distributePot(questionIndex) {
        const contract = web3Props.contract;

        // Call the smart contract function
        await contract.distributePot(questionIndex);

        await loadQuestions();
    }
    async function getQuestionPot(index) {
        const contract = web3Props.contract;

        // Call the smart contract function
        console.log("pot")
        const pot = await contract.getQuestionPot(index);
        console.log(pot)

        // Update the question pot value
        questionPots[index] = pot.toNumber();
    }
    questionPots = questionPots;
</script>

<div>
    <button on:click={handleLoadQuestions}>Load Questions</button>

    {#each questions as question, index}
        <div class="question">
            <div class="question-text">{question.questionText}</div>
            <textarea class="answer-input" bind:value={answerInputs[index]}></textarea>
            <button on:click={() => addAnswer(index)}>Submit Answer</button>
            <button on:click={() => distributePot(index)}>Distribute Pot</button>
            <div class="question-pot">
                Pot: {questionPots[index]} wei
            </div>
        </div>
    {/each}
</div>

<style>
    .question {
        border: 1px solid #ccc;
        border-radius: 5px;
        padding: 10px;
        margin-bottom: 20px;
        background-color: #f9f9f9;
    }

    .question-text {
        font-weight: bold;
        margin-bottom: 10px;
    }

    .answer-input {
        margin-top: 10px;
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 3px;
        background-color: #ffffff;
    }

    button:hover {
        background-color: #000088;
    }

    button {
        font-size: 12px;
        color: blue;
        cursor: pointer;
        margin-top: 5px;
        background-color: #000000;
        color: #ffffff;
        border-radius: 3px;
        padding: 5px 10px;
        transition: 0.2s;
        margin-left: 10px;
    }
</style>
