# Decentralized Book Rental

## Project Objective

Develop a trustless book rental platform that enables:

- Owners to list books or items for rent, specifying a deposit amount and daily rental fee  
- Renters to borrow listed items by paying the required deposit and rental charges  

The platform leverages Ethereum smart contracts to:

- Securely hold deposits until items are returned  
- Automatically refund deposits after deducting applicable rental fees  
- Enforce penalties for late returns

Team Members:
<br>
- [Param Saxena(230001060)](https://github.com/SAXENA-PARAM)
- [Saumya Vaidya(230008035)](https://github.com/samthedoctor)
- [Jagrit(230051005)](https://github.com/idJagrit)
- [Jai Pannu(230004019)]()
- [Krishay Rathaure(230004026)](https://github.com/Quanmat)
- [Rudra Jadon(230004043)](https://github.com/rudrajadon)

## Setup Instructions

### 1. Prerequisites
Ensure you have the following installed/configured:
- **Node.js** (v16 or above)
- **npm** 
- **MetaMask** browser extension (for Ethereum wallet)
- **Remix IDE** ([remix.ethereum.org](https://remix.ethereum.org/)) for smart contract deployment
- **Pinata account** ([pinata.cloud](https://pinata.cloud/)) for IPFS file storage
- **Railway account** ([railway.app](https://railway.app/)) for backend server deployment
- **Vercel account** ([vercel.com](https://vercel.com/)) for frontend deployment (or run locally)

---
### 2. Deployment Status
The website is live and accessible at [bookchain-lemon.vercel.app](https://bookchain-lemon.vercel.app/), having been successfully deployed to the Vercel hosting platform.

---

### 3. Smart Contract Deployment (Sepolia Testnet)
1. **Open Remix IDE**:  
   Go to [remix.ethereum.org](https://remix.ethereum.org/).

2. **Add Holesky Testnet to MetaMask**:  
   - Visit [Chainlist](https://chainlist.org/chain/17000).
   - Click **Connect Wallet** to add it to MetaMask.
   - This step is essential because Remix operates on a public Ethereum testnet. To ensure compatibility, the MetaMask wallet must be connected to the same network—specifically the Holesky testnet via Chainlist. Test ETH can be obtained from the Sepolia faucet to fund transactions during development and testing.

3. **Get Sepolia Testnet ETH**:  
   Use [Google Cloud Sepolia Faucet](https://cloud.google.com/application/web3/faucet/ethereum/sepolia) to get test tokens in your metamask account. 

4. **View Transactions**:  
   Check activity on:  
   - [Holesky Explorer](https://eth-holesky.blockscout.com/address/0xaFcA4e33E00CFF39BF2278202e8ABA507dF92ea5?tab=txs)  

---

<table>
  <tr>
    <td style="width: 50%; vertical-align: top; padding-right: 20px;">
      <h3>4. Connect MetaMask</h3>
      <ol>
        <li><strong>Ensure Holesky Testnet is Active</strong>:<br>Select "Holesky" in MetaMask.</li>
        <li><strong>Get Test ETH</strong>:<br>Use the faucet listed in Step 3.</li>
        <li><strong>Connect to Frontend</strong>:<br>Click <strong>Connect Wallet</strong> on the frontend and approve in MetaMask.</li>
      </ol>
      <p>You can see in the given image as well.</p>
    </td>
    <td style="width: 50%; text-align: center;">
      <img src="wallet1.jpg" alt="MetaMask Connect Example" style="max-width: 100%; border-radius: 10px; box-shadow: 0 0 10px rgba(0,0,0,0.1);"/>
    </td>
  </tr>
</table>

---

### 5. Backend (Pinata Server) Setup

1. **Deploy to Railway**  
   - This is backend server repository: [Pinata](https://github.com/SAXENA-PARAM/Pinata-server) and has been successfully deployed to [Railway](https://railway.app/).  
   - This backend server handles file uploads to IPFS via the Pinata service by generating pre-signed URLs.

2.**Note API Endpoint**  
   - This will be used by the frontend to request pre-signed URLs `/presigned_url`.

---

### 6. Frontend Setup
- The frontend is deployed on Vercel

- It uses `web3.js` to interact with the smart contract deployed on the Holesky Ethereum testnet.

#### 🎉 YOU ARE READY TO USE THE PLATFORM NOW!! 🚀🎯

 ---

## Contract Explanation

### Overview
The smart contract enables a trustless book/item rental system where:
- **Owners** list items for rent with a daily price and deposit.
- **Renters** pay a deposit upfront (covering rental fees + maximum penalties) to borrow items.
- **Late returns** are automatically penalized up to a predefined limit (`max_penalty_days`), after which the item is auto-returned.

---

### Key Variables
1. **`max_penalty_days`**  
   - Maximum days a renter can be late (e.g., 5 days).  
   - After this period, the item is auto-returned, and the deposit is fully consumed.  

2. **`max_penalty_fees`**  
   - Penalty charged per late day (e.g., 0.01 ETH).  

3. **`Item` Struct**  
   - Stores item details and rental status:  
     ```
     struct Item {
         address owner;
         string title;
         uint256 dailyPrice;
         uint256 deposit;
         bool isAvailable;
         uint256 rentalStartTime;
         address renter;
     }
     ```

4. **Mappings**  
   - `items`: Maps item IDs to their `Item` struct.  
   - `rentals`: Tracks active rentals by renter address.  

---

### Core Functions

<table>
  <tr>
    <td style="width: 50%; vertical-align: top; padding-right: 20px;">
      <h4>1. <code>listItem</code></h4>
      <ul>
        <li><strong>Purpose</strong>:<br>Allows owners to list items for rent.</li>
        <li><strong>Parameters</strong>:
          <ul>
            <li><code>title </code>: Name/description of the item.</li>
            <li><code>dailyPrice</code>: Daily Rent (in ETH).</li>
            <li><code>image </code>: Image file of the item.</li>
          </ul>
        </li>
        <li><strong>Logic</strong>:
          <ul>
            <li>Stores the item in the <code>items</code> mapping.</li>
            <li>Confirms the transaction with MetaMask.</li>
            <li>Emits an <code>ItemListed</code> event.</li>
          </ul>
        </li>
      </ul>
    </td>
    <td style="width: 50%; text-align: center;">
      <img src="image.png" alt="List Item Flow" style="max-width: 100%; border-radius: 10px; box-shadow: 0 0 10px rgba(0,0,0,0.1);"/>
    </td>
  </tr>
</table>

#### 2. `rentItem`
- **Purpose**: Renters borrow items by paying the deposit.  
- **Parameters**:  
  - `itemId`: ID of the item to rent.  
  - `rentalDays`: Number of days the renter intends to use the item.  
- **Logic**:  
  - Validates payment:  
    ```
    require(msg.value == item.deposit, "Incorrect deposit");
    ```
  - Locks the item (`isAvailable = false`).  
  - Records rental start time and renter address.  
  - Emits an `ItemRented` event.  

#### 3. `returnItem`
- **Purpose**: Renters return items to claim a refund.  
- **Parameters**:  
  - `itemId`: ID of the item being returned.  
- **Logic**:  
  - Calculates rental duration:  
    ```
    uint256 daysRented = (block.timestamp - rentalStartTime) / 86400;
    ```
  - Computes refund:  
    ```
    uint256 refund = item.deposit - (daysRented * item.dailyPrice);
    ```
  - Deducts penalties for late returns up to `max_penalty_days`.  
  - Transfers refund to renter and remaining funds to owner.  
  - Emits an `ItemReturned` event.  

---

### Penalty System
The deposit is calculated as:  

| Scenario          | Refund Calculation                          |
|-------------------|---------------------------------------------|
| **Early return**  | Unused penalty fees are refunded.           |
| **Late return**   | Penalties deducted daily up to `max_penalty_days`. |

**Example**:  
- `daily_price = 0.1 ETH`, `max_penalty_days = 5`, `max_penalty_fees = 0.01 ETH`  
- Rent for **3 days**:  
  - **Deposit** = `(3 × 0.1) + (5 × 0.01) = 0.35 ETH`  
  - **Returned on day 3**: Refund = `0.35 - (3 × 0.1) = 0.05 ETH`  
  - **Returned on day 8**: Refund = `0.35 - (8 × 0.1) = -0.45 ETH` (full deposit forfeited).  

---

### Events
- **`ItemListed`**  
- **`ItemRented`**  
- **`ItemReturned`**  

---

### Security Features
- **Reentrancy Guard**: Uses OpenZeppelin’s `ReentrancyGuard`.  
- **Access Control**: Only owners can list items (`onlyOwner` modifier).  
- **Input Validation**: Checks for correct deposit amounts and item availability.  

---

### Workflow Example
1. **Owner lists a book**:  
2. **Renter borrows for 3 days**:  
3. **Returned on day 3**:  
4. **Returned on day 8**: Auto-returned → full deposit forfeited.  
## Testing Guide

This section explains how to test the core functionalities of your smart contract and platform to ensure all features work as intended.

---

### 1. Prerequisites
- Smart contract deployed on Sepolia testnet (see [Setup Instructions](#setup-instructions))
- Test ETH in your MetaMask wallet (from Sepolia faucet)
- Access to your frontend (locally or on Vercel)
- *(Optional)* Remix IDE for manual contract interaction

---

### 2. Manual Testing via Frontend

#### **A. Listing an Item**
1. Connect MetaMask with an account that owns books/items.
2. Go to the **"List Item"** page.
3. Fill in the item details:
   - Title (e.g., "The Alchemist")
   - Daily Price (e.g., 0.1 ETH)
   - Deposit (auto-calculated based on rental days and penalties)
4. Submit the form.
5. **Expected Result**: The item appears in the marketplace as available for rent.

#### **B. Renting an Item**
1. Switch to a renter account in MetaMask.
2. Browse the marketplace and select an available item.
3. Enter the number of days to rent (e.g., 3 days).
4. Confirm and pay the deposit (including rental fee + max penalty).
5. **Expected Result**:  
   - The item is marked as **"Rented"**.  
   - It disappears from the marketplace for other users.

#### **C. Returning an Item**
1. As the renter, go to **"My Rentals"**.
2. Click **"Return"** on an active rental.
3. Confirm the transaction in MetaMask.
4. **Expected Result**:  
   - Refund is sent to the renter (unused penalty refunded if returned early).  
   - Item reappears in the marketplace.  
   - If late, penalty is deducted up to `max_penalty_days`.

#### **D. Auto-Return on Max Penalty**
1. Rent an item and **do not return it** within the allowed period.
2. Wait for `max_penalty_days` to expire (e.g., 5 days).
3. **Expected Result**:  
   - Item is auto-returned.  
   - Full deposit is consumed (no refund).  
   - Item becomes available again.

---

### 3. Manual Testing via Remix
1. Open [Remix IDE](https://remix.ethereum.org/) and connect to Sepolia.
2. Load your deployed contract using its address and ABI.
3. Test these functions with sample values:
   - **`listItem`**:  
     ```
     listItem("Sample Book", 100000000000000000, 350000000000000000); // 0.1 ETH/day, 0.35 ETH deposit
     ```
   - **`rentItem`**:  
     Send `0.35 ETH` with `rentItem(1, 3)` (itemId=1, 3 days).
   - **`returnItem`**:  
     Call `returnItem(1)` after varying rental periods.
4. **Verify**:  
   - Events are emitted (check Remix logs).  
   - State variables (e.g., `isAvailable`, `deposit`) update correctly.

---

### 4. Example Test Cases
| Test Case                        | Steps                                                                 | Expected Result                       |
|----------------------------------|-----------------------------------------------------------------------|---------------------------------------|
| Only owner can list items         | Non-owner tries to call `listItem`                                    | Transaction fails                     |
| Correct deposit validation         | Rent with incorrect deposit amount                                   | Transaction fails                     |
| Double rental prevention           | Rent an already rented item                                          | Transaction fails                     |
| Early return refund                | Return item before rental period ends                                | Unused penalty refunded               |
| Late return penalty deduction      | Return item 2 days late (within `max_penalty_days`)                  | Penalty = `2 × max_penalty_fees`      |
| Max penalty auto-return            | Return item after `max_penalty_days + 1`                             | Full deposit forfeited                |

---

### 5. (Optional) Automated Testing
If using Hardhat/Truffle:
1. Run tests:
2. Sample test checks:
- Listing permissions
- Deposit calculations
- Refund logic for early/late returns

---

**Tip**: Include screenshots of successful transactions and UI states in your documentation!  
