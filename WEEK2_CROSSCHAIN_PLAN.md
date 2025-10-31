# 🌉 ZERONE - Week 2: Cross-Chain Layer Implementation

## 🎯 **Week 2 Objectives**

Build the cross-chain infrastructure to enable encrypted asset transfers between multiple blockchain networks using LayerZero V2 and FHEVM.

---

## 📋 **Tasks Breakdown**

### **Day 1-2: LayerZero V2 Integration**
- [x] Research LayerZero V2 architecture
- [ ] Install LayerZero V2 dependencies
- [ ] Create ZeroneCrossChain.sol contract
- [ ] Implement OApp (Omnichain Application) pattern
- [ ] Add cross-chain messaging
- [ ] Write cross-chain tests

### **Day 3-4: Encrypted Cross-Chain Transfers**
- [ ] Implement encrypted message encoding
- [ ] Add cross-chain balance synchronization
- [ ] Build bridge lock/unlock mechanism
- [ ] Test encrypted transfers between chains
- [ ] Add security validations

### **Day 5-6: Multi-Chain Deployment**
- [ ] Deploy to Arbitrum Sepolia
- [ ] Configure LayerZero endpoints
- [ ] Set up trusted peers
- [ ] Test end-to-end cross-chain flow
- [ ] Verify all contracts

### **Day 7: Testing & Documentation**
- [ ] Comprehensive cross-chain tests
- [ ] Gas optimization
- [ ] Update documentation
- [ ] Create cross-chain demo

---

## 🏗️ **Architecture Overview**

### **Components**

```
┌─────────────────────────────────────────────────────────────┐
│                    ZERONE CROSS-CHAIN                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────┐         LayerZero V2        ┌──────────────┐
│  │   Sepolia    │◄──────────────────────────►│   Arbitrum   │
│  │              │                              │   Sepolia    │
│  │ ZeroneVault  │   Encrypted Messages        │ ZeroneVault  │
│  │ CrossChain   │                              │ CrossChain   │
│  └──────────────┘                              └──────────────┘
│         │                                              │        │
│         │         Encrypted Balance Sync               │        │
│         └──────────────────────────────────────────────┘        │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### **Key Contracts**

1. **ZeroneCrossChain.sol** (New)
   - Extends ZeroneVault
   - Implements LayerZero OApp
   - Handles cross-chain messaging
   - Manages encrypted balance transfers

2. **ZeroneVault.sol** (Existing)
   - Core FHE vault functionality
   - Local encrypted balances
   - Token management

---

## 🔐 **Cross-Chain FHE Design**

### **Challenge**
How do we transfer encrypted balances between chains while maintaining privacy?

### **Solution: Encrypted Message Protocol**

```solidity
struct EncryptedTransferMessage {
    address token;           // Token address on destination chain
    address recipient;       // Recipient address
    euint128 encryptedAmount; // Encrypted amount (FHE)
    bytes32 messageHash;     // Message integrity check
    uint256 nonce;           // Replay protection
}
```

### **Flow**

1. **Source Chain (Sepolia)**
   ```
   User → deposit() → ZeroneVault (encrypted balance)
   User → bridgeToChain() → Lock encrypted balance
   → LayerZero send() → Encrypted message
   ```

2. **LayerZero Network**
   ```
   Relayer picks up message
   → Delivers to destination chain
   → Verifies authenticity
   ```

3. **Destination Chain (Arbitrum)**
   ```
   LayerZero delivers → lzReceive()
   → Verify message → Mint encrypted balance
   → Update recipient's encrypted balance
   ```

---

## 📦 **LayerZero V2 Integration**

### **Dependencies**
```json
{
  "@layerzerolabs/lz-evm-oapp-v2": "^2.3.0",
  "@layerzerolabs/lz-evm-protocol-v2": "^2.3.0",
  "@layerzerolabs/lz-evm-messagelib-v2": "^2.3.0"
}
```

### **Key Interfaces**

```solidity
interface IOAppCore {
    function setPeer(uint32 _eid, bytes32 _peer) external;
    function peers(uint32 _eid) external view returns (bytes32);
}

interface IOAppReceiver {
    function lzReceive(
        Origin calldata _origin,
        bytes32 _guid,
        bytes calldata _message,
        address _executor,
        bytes calldata _extraData
    ) external payable;
}

interface IOAppSender {
    function send(
        MessagingParams calldata _params,
        MessagingFee calldata _fee,
        address _refundAddress
    ) external payable returns (MessagingReceipt memory);
}
```

---

## 💻 **Implementation Plan**

### **Step 1: Install Dependencies**
```bash
npm install @layerzerolabs/lz-evm-oapp-v2 @layerzerolabs/lz-evm-protocol-v2
```

### **Step 2: Create ZeroneCrossChain.sol**
```solidity
// SPDX-License-Identifier: BSD-3-Clause-Clear
pragma solidity ^0.8.24;

import "./ZeroneVault.sol";
import {OApp, Origin, MessagingFee} from "@layerzerolabs/lz-evm-oapp-v2/contracts/oapp/OApp.sol";
import {MessagingParams, MessagingReceipt} from "@layerzerolabs/lz-evm-protocol-v2/contracts/interfaces/ILayerZeroEndpointV2.sol";

contract ZeroneCrossChain is ZeroneVault, OApp {
    // Cross-chain state
    mapping(uint32 => mapping(address => address)) public tokenMappings;
    mapping(bytes32 => bool) public processedMessages;
    
    // Events
    event CrossChainTransferInitiated(
        uint32 indexed dstEid,
        address indexed user,
        address indexed token,
        bytes32 messageId
    );
    
    event CrossChainTransferReceived(
        uint32 indexed srcEid,
        address indexed recipient,
        address indexed token,
        bytes32 messageId
    );
    
    constructor(
        address _owner,
        address _endpoint
    ) ZeroneVault(_owner) OApp(_endpoint, _owner) {}
    
    // Bridge encrypted balance to another chain
    function bridgeToChain(
        uint32 dstEid,
        address token,
        externalEuint128 encryptedAmount,
        bytes calldata inputProof,
        bytes calldata options
    ) external payable;
    
    // Receive cross-chain message
    function _lzReceive(
        Origin calldata origin,
        bytes32 guid,
        bytes calldata message,
        address executor,
        bytes calldata extraData
    ) internal override;
    
    // Set token mapping for cross-chain
    function setTokenMapping(
        uint32 dstEid,
        address localToken,
        address remoteToken
    ) external onlyOwner;
}
```

### **Step 3: Implement Cross-Chain Transfer**
```solidity
function bridgeToChain(
    uint32 dstEid,
    address token,
    externalEuint128 encryptedAmount,
    bytes calldata inputProof,
    bytes calldata options
) external payable nonReentrant {
    // 1. Verify and convert encrypted amount
    euint128 amount = FHE.fromExternal(encryptedAmount, inputProof);
    
    // 2. Lock balance on source chain
    euint128 currentBalance = encryptedBalances[msg.sender][token];
    require(FHE.isInitialized(currentBalance), "No balance");
    
    // 3. Subtract from source balance
    encryptedBalances[msg.sender][token] = FHE.sub(currentBalance, amount);
    
    // 4. Encode message
    bytes memory message = abi.encode(
        msg.sender,
        tokenMappings[dstEid][token],
        amount
    );
    
    // 5. Send via LayerZero
    MessagingParams memory params = MessagingParams({
        dstEid: dstEid,
        receiver: peers(dstEid),
        message: message,
        options: options,
        payInLzToken: false
    });
    
    _lzSend(dstEid, message, options, MessagingFee(msg.value, 0), payable(msg.sender));
}
```

### **Step 4: Implement Message Receiver**
```solidity
function _lzReceive(
    Origin calldata origin,
    bytes32 guid,
    bytes calldata message,
    address executor,
    bytes calldata extraData
) internal override {
    // 1. Prevent replay
    require(!processedMessages[guid], "Already processed");
    processedMessages[guid] = true;
    
    // 2. Decode message
    (address recipient, address token, euint128 amount) = abi.decode(
        message,
        (address, address, euint128)
    );
    
    // 3. Add to recipient's balance on destination chain
    euint128 currentBalance = encryptedBalances[recipient][token];
    
    if (FHE.isInitialized(currentBalance)) {
        encryptedBalances[recipient][token] = FHE.add(currentBalance, amount);
    } else {
        encryptedBalances[recipient][token] = amount;
    }
    
    // 4. Grant permissions
    FHE.allowThis(encryptedBalances[recipient][token]);
    FHE.allow(encryptedBalances[recipient][token], recipient);
    
    emit CrossChainTransferReceived(origin.srcEid, recipient, token, guid);
}
```

---

## 🧪 **Testing Strategy**

### **Unit Tests**
```typescript
describe("ZeroneCrossChain", () => {
  it("Should bridge encrypted balance to another chain");
  it("Should receive cross-chain message");
  it("Should prevent replay attacks");
  it("Should maintain encrypted balances");
  it("Should handle token mappings");
});
```

### **Integration Tests**
```typescript
describe("Cross-Chain Integration", () => {
  it("Should transfer encrypted balance Sepolia → Arbitrum");
  it("Should transfer encrypted balance Arbitrum → Sepolia");
  it("Should handle multiple concurrent transfers");
  it("Should maintain total supply across chains");
});
```

---

## 🌐 **Multi-Chain Deployment**

### **Networks**

| Network | Chain ID | LayerZero Endpoint | Status |
|---------|----------|-------------------|--------|
| Sepolia | 11155111 | 0x6EDCE65403992e310A62460808c4b910D972f10f | ✅ Ready |
| Arbitrum Sepolia | 421614 | 0x6EDCE65403992e310A62460808c4b910D972f10f | 📋 Planned |

### **Deployment Steps**

1. **Deploy to Sepolia** (Already done)
   ```bash
   npx hardhat deploy --network sepolia --tags CrossChain
   ```

2. **Deploy to Arbitrum Sepolia**
   ```bash
   npx hardhat deploy --network arbitrumSepolia --tags CrossChain
   ```

3. **Configure Peers**
   ```bash
   # On Sepolia: Set Arbitrum as peer
   npx hardhat setPeer --network sepolia --dst-eid 40231 --peer <arbitrum-address>
   
   # On Arbitrum: Set Sepolia as peer
   npx hardhat setPeer --network arbitrumSepolia --dst-eid 40161 --peer <sepolia-address>
   ```

4. **Set Token Mappings**
   ```bash
   # Map USDC on Sepolia to USDC on Arbitrum
   npx hardhat setTokenMapping --network sepolia --dst-eid 40231 --local <usdc-sepolia> --remote <usdc-arbitrum>
   ```

---

## 📊 **Success Criteria**

### **Week 2 Goals**
- [ ] ZeroneCrossChain.sol implemented and tested
- [ ] LayerZero V2 integration complete
- [ ] Deployed to 2+ testnets (Sepolia + Arbitrum Sepolia)
- [ ] Cross-chain encrypted transfers working
- [ ] All contracts verified on Etherscan
- [ ] 15+ cross-chain tests passing
- [ ] Documentation updated

### **Key Metrics**
- **Gas Cost**: < 500k per cross-chain transfer
- **Latency**: < 5 minutes for cross-chain confirmation
- **Security**: No replay attacks, proper validation
- **Privacy**: Encrypted amounts never revealed

---

## 🚀 **Next Steps (Immediate)**

1. **Install LayerZero V2 packages**
2. **Create ZeroneCrossChain.sol**
3. **Write cross-chain tests**
4. **Deploy to Arbitrum Sepolia**
5. **Test end-to-end flow**

---

**Let's build the future of cross-chain privacy! 🌉🔐**

