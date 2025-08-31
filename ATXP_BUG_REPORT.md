# ATXP Wallet Address Case Sensitivity Issue

## Problem Description
When making payments through the ATXP service, there's a case sensitivity issue with Ethereum wallet addresses. The system fails with a payment mismatch error due to different letter casing of the same wallet address.

## Error Message
```
Payment source does not match authenticated user: 
0x5609eba7ee2d356ad875f4af3170769eeaf0cfa1 (all lowercase) 
!== 
0x5609EbA7ee2d356Ad875f4af3170769EEAf0CFA1 (mixed case)
```

## Environment
- **ATXP Client Version**: (Run `npm list @atxp/client` to get the exact version)
- **Node.js Version**: (Run `node -v` to get the version)
- **Environment**: Development
- **Network**: Base

## Steps to Reproduce
1. Initialize ATXP client with a connection string
2. Make a request that requires payment (e.g., image generation)
3. The payment fails due to case sensitivity mismatch

## Relevant Code Snippets

### Server Initialization
```typescript
// server.ts
const account = new ATXPAccount(ATXP_CONNECTION_STRING, {
  network: 'base'
});
```

### Image Generation Request
```typescript
const imageClient = await atxpClient({
  mcpServer: 'https://image.mcp.atxp.ai',
  account: account,
  allowedAuthorizationServers: [
    'http://localhost:3001', 
    'https://auth.atxp.ai', 
    'https://atxp-accounts-staging.onrender.com/'
  ],
  logger: new ConsoleLogger({level: LogLevel.DEBUG})
});
```

## Full Error Stack Trace
```
[atxp] ATXP: payment to https://auth.atxp.ai/payment-request/1xCLyDbbQjrhPFX2C6tYB failed: HTTP 400 
{
  "status": "error",
  "message": "Payment source does not match authenticated user: 0x5609eba7ee2d356ad875f4af3170769eeaf0cfa1 !== 0x5609EbA7ee2d356Ad875f4af3170769EEAf0CFA1"
}
```

## Expected Behavior
Wallet address comparison should be case-insensitive as per Ethereum address standards (EIP-55). The same wallet address with different letter casing should be treated as identical.

## Additional Context
- The connection is established successfully
- The issue only occurs during the payment phase
- The wallet address is being converted to lowercase somewhere in the payment flow
- The authentication works fine, but payment fails due to case mismatch

## Suggested Fixes
1. Make wallet address comparison case-insensitive in the payment processing flow
2. Normalize wallet addresses to a consistent case before comparison
3. Update the error message to be more descriptive about the case sensitivity issue

## Reproduction Steps for Support
1. Use a wallet address with mixed case (e.g., 0x5609EbA7ee2d356Ad875f4af3170769EEAf0CFA1)
2. Generate a connection token
3. Attempt to make a paid request through the ATXP API
4. Observe the case sensitivity error
