# Reown (Wallet Connect)

We recommend using the `wagmi` library alongside ``Reown (WalletConnect)`` for better integration and support. Below is an example of the necessary configuration and adjustments when working with Reown.

```js
import { createAppKit } from '@reown/appkit/react';
import { WagmiProvider } from 'wagmi';
import { defineChain, sepolia } from '@reown/appkit/networks';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { WagmiAdapter } from '@reown/appkit-adapter-wagmi';

const queryClient = new QueryClient();

const projectId = 'Project-id-from-reown';

const metadata = {
  name: 'DApp-name',
  description: 'DApp-desc',
  url: 'DApp-URL',
  icons: ['DApp-icon-url'],
};

const rosettanetSepolia = defineChain({
  id: 1381192787,
  caipNetworkId: 'eip155:1381192787',
  chainNamespace: 'eip155',
  name: 'Rosettanet',
  nativeCurrency: {
    decimals: 18,
    name: 'Starknet Token',
    symbol: 'STRK',
  },
  rpcUrls: {
    default: {
      http: ['Rosettanet Sepolia RPC URL'],
    },
  },
  blockExplorers: {
    default: { name: 'Voyager Sepolia', url: 'https://sepolia.voyager.online' },
  },
  contracts: {
  },
})

const networks = [rosettanetSepolia, sepolia];

const wagmiAdapter = new WagmiAdapter({
  networks,
  projectId,
});

export const reownConfig = wagmiAdapter.wagmiConfig;

createAppKit({
  adapters: [wagmiAdapter],
  networks,
  projectId,
  metadata,
});

export function App({ children }) {
  return (
    <WagmiProvider config={wagmiAdapter.wagmiConfig}>
      <QueryClientProvider client={queryClient}>{/** ... */}</QueryClientProvider>
    </WagmiProvider>
  );
}
```