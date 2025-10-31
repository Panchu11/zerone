# 🔧 ZERONE Troubleshooting Guide

## Issues Found and Solutions

This guide addresses the console errors you're experiencing and provides step-by-step solutions.

---

## 🔴 Issue 1: Transaction Failed - "TokenNotSupported"

### **Problem:**
Your deposit transaction failed with "execution reverted" because the tokens (USDC/USDT/DAI) are **not added to the vault's supported tokens list**.

### **Root Cause:**
The `ZeroneVault` contract has a check:
```solidity
function deposit(address token, uint128 amount) external nonReentrant {
    if (!supportedTokens[token]) revert TokenNotSupported(); // ← This is failing
    ...
}
```

### **Solution:**

#### **Step 1: Navigate to contracts directory**
```bash
cd zerone-contracts
```

#### **Step 2: Run the script to add tokens to vault**
```bash
npx hardhat run scripts/add-tokens-to-vault.ts --network sepolia
```

This script will:
- ✅ Check if you're the vault owner
- ✅ Check which tokens are already supported
- ✅ Add USDC, USDT, and DAI to the vault's supported tokens
- ✅ Verify all tokens were added successfully

#### **Expected Output:**
```
🔧 Adding Supported Tokens to ZeroneVault...

Using account: 0x40136eFBc6906abA5a1EcBD55103Ac1D89825Fc6
Vault Address: 0x1608407298c28e0409B7CEBA2AD75228d0c4983a
...

✅ All tokens successfully added to vault!
🎉 You can now deposit these tokens into the vault from the frontend!
```

#### **Step 3: Verify on Etherscan**
After running the script, verify on Etherscan that the transactions succeeded:
- Go to: https://sepolia.etherscan.io/address/0x1608407298c28e0409B7CEBA2AD75228d0c4983a
- Check recent transactions for `addSupportedToken` calls

#### **Step 4: Try depositing again**
Once tokens are added, try depositing from the frontend again. It should work now!

---

## 🔴 Issue 2: WalletConnect Configuration Error

### **Problem:**
```
GET https://api.web3modal.org/appkit/v1/config?projectId=YOUR_PROJECT_ID 403 (Forbidden)
```

### **Root Cause:**
The placeholder `YOUR_PROJECT_ID` is still in the code, causing Web3Modal/WalletConnect to fail.

### **Solution:**

✅ **Already Fixed!** I've updated `zerone-frontend/lib/wagmi.ts` to:
- Only use WalletConnect if a valid project ID is provided
- Fall back to injected provider (MetaMask) if no project ID

### **Optional: Enable WalletConnect**

If you want to enable WalletConnect support:

#### **Step 1: Get a free WalletConnect Project ID**
1. Go to https://cloud.walletconnect.com
2. Create a free account
3. Create a new project
4. Copy your Project ID

#### **Step 2: Add to environment variables**
Create or update `zerone-frontend/.env.local`:
```bash
NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID=your_actual_project_id_here
```

#### **Step 3: Restart dev server**
```bash
cd zerone-frontend
npm run dev
```

**Note:** WalletConnect is optional. The app works fine with just MetaMask (injected provider).

---

## 🔴 Issue 3: React Hydration Error

### **Problem:**
```
Uncaught Error: Minified React error #418
```

### **Root Cause:**
HTML mismatch between server-side rendering and client-side hydration.

### **Solution:**

✅ **Already Fixed!** I've added `suppressHydrationWarning` to `app/layout.tsx`:
```tsx
<html lang="en" suppressHydrationWarning>
  <body className={inter.className} suppressHydrationWarning>
```

This suppresses the warning for known client-side only content (like wallet connections).

---

## 🔴 Issue 4: Multiple Wallet Provider Conflicts

### **Problem:**
```
evmAsk.js:5 Uncaught TypeError: Cannot redefine property: ethereum
MetaMask encountered an error setting the global Ethereum provider
```

### **Root Cause:**
You have multiple wallet extensions installed (e.g., MetaMask + another wallet like Coinbase Wallet, Brave Wallet, etc.) that are both trying to inject `window.ethereum`.

### **Solutions:**

#### **Option 1: Disable conflicting wallet extensions (Recommended)**
1. Open your browser extensions
2. Disable all wallet extensions except MetaMask
3. Refresh the page

#### **Option 2: Use MetaMask's priority mode**
1. Open MetaMask settings
2. Go to Advanced settings
3. Enable "Prefer MetaMask" or similar option

#### **Option 3: Ignore the warning**
- This warning doesn't break functionality
- The app will still work with MetaMask
- It's just a console warning from competing wallet extensions

---

## 📋 Complete Fix Checklist

Follow these steps in order:

### **1. Add Tokens to Vault** ⚠️ **CRITICAL - DO THIS FIRST**
```bash
cd zerone-contracts
npx hardhat run scripts/add-tokens-to-vault.ts --network sepolia
```

### **2. Restart Frontend** (to apply WalletConnect fix)
```bash
cd zerone-frontend
npm run dev
```

### **3. Clear Browser Cache**
- Press `Ctrl+Shift+Delete` (Windows) or `Cmd+Shift+Delete` (Mac)
- Clear cached images and files
- Or use incognito/private mode

### **4. Disable Extra Wallet Extensions**
- Keep only MetaMask enabled
- Disable Coinbase Wallet, Brave Wallet, etc.

### **5. Test Deposit**
1. Connect wallet
2. Go to Vault tab
3. Select USDC
4. Enter amount (e.g., 10 USDC)
5. Click "Approve" first (if needed)
6. Then click "Deposit to Vault"
7. Confirm transaction in MetaMask

---

## 🧪 Testing Your Fixes

### **Test 1: Check Console Errors**
After applying fixes, you should see:
- ✅ No more WalletConnect 403 errors
- ✅ No more React hydration errors
- ⚠️ Wallet provider conflict warnings may remain (harmless)

### **Test 2: Verify Token Support**
Run this in your browser console on the frontend:
```javascript
// Check if USDC is supported
const vault = new ethers.Contract(
  '0x1608407298c28e0409B7CEBA2AD75228d0c4983a',
  ['function supportedTokens(address) view returns (bool)'],
  provider
);
await vault.supportedTokens('0x7A78aeBE9009E03168f8497e4F0fBe2CedBdC236'); // Should return true
```

### **Test 3: Try a Small Deposit**
1. Make sure you have test USDC (get from faucet if needed)
2. Approve vault to spend USDC
3. Deposit 1 USDC
4. Transaction should succeed ✅

---

## 🆘 Still Having Issues?

### **Check Vault Owner**
Make sure you're using the correct wallet that deployed the vault:
```bash
cd zerone-contracts
npx hardhat run scripts/get-address.ts --network sepolia
```

### **Check Token Balances**
Make sure you have test tokens:
```bash
npx hardhat run scripts/check-balance.ts --network sepolia
```

### **View Transaction Details**
If deposit still fails, check the transaction on Etherscan:
- Look for the error message
- Check if approval was successful
- Verify gas settings

---

## 📚 Additional Resources

- **Vault Contract:** https://sepolia.etherscan.io/address/0x1608407298c28e0409B7CEBA2AD75228d0c4983a
- **USDC Token:** https://sepolia.etherscan.io/address/0x7A78aeBE9009E03168f8497e4F0fBe2CedBdC236
- **WalletConnect Cloud:** https://cloud.walletconnect.com
- **Sepolia Faucet:** https://sepoliafaucet.com

---

## ✅ Summary

**Critical Fix (Required):**
1. ✅ Run `add-tokens-to-vault.ts` script to add supported tokens

**Already Fixed (No action needed):**
2. ✅ WalletConnect configuration updated
3. ✅ React hydration warning suppressed

**Optional (Recommended):**
4. ⚠️ Disable extra wallet extensions to reduce console noise

After completing step 1, your deposits should work! 🎉

