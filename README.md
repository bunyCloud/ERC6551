# ERC6551
ERC6551 NFT Wallet SmartContracts

- On-chain identity
- Each NFT is minted and deployed with its own token bound smartcontract. 
- Token bound accounts are delegated to the owner of the ERC-721 token
- Each token bound account is owned by a single ERC-721 token
- NFT can interact with the blockchain, record transaction history, and own on-chain assets.
- Token bound accounts can own any type of on-chain asset, and can be extended to support new asset types created in the future.
The BunyERC6551Account contract implements the interfaces IERC165,
IERC1271, and IERC6551Account that interacts with ERC20, ERC721 tokens,
verifies digital signatures, and supports interface detection.

The contract is designed to manage a "token owned account" (a user
account that holds tokens). This account is given a name.

# BunyERC6551Account.sol
## NFT bound account implementation

[![N|Solid](https://buny.cloud/android-chrome-192x192.png)](https://buny.cloud)
## The Buny Project
https://buny.cloud

The BunyERC6551Account contract is designed to manage a "token owned account" (a user account that holds tokens). This contract is used as a template implementation by the BunyERC6551Registry contract. 

## Features

- Onchain NFT wallet owned by NFT Token
- contract assets transfer with token
- multi-chain nft binding support
- receive deposits of any erc20 token
- erc20 token balance array
- Withdraw erc20 tokens
- Assign a custom name
- Create calendar events

## Mappings

| Variable Name | Description |
| --- | --- |
| `deposits` | Stores information about deposits, indexed by a numerical ID. The deposit details are stored in the `Deposit` struct which includes the depositor's address, the token's address, the deposit amount, and the timestamp of the deposit. |
| `tokenBalances` | Saves the balance of various tokens, indexed by the token address. |
| `userPrivateEvents` and `userPublicEvents` | Stores private and public calendar events respectively, associated with a user's address. Each event's details are stored in either the `PrivateCalendarEvent` or `PublicCalendarEvent` structs which includes a title, the attendee's address, a start and end time, and for public events, a calendar address. |
| `calendarAddress` | Stores the address of a calendar contract. |


## Events
| Event Name | Description |
| --- | --- |
| `NewEventCreated` | Stores information about a newly created event. Includes the event title, the organizer's address, start and end times, a URI to additional metadata, and the timestamp of the event creation. |
| `EventUpdated` | Stores information about an updated event. Includes the event ID, the updated title, the organizer's address, start and end times, a URI to additional metadata, and the timestamp of the event update. |
| `UserInvited` | Stores information about a user being invited to an event. Includes the event ID, the title of the event, and the address of the invited user. |
| `InvitationAccepted` | Stores information about an invitation being accepted by an attendee. Includes the event ID and the address of the attendee who accepted the invitation. |



## Functions
nstructions on how to use them in your own application are linked below.

| Function | Description |
| ------ | ------ |
| `receive()` | external and payable, allowing the contract to accept TLOS directly (without any function being called) by sending a transaction to the contract's address. |
| `setAccountName(string memory newName)` | This function allows the contract owner to change the name of the account. The new name is passed as an argument to the function. A requirement is enforced that only the contract owner can call this function. |
| `setCalendarAddress(address _contractAddress)` | This function allows the contract owner to change the address of the calendar contract. The new address is passed as an argument to the function. A requirement is enforced that only the contract owner can call this function. |
| `createPrivateCalendarEvent(string memory title, uint startTime, uint endTime)` | This function allows the contract owner to create a new private calendar event. The event's title, start time, and end time are passed as arguments to the function. The function creates a new PrivateCalendarEvent struct instance and pushes it to the userPrivateEvents array of the contract owner. An event NewPrivateEventCreated is also emitted. A requirement is enforced that only the contract owner can call this function. |
| `getUserPrivateEvents(address user)` | This function allows the contract owner to fetch private calendar events for any user. The user's address is passed as an argument to the function. The function returns an array of PrivateCalendarEvent instances. A requirement is enforced that only the contract owner can call this function. |
| `getUserPublicEvents(address user)` | This function allows any user to fetch public calendar events for any user. The user's address is passed as an argument to the function. The function returns an array of PublicCalendarEvent instances. |
| `createTelosCalendarEvent(string memory title, uint256 startTime, uint256 endTime)` | This function allows any user to create a new public calendar event. The event's title, start time, and end time are passed as arguments to the function. The function first interfaces with a calendar contract (default is CalendarOne), and then calls its createTelosCalendarEvent function to create the event. It then creates a new PublicCalendarEvent struct instance and pushes it to the userPublicEvents array. An event NewPublicEventCreated is also emitted. |
| `deposit()` | This function enables users to deposit native chain ERC20 tokens (for example, TLOS) into the contract. The function first requires that the deposit amount (msg.value) is greater than 0. It then increments depositCount and stores the deposit details (including the depositor address, token address (which is zero for native tokens), deposit amount, and timestamp of the deposit) in the deposits mapping. It also emits a NewDeposit event to log this transaction. |
| `receiveERC20(address _erc20Token, uint256 amount)` | This function is for depositing any ERC20 token into the contract. The depositor specifies the address of the ERC20 token and the amount to deposit. The token balance for the specified ERC20 token is updated in the tokenBalances mapping. The deposit details are stored in the deposits mapping just like in the deposit() function, and a NewERC20Deposit event is emitted. |
| `getBalance()` | This function returns the balance of native tokens (such as TLOS) in the contract. It does so by returning the balance of the contract address. |
| `getTokenBalance(address _erc20Token)` | This function returns the balance of a specified ERC20 token in the contract. It does so by calling the balanceOf() function of the specified ERC20 token for the contract address. It's worth noting that the function assumes that the provided _erc20Token address represents a contract that implements the ERC20 standard. If it doesn't, this function call could fail. |
| `executeCall(address to, uint256 value, bytes calldata data)` | This function is typically used to call another contract function with a specific amount of ether and provided data. The call is made by the contract owner. It takes in the address of the contract to call, the amount of ether to send with the call, and the data (function signature and arguments) to call with. The function requires the caller to be the owner. If the external contract call fails, it reverts the transaction using inline assembly. After a successful call, it increments the _nonce variable, which could be used for tracking the number of successful calls made. |
| `withdraw(uint256 amount)` | This function is for the contract owner to withdraw a specified amount of ether from the contract. It first checks if the sender is the contract owner and if there is sufficient balance in the contract. If the checks pass, it transfers the specified amount to the contract owner and emits a WithdrawEvent. |
| `withdrawTo(address payable recipient, uint256 amount)` | This function is similar to the withdraw function but instead allows the contract owner to specify a recipient address to which the ether will be sent. It checks for the same conditions as the withdraw function and transfers the amount to the specified recipient. |
| `token()` | This function returns the chain ID, the token contract address, and the token ID. These are retrieved by decoding the last 96 bytes of the contract's own bytecode. It seems that this function assumes that the last 96 bytes of the contract's bytecode hold these values in a specific format, which should be the case if the contract was created by an ERC721 factory contract or a similar mechanism. However, it's not clear from the provided code where this data comes from and what ensures its correct placement in the bytecode. |
| `owner()` | This function returns the address of the owner of a token. It first calls the token() function of the contract to get the chain ID, token contract address, and token ID. If the chain ID returned by the token() function does not match the current chain ID, it returns a zero address. Otherwise, it calls the ownerOf(tokenId) function of the token contract (which must be an ERC721 contract) to return the owner of the token. The purpose of this function is to determine the owner of an NFT (Non-Fungible Token) represented by the contract. |
| `supportsInterface(bytes4 interfaceId)` | This function checks whether the contract supports a certain interface, which is determined by its interface ID. It checks if the interface ID matches the interface ID of any of the three interfaces implemented by the contract (IERC165, IERC6551Account, IERC1271). If the interface ID matches any of them, the function returns true; otherwise, it returns false. |
| `isValidSignature(bytes32 hash, bytes memory signature)` | This function is used to check if a certain signature of a hash is valid, and if the signature has been signed by the owner of the contract. It calls the `isValidSignatureNow` function of the SignatureChecker library, which checks the validity of the signature. If the signature is valid, it returns the function selector of the `isValidSignature` function of the IERC1271 interface. Otherwise, it returns the maximum possible value of a `bytes4` type. |
| `nonce()` | This function is a simple getter function that returns the current value of `_nonce`. A nonce is a number that is often used in cryptographic operations for security reasons. It is used to prevent replay attacks. It's typically used to ensure that a function can't be called with the same parameters more than once. In this contract, it's incremented every time `executeCall()` is called. |


## License

// SPDX-License-Identifier: MIT




