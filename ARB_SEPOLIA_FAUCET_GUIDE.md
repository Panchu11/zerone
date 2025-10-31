# 💧 Arbitrum Sepolia Testnet ETH Faucet Guide

## 🎯 **Quick Summary**

You need **Arbitrum Sepolia ETH** to deploy contracts on Arbitrum Sepolia testnet.

**Your Wallet Address**: Check with `npx hardhat vars get MNEMONIC` and derive address

---

## 🚰 **Method 1: Alchemy Faucet (RECOMMENDED - No Sepolia ETH Required)**

### **✅ Best Option - Direct ARB Sepolia ETH**

**URL**: https://www.alchemy.com/faucets/arbitrum-sepolia

**Steps**:
1. Visit https://www.alchemy.com/faucets/arbitrum-sepolia
2. Sign in with:
   - Google account, OR
   - Email, OR
   - Wallet (MetaMask)
3. Enter your wallet address
4. Complete CAPTCHA
5. Click "Send Me ETH"

**Amount**: 0.1 ARB Sepolia ETH per day
**Requirements**: Free Alchemy account (no credit card needed)
**Wait Time**: Instant

---

## 🚰 **Method 2: QuickNode Faucet**

**URL**: https://faucet.quicknode.com/arbitrum/sepolia

**Steps**:
1. Visit https://faucet.quicknode.com/arbitrum/sepolia
2. Sign in with Twitter/GitHub
3. Enter your wallet address
4. Click "Continue"
5. Complete verification

**Amount**: 0.05 ARB Sepolia ETH
**Requirements**: Twitter or GitHub account
**Wait Time**: Instant

---

## 🚰 **Method 3: Chainlink Faucet**

**URL**: https://faucets.chain.link/arbitrum-sepolia

**Steps**:
1. Visit https://faucets.chain.link/arbitrum-sepolia
2. Connect wallet OR enter address
3. Complete CAPTCHA
4. Click "Send request"

**Amount**: 0.01 ARB Sepolia ETH
**Requirements**: None (but limited)
**Wait Time**: Instant

---

## 🚰 **Method 4: Bridge from Sepolia (If you have Sepolia ETH)**

If you already have Sepolia ETH, you can bridge it to Arbitrum Sepolia.

**URL**: https://bridge.arbitrum.io/?destinationChain=arbitrum-sepolia&sourceChain=sepolia

**Steps**:
1. Visit the Arbitrum bridge
2. Connect your wallet
3. Select "Sepolia" → "Arbitrum Sepolia"
4. Enter amount (minimum 0.01 ETH)
5. Click "Move funds to Arbitrum Sepolia"
6. Confirm transaction
7. Wait ~10 minutes for bridging

**Requirements**: Sepolia ETH in your wallet
**Wait Time**: ~10 minutes

---

## 🚰 **Method 5: Triangle Faucet (Multi-Faucet)**

**URL**: https://faucet.triangleplatform.com/arbitrum/sepolia

**Steps**:
1. Visit the Triangle faucet
2. Enter your wallet address
3. Complete CAPTCHA
4. Click "Request"

**Amount**: 0.001 ARB Sepolia ETH
**Requirements**: None
**Wait Time**: Instant

---

## 📋 **Step-by-Step: Using Alchemy Faucet (Recommended)**

### **1. Get Your Wallet Address**

Run this command to see your wallet address:
```bash
npx hardhat run scripts/get-address.ts
```

Or derive it from your mnemonic using a tool like:
- https://iancoleman.io/bip39/
- Enter your mnemonic
- Select "ETH - Ethereum"
- Copy the first address

### **2. Visit Alchemy Faucet**

Go to: https://www.alchemy.com/faucets/arbitrum-sepolia

### **3. Sign In**

- Click "Login" in the top right
- Choose sign-in method:
  - **Google** (easiest)
  - **Email**
  - **Wallet** (MetaMask)

### **4. Request Testnet ETH**

- Paste your wallet address
- Complete CAPTCHA
- Click "Send Me ETH"
- Wait 5-10 seconds

### **5. Verify Receipt**

Check your balance:
```bash
npx hardhat run scripts/check-balance.ts --network arbitrumSepolia
```

Or check on Arbiscan:
https://sepolia.arbiscan.io/address/YOUR_ADDRESS

---

## 🔍 **Verify Your Balance**

### **Option 1: Using Hardhat**

Create `scripts/check-balance.ts`:
```typescript
import { ethers } from "hardhat";

async function main() {
  const [deployer] = await ethers.getSigners();
  const balance = await ethers.provider.getBalance(deployer.address);
  
  console.log("Address:", deployer.address);
  console.log("Balance:", ethers.formatEther(balance), "ETH");
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

Run:
```bash
npx hardhat run scripts/check-balance.ts --network arbitrumSepolia
```

### **Option 2: Using Arbiscan**

Visit: https://sepolia.arbiscan.io/address/YOUR_ADDRESS

---

## 💰 **How Much Do You Need?**

### **For Deployment**:
- **ZeroneCrossChain**: ~0.015 ETH
- **3 Mock Tokens**: ~0.003 ETH each = 0.009 ETH
- **Configuration**: ~0.005 ETH
- **Buffer**: 0.01 ETH

**Total Recommended**: **0.05 ETH**

### **Faucet Strategy**:
1. **Alchemy**: 0.1 ETH (covers everything!) ✅
2. **QuickNode**: 0.05 ETH (just enough) ✅
3. **Chainlink**: 0.01 ETH (need multiple sources)

---

## 🎯 **Recommended Approach**

### **Best Strategy**:

1. **Use Alchemy Faucet** (0.1 ETH - more than enough!)
   - https://www.alchemy.com/faucets/arbitrum-sepolia
   - Sign in with Google
   - Get 0.1 ETH instantly

2. **Verify Balance**:
   ```bash
   npx hardhat run scripts/check-balance.ts --network arbitrumSepolia
   ```

3. **Deploy**:
   ```bash
   npx hardhat deploy --network arbitrumSepolia --tags CrossChain
   ```

---

## ⚠️ **Troubleshooting**

### **"Insufficient funds" error**

**Problem**: Not enough ETH in wallet

**Solution**:
1. Check balance: `npx hardhat run scripts/check-balance.ts --network arbitrumSepolia`
2. If balance is 0, use faucet again
3. Try multiple faucets to accumulate enough

### **"Faucet limit reached"**

**Problem**: Used faucet too many times

**Solution**:
1. Wait 24 hours, OR
2. Try a different faucet, OR
3. Use a different wallet address, OR
4. Bridge from Sepolia if you have Sepolia ETH

### **"Transaction failed"**

**Problem**: Network congestion or gas issues

**Solution**:
1. Wait a few minutes and try again
2. Increase gas limit in hardhat.config.ts:
   ```typescript
   arbitrumSepolia: {
     accounts: getAccounts(),
     chainId: 421614,
     url: `https://arbitrum-sepolia.infura.io/v3/${INFURA_API_KEY}`,
     gas: 10000000, // Add this
   },
   ```

---

## 📞 **Need Help?**

### **Check Faucet Status**:
- Alchemy Status: https://status.alchemy.com/
- QuickNode Status: https://status.quicknode.com/

### **Alternative Faucets** (if main ones are down):
1. https://faucet.triangleplatform.com/arbitrum/sepolia
2. https://www.l2faucet.com/arbitrum
3. https://faucet.paradigm.xyz/ (requires Twitter)

---

## ✅ **Quick Checklist**

Before deploying to Arbitrum Sepolia:

- [ ] Have wallet address
- [ ] Got ARB Sepolia ETH from faucet (0.05+ ETH)
- [ ] Verified balance on Arbiscan
- [ ] Hardhat config has `arbitrumSepolia` network
- [ ] Infura API key is set
- [ ] Mnemonic is set

---

## 🚀 **Ready to Deploy!**

Once you have ARB Sepolia ETH:

```bash
# Deploy to Arbitrum Sepolia
npx hardhat deploy --network arbitrumSepolia --tags CrossChain

# Verify contracts
npx hardhat verify --network arbitrumSepolia <CONTRACT_ADDRESS> "<OWNER>" "<ENDPOINT>"
```

---

**Good luck! 🎉**

