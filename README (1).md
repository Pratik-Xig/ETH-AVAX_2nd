# ETH_AVAX_proj2

This project connects a React application to MetaMask, allowing users to vote for candidates and retrieve vote counts stored on the Ethereum blockchain using a smart contract.

## Description

The `ETH_AVAX_proj2` project includes a smart contract and a React frontend that interact with each other. The smart contract allows users to vote for candidates and retrieve the vote count for a candidate, while the React application connects to MetaMask for user authentication and interaction with the smart contract.

## Contract Details

The `ETH_AVAX_proj2` smart contract is written in Solidity and includes the following functionalities:

### State Variables

- `mapping(string => uint256) private votes`: Stores the vote count for each candidate.

### Events

- `event VoteCasted(string candidate, uint256 votes)`: Emitted when a new vote is cast.

### Functions

#### vote

```solidity
function vote(string memory candidate) public {
    votes[candidate]++;
    emit VoteCasted(candidate, votes[candidate]);
}
```

#### getVotes

```solidity
function getVotes(string memory candidate) public view returns (uint256) {
    return votes[candidate];
}
```

## Frontend Details

The frontend of the `ETH_AVAX_proj2` project is a React application that interacts with the smart contract using ethers.js.

### Main Components

- **App.js**: Renders the `WalletCard` component.
- **WalletCard.js**: Contains the logic for connecting to MetaMask and interacting with the smart contract.

### Key Functions

#### App.js

```javascript
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <WalletCard />
      </header>
    </div>
  );
}
```

#### WalletCard.js

- **connWalletHandler**: Connects to MetaMask.

```javascript
const connWalletHandler = () => {
    if (window.ethereum) {
        window.ethereum.request({ method: 'eth_requestAccounts' })
            .then(result => {
                accountChangedHandler(result[0]);
                setConnButtonText('Connected..!!');
            });
    } else {
        setErrorMessage("Metamask Not Installed");
    }
};
```

- **accountChangedHandler**: Updates the account information.

```javascript
const accountChangedHandler = (newAccount) => {
    setDefaultAccount(newAccount);
    updateEthers();
};
```

- **updateEthers**: Sets up ethers.js provider, signer, and contract.

```javascript
const updateEthers = () => {
    let tempProvider = new ethers.providers.Web3Provider(window.ethereum);
    setProvider(tempProvider);
    let tempSigner = tempProvider.getSigner();
    setSigner(tempSigner);
    let tempContract = new ethers.Contract(ContractAdd, ContractABI, tempSigner);
    setContract(tempContract);
};
```

- **voteHandler**: Casts a vote for a candidate.

```javascript
const voteHandler = (event) => {
    event.preventDefault();
    const candidate = event.target.candidateName.value;
    contract.vote(candidate)
        .then(() => {
            getVotes(candidate);
        })
        .catch(error => {
            console.log(error);
        });
};
```

- **getVotes**: Retrieves the vote count for a candidate.

```javascript
const getVotes = async (candidate) => {
    let votes = await contract.getVotes(candidate);
    setVoteCount(votes.toString());
};
```

### Usage

```javascript
return (
    <>
        <h3>Voting System using Smart Contract</h3>
        <button onClick={connWalletHandler}>{connButtonText}</button>
        <h4>Address: {defaultAccount}</h4>
        <form onSubmit={voteHandler}>
            <input id='candidateName' type="text" placeholder="Candidate Name" />
            <button type="submit">Vote</button>
        </form>
        <form onSubmit={(e) => { e.preventDefault(); getVotes(e.target.candidateName.value); }}>
            <input id='candidateName' type="text" placeholder="Candidate Name" />
            <button type="submit">Get Vote Count</button>
        </form>
        <div>
            {voteCount !== null && <p>Vote Count: {voteCount}</p>}
        </div>
        {errorMessage}
    </>
);
```

## Deployment

### Smart Contract

To deploy the `ETH_AVAX_proj2` smart contract:

1. Open [Remix](https://remix.ethereum.org/).
2. Create a new file and paste the smart contract code.
3. Compile the contract.
4. Deploy the contract to your desired Ethereum network.

### React Application

To run the React application:

1. Clone the repository.
2. Navigate to the project directory.
3. Install dependencies: `npm install`.
4. Start the application: `npm start`.

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.

## Authors

Sujal Mahajan

## Contributing

Contributions are welcome! Feel free to submit changes or improvements.

---

This README provides a concise guide to understanding, deploying, and using the `ETH_AVAX_proj2` smart contract and React application.
