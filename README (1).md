# ETH-AVAX_2nd

The ETH-AVAX_2nd project connects a React application to MetaMask, enabling users to interact with a smart contract (Assessment.sol) on the Ethereum blockchain. Users can deposit Ether, withdraw Ether, and send Ether to other addresses using the smart contract.

## Description

The `ETH-AVAX_2nd` project includes a smart contract and a React frontend that interact with each other. The smart contract allows users to vote for candidates and retrieve the vote count for a candidate, while the React application connects to MetaMask for user authentication and interaction with the smart contract.

## Contract Details

The Assessment.sol smart contract includes:

### State Variables

- `address payable public owner`: Stores the owner's address.
uint256 public balance: Stores the current contract balance.

### Events

- `event Deposit(uint256 amount)`: Emitted when Ether is deposited into the contract.
-`event Withdraw(uint256 amount)`: Emitted when Ether is withdrawn from the contract.
-`event EtherSent(address recipient, uint256 amount)`: Emitted when Ether is sent to another address.

### Functions

#### deposit

```solidity
function deposit(uint256 _amount) public payable {
        uint _previousBalance = balance;
        require(msg.sender == owner, "You are not the owner of this account");
        balance += _amount;
        assert(balance == _previousBalance + _amount);
        emit Deposit(_amount);
    }
```

#### withdraw

```solidity
 function withdraw(uint256 _withdrawAmount) public {
        require(msg.sender == owner, "You are not the owner of this account");
        uint _previousBalance = balance;
        if (balance < _withdrawAmount) {
            revert InsufficientBalance({
                balance: balance,
                withdrawAmount: _withdrawAmount
            });
        }
        balance -= _withdrawAmount;
        assert(balance == (_previousBalance - _withdrawAmount));
        emit Withdraw(_withdrawAmount);
    }
```

### sendEther

```solidity

function sendEther(address payable _recipient, uint256 _amount) public {
    require(msg.sender == owner, "You are not the owner of this account");
    require(balance >= _amount, "Insufficient balance to send");

    uint _previousBalance = balance;

    (bool success, ) = _recipient.call{value: _amount}("");

    require(success, "Transfer failed");

    balance -= _amount;

    assert(balance == (_previousBalance - _amount));

    emit EtherSent(_recipient, _amount);
}

```
Allows the owner to send Ether to another address (_recipient).

## Frontend Details


The frontend of the `ETH_AVAX_proj2` project is a React application that interacts with the smart contract using ethers.js.

### Main Components

- **Deploy.js**: Renders the `index.js` component.

### Key Functions

#### Deploy.js

```javascript
const hre = require("hardhat");

async function main() {
  const initBalance = 1;
  const Assessment = await hre.ethers.getContractFactory("Assessment");
  const assessment = await Assessment.deploy(initBalance);
  await assessment.deployed();

  console.log(`A contract with balance of ${initBalance} eth deployed to ${assessment.address}`);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});

```

#### index.js

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

- **storeTransaction**: Stores transactions
```javascript
 const storeTransaction = (type, amount) => {
    const newTransaction = {
      type,
      amount: ethers.utils.formatEther(amount),
      address: account,
      timestamp: new Date().toLocaleString(),
    };
    setTransactions((prevTransactions) =>
      prevTransactions.concat(newTransaction)
    );
  };

```

- **SendMoney**: To send ether to a specific address.

```javascript
 const sendMoney = async () => {
    if (recipient && amount) {
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner();
      const tx = await signer.sendTransaction({
        to: recipient,
        value: ethers.utils.parseEther(amount),
      });
      await tx.wait();
      alert("Transaction successful");
      storeTransaction("Sent", ethers.utils.parseEther(amount));
      getBalance();
    } else {
      alert("Recipient address and amount are required.");
    }
  };
```


### Usage

```javascript
 return (
      <div>
        <p>Your Account: {account}</p>
        <p>Your Balance: {balance} ETH</p>
        <button onClick={deposit}>Deposit 1 ETH</button>
        <button onClick={withdraw}>Withdraw 1 ETH</button>

        <h2>Send Ether</h2>
        <input
          type="text"
          placeholder="Recipient Address"
          value={recipient}
          onChange={(e) => setRecipient(e.target.value)}
        />
        <input
          type="text"
          placeholder="Amount in ETH"
          value={amount}
          onChange={(e) => setAmount(e.target.value)}
        />
        <button onClick={sendMoney}>Send</button>

        <h2>Transaction History</h2>
        <ul>
          {transactions.map((tx, index) => (
            <li key={index}>
              {tx.type} - {tx.amount} - {tx.timestamp}
            </li>
          ))}
        </ul>
      </div>
    );
  };

```

## Deployment

### Smart Contract

To deploy the `ETH-AVAX_2nd` smart contract:

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

Pratik Mishra

## Contributing

Contributions are welcome! Feel free to submit changes or improvements.

---

This README provides a concise guide to understanding, deploying, and using the `ETH-AVAX_2nd` smart contract and React application.
