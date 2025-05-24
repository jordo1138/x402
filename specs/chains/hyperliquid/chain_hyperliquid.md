# Hyperliquid Chain Support for x402

## Summary

This specification defines how x402 protocol supports Hyperliquid's HyperEVM, focusing on integration with HyperCore for native asset management. Hyperliquid is a high-performance L1 blockchain featuring dual architecture: HyperCore (native trading engine) and HyperEVM (EVM-compatible execution layer). This enables seamless integration between traditional DeFi and native order book liquidity.

## Network Identifiers

### Hyperliquid Mainnet (HyperEVM)
- **Network ID**: `hyperliquid-mainnet`
- **Chain ID**: 999 (0x3E7)
- **RPC**: https://rpc.hyperliquid.xyz/evm
- **Block Explorer**: https://app.hyperliquid.xyz/explorer
- **Native Currency**: HYPE
- **Architecture**: Dual-block system (fast small blocks, slow big blocks)

### Hyperliquid Testnet
- **Network ID**: `hyperliquid-testnet`
- **Chain ID**: 998 (0x3E6)
- **RPC**: https://rpc-testnet.hyperliquid.xyz/evm
- **Block Explorer**: https://testnet-explorer.hyperliquid.xyz
- **Native Currency**: HYPE (testnet)

## Supported Assets

### USDC on Hyperliquid
**Note**: Hyperliquid's USDC exists primarily on HyperCore (native layer) with bridging to HyperEVM

- **HyperCore USDC** (Primary)
  - **Network Layer**: HyperCore (native)
  - **Symbol**: USDC
  - **Decimals**: 6
  - **Transfer Method**: Native HyperCore transfers
  - **Gas**: Paid in HYPE on HyperCore

- **HyperEVM USDC** (Bridged)
  - **Contract**: [To be determined - pending HyperEVM USDC deployment]
  - **Symbol**: USDC
  - **Decimals**: 6
  - **EIP-3009**: [To be verified upon contract deployment]
  - **Transfer Method**: Standard ERC20 with HyperCore bridge

### HYPE Token
- **HyperCore HYPE**: Native gas and governance token
- **HyperEVM HYPE**: ERC20-wrapped version for DeFi applications
- **Bridge Address**: `0x2222222222222222222222222222222222222222` (special transfer address)

## Scheme Support

Hyperliquid's x402 implementation focuses on **HyperCore integration** rather than traditional EVM schemes, leveraging native asset transfers and the unified state architecture.

### HyperCore Native Scheme (Recommended)
The preferred approach utilizes Hyperliquid's native HyperCore asset management:

- **Asset Layer**: HyperCore USDC (native)
- **Signature Method**: Hyperliquid native signatures
- **Settlement**: Direct HyperCore transfers
- **Gas**: HYPE on HyperCore (lower cost than HyperEVM)
- **Integration**: Seamless access to native order book liquidity

### EVM Scheme (Alternative)
For compatibility with existing EVM infrastructure:

- **Asset Layer**: HyperEVM USDC (bridged)
- **Signature Method**: EIP-712 (pending USDC contract EIP-3009 support)
- **Settlement**: Standard ERC20 transfers
- **Gas**: HYPE on HyperEVM
- **Bridge Required**: HyperCore ↔ HyperEVM transfers

## Architecture Considerations

### Dual-State Integration
Hyperliquid's unique architecture enables x402 to leverage both layers:

1. **HyperCore Advantages**:
   - Direct access to native order book liquidity
   - Lower gas costs for transfers
   - High-performance settlement
   - Native USDC without bridge risk

2. **HyperEVM Advantages**:
   - EVM compatibility for existing tools
   - Smart contract composability
   - Standard wallet integration
   - Familiar developer experience

### Cross-Layer Transfers
Assets can move between HyperCore and HyperEVM:
- **HyperCore → HyperEVM**: Transfer via bridge (gas cost in HyperCore)
- **HyperEVM → HyperCore**: Transfer via smart contract (gas cost in HyperEVM)
- **Special Addresses**: Certain addresses enable direct transfers

## Implementation Strategy

### Phase 1: HyperCore Native Integration (Recommended)
Implement x402 using HyperCore's native capabilities:

```json
{
  "x402Version": 1,
  "scheme": "hypercore-native",
  "network": "hyperliquid-mainnet",
  "payload": {
    "signature": "[Hyperliquid native signature]",
    "authorization": {
      "from": "[HyperCore address]",
      "to": "[Recipient HyperCore address]",
      "amount": "10000",
      "asset": "USDC",
      "nonce": "[Transfer nonce]",
      "validBefore": "1740672154"
    }
  }
}
```

### Phase 2: HyperEVM Compatibility
Add EVM compatibility for broader ecosystem support:

```json
{
  "x402Version": 1,
  "scheme": "exact",
  "network": "hyperliquid-mainnet",
  "payload": {
    "signature": "[EIP-712 signature]",
    "authorization": {
      "from": "0x857b06519E91e3A54538791bDbb0E22373e36b66",
      "to": "0x209693Bc6afc0C5328bA36FaF03C514EF312287C",
      "value": "10000",
      "validAfter": "1740672089",
      "validBefore": "1740672154",
      "nonce": "0xf3746613c2d920b5fdabc0856f2aeb2d4f88ee6037b8cc5d04a71a4462f13480"
    }
  }
}
```

## Gas Considerations

### HyperCore Gas
- **Token**: HYPE
- **Cost**: Extremely low for native transfers
- **Pricing**: Fixed cost model
- **Performance**: Sub-second confirmation

### HyperEVM Gas  
- **Token**: HYPE
- **Model**: EIP-1559 (base fee + priority fee)
- **Throughput**: Initially limited, scaling over time
- **Spikes**: Possible during high demand periods

## Security Considerations

### Asset Verification
- **Verify Layer**: Confirm whether using HyperCore or HyperEVM USDC
- **Bridge Security**: HyperCore assets have no bridge risk
- **Contract Audits**: Verify any HyperEVM USDC contract implementations

### Signature Validation
- **HyperCore**: Use Hyperliquid's native signature verification
- **HyperEVM**: Standard EIP-712 verification with chain ID 999
- **Replay Protection**: Ensure proper nonce handling across layers

### Network Verification
- **Chain ID**: Always verify correct chain ID (999 for mainnet)
- **RPC Security**: Use official Hyperliquid RPC endpoints
- **State Consistency**: Account for dual-layer architecture

## Use Cases

### High-Frequency Payments
- **Native Transfers**: Sub-second HyperCore settlements
- **Order Book Integration**: Direct access to native liquidity
- **Trading Fees**: Micropayments for trading API access

### DeFi Integration
- **Yield Strategies**: Payment for yield optimization services
- **Lending Protocols**: Interest payments via x402
- **Cross-Chain Services**: Bridge fees and cross-chain payments

### Content & AI Services
- **Real-Time Content**: Ultra-fast payments for content access
- **AI API Calls**: Micropayments for AI model usage
- **Gaming**: In-game transactions with minimal latency

## Implementation Notes

1. **Prioritize HyperCore**: Native integration provides better UX and lower costs
2. **Bridge Awareness**: Understand asset layer when implementing settlements
3. **Performance Optimization**: Leverage Hyperliquid's high-performance architecture
4. **Ecosystem Integration**: Consider integration with Hyperliquid's native trading features

## Testing Strategy

### HyperCore Testing
- Test native USDC transfers between accounts
- Verify signature schemes and nonce handling
- Test integration with order book liquidity

### HyperEVM Testing  
- Test ERC20 transfers (when USDC contract available)
- Verify EIP-712 signature validation
- Test cross-layer bridge functionality

### Performance Testing
- Measure settlement times across both layers
- Test gas cost optimization strategies
- Verify throughput under various load conditions

## Appendix

### Network Configuration

```typescript
const HYPERLIQUID_NETWORKS = {
  'hyperliquid-mainnet': {
    chainId: 999,
    hypercoreRpc: 'https://api.hyperliquid.xyz',
    hyperevmRpc: 'https://rpc.hyperliquid.xyz/evm',
    nativeAssets: ['HYPE', 'USDC'],
    schemes: ['hypercore-native', 'exact']
  },
  'hyperliquid-testnet': {
    chainId: 998, 
    hypercoreRpc: 'https://api-testnet.hyperliquid.xyz',
    hyperevmRpc: 'https://rpc-testnet.hyperliquid.xyz/evm',
    nativeAssets: ['HYPE', 'USDC'],
    schemes: ['hypercore-native', 'exact']
  }
};
```

### Integration Priorities

1. **Phase 1**: HyperCore native USDC transfers
2. **Phase 2**: HyperEVM bridge integration  
3. **Phase 3**: Cross-layer payment routing
4. **Phase 4**: Native order book integration