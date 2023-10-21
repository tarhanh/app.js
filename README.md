# app.js
const web3 = new Web3(Web3.givenProvider);

const contractAddress = "YOUR_CONTRACT_ADDRESS";
const contractABI = [ /* Paste the ABI from Remix or compile the contract */ ];
const contract = new web3.eth.Contract(contractABI, contractAddress);

async function createPoll() {
    const question = prompt("Enter poll question:");
    if (question) {
        await contract.methods.createPoll(question).send({ from: ethereum.selectedAddress });
        displayPolls();
    }
}

async function vote(pollId, option) {
    await contract.methods.vote(pollId, option).send({ from: ethereum.selectedAddress });
    displayPolls();
}

async function displayPolls() {
    const pollsDiv = document.getElementById("polls");
    pollsDiv.innerHTML = "";

    const pollCount = await contract.methods.pollCount().call();
    for (let i = 1; i <= pollCount; i++) {
        const poll = await contract.methods.polls(i).call();
        const isOpen = poll.isOpen ? "Open" : "Closed";

        const pollElement = document.createElement("div");
        pollElement.innerHTML = `
            <h3>${poll.question}</h3>
            <p>Status: ${isOpen}</p>
            <p>Total Votes: ${poll.totalVotes}</p>
            <button onclick="vote(${i}, 1)">Option 1</button>
            <button onclick="vote(${i}, 2)">Option 2</button>
            <button onclick="vote(${i}, 3)">Option 3</button>
        `;

        pollsDiv.appendChild(pollElement);
    }
}

window.addEventListener("load", async () => {
    // Prompt the user to connect their wallet
    await ethereum.request({ method: 'eth_requestAccounts' });
    
    // Display polls on page load
    displayPolls();
});
