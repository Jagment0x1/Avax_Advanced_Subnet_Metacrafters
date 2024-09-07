# Metacrafters Avalanche Subnet Project (Cloning DEFI Kingdom)

An Avalanche Subnet (short for subnetwork) is a customizable blockchain network within the Avalanche ecosystem. Avalanche is a blockchain platform that enables the creation of highly scalable decentralized applications (dApps) and enterprise blockchain solutions. It achieves this by supporting the creation of multiple parallel blockchain networks, called Subnets, which are specialized to serve specific use cases.

**Project Objective**

1. **Set up your EVM Subnet**: Use the provided guide along with Avalanche's official documentation to create a custom EVM-based subnet on the Avalanche network.

2. **Define Your Native Currency**: Configure a unique native currency that will function as the in-game currency for your DeFi Kingdom clone.

3. **Connect to MetaMask**: Follow the steps outlined in the guide to connect your custom EVM subnet to MetaMask, enabling interaction between your subnet and the wallet.

4. **Deploy Core Game Contracts**: Utilize Solidity and Remix to deploy the fundamental smart contracts that will serve as the building blocks of your game. These contracts will handle various game mechanics such as battling, exploring, trading, liquidity pools, and token management, defining the core rules of gameplay.

## 'ERC20' Contract Explanation

```solidity
   // SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "Solidity by Example";
    string public symbol = "SOLBYEX";
    uint8 public decimals = 18;

		event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```

This Solidity code implements a basic **ERC-20 token** contract, which is a standard for fungible tokens on Ethereum and compatible blockchains like Avalanche. Below is a breakdown of its components:

### SPDX License Identifier
```solidity
// SPDX-License-Identifier: MIT
```

### Solidity Version Declaration
```solidity
pragma solidity ^0.8.17;
```
Specifies that this contract is written for Solidity version 0.8.17 or higher, ensuring compatibility and security improvements introduced in these versions.

### Contract Structure: `ERC20`
```solidity
contract ERC20 {
```
The contract is named `ERC20` and follows the ERC-20 token standard, which defines how a token should behave on Ethereum-based blockchains.

### State Variables
```solidity
uint public totalSupply;
mapping(address => uint) public balanceOf;
mapping(address => mapping(address => uint)) public allowance;
string public name = "Solidity by Example";
string public symbol = "SOLBYEX";
uint8 public decimals = 18;
```
1. **`totalSupply`**: Tracks the total number of tokens that have been minted.
2. **`balanceOf`**: A mapping that stores the token balance of each address.
3. **`allowance`**: A mapping that allows one address (a spender) to withdraw tokens from another address (the owner), up to a specified amount.
4. **`name`**: The name of the token.
5. **`symbol`**: The ticker symbol for the token.
6. **`decimals`**: Represents how divisible the token is. 18 decimals means the smallest unit of the token is \(10^{-18}\), similar to how Ethereum uses `wei`.

### Events
```solidity
event Transfer(address indexed from, address indexed to, uint value);
event Approval(address indexed owner, address indexed spender, uint value);
```
1. **`Transfer`**: Emitted whenever tokens are transferred from one address to another.
2. **`Approval`**: Emitted when an address approves another address to spend tokens on its behalf.

### Functions

#### `transfer`
```solidity
function transfer(address recipient, uint amount) external returns (bool) {
    balanceOf[msg.sender] -= amount;
    balanceOf[recipient] += amount;
    emit Transfer(msg.sender, recipient, amount);
    return true;
}
```
- Transfers tokens from the caller's (`msg.sender`) account to the recipient.
- Updates the balances of both the sender and recipient.
- Emits a `Transfer` event.

#### `approve`
```solidity
function approve(address spender, uint amount) external returns (bool) {
    allowance[msg.sender][spender] = amount;
    emit Approval(msg.sender, spender, amount);
    return true;
}
```
- Allows a third-party `spender` to withdraw tokens from the caller's account, up to the specified `amount`.
- Updates the `allowance` mapping and emits an `Approval` event.

#### `transferFrom`
```solidity
function transferFrom(
    address sender,
    address recipient,
    uint amount
) external returns (bool) {
    allowance[sender][msg.sender] -= amount;
    balanceOf[sender] -= amount;
    balanceOf[recipient] += amount;
    emit Transfer(sender, recipient, amount);
    return true;
}
```
- Allows an approved third-party (the spender) to transfer tokens from one address (`sender`) to another (`recipient`).
- Deducts from the allowance and balances accordingly and emits a `Transfer` event.

#### `mint`
```solidity
function mint(uint amount) external {
    balanceOf[msg.sender] += amount;
    totalSupply += amount;
    emit Transfer(address(0), msg.sender, amount);
}
```
- Allows the caller to mint (create) new tokens, increasing both their balance and the total supply.
- Emits a `Transfer` event where the sender is the zero address (`address(0)`), signaling the creation of new tokens.

#### `burn`
```solidity
function burn(uint amount) external {
    balanceOf[msg.sender] -= amount;
    totalSupply -= amount;
    emit Transfer(msg.sender, address(0), amount);
}


```
- Allows the caller to burn (destroy) tokens from their balance, reducing the total supply.
- Emits a `Transfer` event to the zero address, indicating the tokens have been destroyed.

## 'Vault' Contract Explanation 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC20 {
    function totalSupply() external view returns (uint);

    function balanceOf(address account) external view returns (uint);

    function transfer(address recipient, uint amount) external returns (bool);

    function allowance(address owner, address spender) external view returns (uint);

    function approve(address spender, uint amount) external returns (bool);

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}

contract Vault {
    IERC20 public immutable token;

    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }

    function _burn(address _from, uint _shares) private {
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }

    function deposit(uint _amount) external {
        /*
        a = amount
        B = balance of token before deposit
        T = total supply
        s = shares to mint

        (T + s) / T = (a + B) / B 

        s = aT / B
        */
        uint shares;
        if (totalSupply == 0) {
            shares = _amount;
        } else {
            shares = (_amount * totalSupply) / token.balanceOf(address(this));
        }

        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint _shares) external {
        /*
        a = amount
        B = balance of token before withdraw
        T = total supply
        s = shares to burn

        (T - s) / T = (B - a) / B 

        a = sB / T
        */
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        token.transfer(msg.sender, amount);
    }
}
```

### SPDX License and Solidity Version

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;
```
This indicates that the contract is licensed under MIT and specifies that the Solidity compiler version used should be 0.8.17 or higher.

### ERC-20 Interface: `IERC20`
```solidity
interface IERC20 {
    function totalSupply() external view returns (uint);
    function balanceOf(address account) external view returns (uint);
    function transfer(address recipient, uint amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint);
    function approve(address spender, uint amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint amount) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}
```
- This interface defines the standard functions and events for an ERC-20 token contract, which include functions to query balances, transfer tokens, approve third-party transfers, and handle allowances.
- The `IERC20` interface is used in the `Vault` contract to interact with the ERC-20 token.

### The `Vault` Contract
```solidity
contract Vault {
    IERC20 public immutable token;

    uint public totalSupply;
    mapping(address => uint) public balanceOf;
```
1. **`token`**: Stores the address of the ERC-20 token that the vault interacts with. This is marked as `immutable`, meaning it is set once during the contract deployment and cannot be changed.
2. **`totalSupply`**: Tracks the total number of "shares" that have been minted.
3. **`balanceOf`**: A mapping that records how many shares each address holds.

### Constructor
```solidity
constructor(address _token) {
    token = IERC20(_token);
}
```
- The constructor sets the ERC-20 token that the vault will accept for deposits by initializing the `token` variable with the token's contract address.

### Internal Functions

#### `_mint`
```solidity
function _mint(address _to, uint _shares) private {
    totalSupply += _shares;
    balanceOf[_to] += _shares;
}
```
- Mints (creates) new "shares" and assigns them to the address `_to`. The total supply of shares is increased accordingly.

#### `_burn`
```solidity
function _burn(address _from, uint _shares) private {
    totalSupply -= _shares;
    balanceOf[_from] -= _shares;
}
```
- Burns (destroys) the specified number of shares from `_from` and reduces the total supply accordingly.

### Core Functions

#### `deposit`
```solidity
function deposit(uint _amount) external {
    uint shares;
    if (totalSupply == 0) {
        shares = _amount;
    } else {
        shares = (_amount * totalSupply) / token.balanceOf(address(this));
    }

    _mint(msg.sender, shares);
    token.transferFrom(msg.sender, address(this), _amount);
}
```
- **Purpose**: Allows users to deposit ERC-20 tokens into the vault and receive "shares" in return.
- **Logic**:
  - If no shares have been issued yet (`totalSupply == 0`), the user receives an equal number of shares to the amount of tokens they deposit.
  - If shares already exist, the user receives shares proportional to their deposit, based on the vault's current token balance and total supply of shares.
- After calculating the number of shares, the function:
  - Mints the calculated number of shares to the depositor.
  - Transfers the deposited tokens from the user to the vault.

#### `withdraw`
```solidity
function withdraw(uint _shares) external {
    uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
    _burn(msg.sender, _shares);
    token.transfer(msg.sender, amount);
}
```
- **Purpose**: Allows users to withdraw their ERC-20 tokens by redeeming shares.
- **Logic**:
  - The amount of tokens the user can withdraw is proportional to the number of shares they redeem, based on the vault's current token balance and the total supply of shares.
- After calculating the token amount:
  - The function burns the shares being redeemed.
  - Transfers the corresponding amount of tokens back to the user.
 
## Thank You Metacrafters 

I want to express my deepest gratitude to **Metacrafters** for providing such an incredible opportunity. The knowledge and hands-on experience I’ve gained through your platform have been invaluable, and it’s truly been a game-changer for my growth in blockchain development. Your dedication to empowering learners with cutting-edge content and real-world challenges has made a huge impact on my journey, and I am genuinely grateful for the skills and insights I've developed.

Thank you, Metacrafters, for fostering such a supportive and innovative learning environment!
