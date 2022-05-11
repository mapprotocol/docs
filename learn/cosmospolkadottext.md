

<table>
    <tr>
        <th>Technical Mechanism</th>
        <th>Cosmos & Polkadot</th>
        <th>MAP Protocol</th>
    </tr>
    <tr>
        <td>Similarities</td>
        <td colspan="2">
            1. Interoperate with other chains through relay chain<br/>
            2. Verification Finality of cross-chain assets and data through Light-clients<br/>
            3. Cross-chain interoperability is achieved through modules including light-client, cross-chain messaging system, and cross-chain assets and data management.
        </td>
    </tr>
    <tr>
        <td rowspan="3">Differences</td>
        <td>
            The relay chain does not support cross-chain between EVM and other heterogeneous chains. EVM and other heterogeneous chains need to modify the in-chain structure to become compatible.
        </td>
        <td>
            MAP Relay Chain expands to all heterogeneous chains proactively by adding other heterogeneous chains’ signatures, hashes, mining, and Merkle proof calculation to the EVM level.
        </td>
    </tr>
    <tr>
        <td>
            Require EVM and other heterogeneous chains to embed the SDK to the bottom layer of the blockchain. The SDK contains cross-chain execution and verification programs such as Light-clients, asset and data management system, and cross-chain messengers.
        </td>
        <td>
            Proactively deploy the cross-chain execution and verification components on-chain or inter-chain, including assets and data management, cross-chain messengers, and Light-client for verification by way of smart contracts on-chain or inter-chain programs.
        </td>
    </tr>
    <tr>
        <td>
            After embedding the SDK, the modified chains become interconnected with the relay chain, thus forming a cross-chain network.
        </td>
        <td>
            Every chain is naturally interoperable with MAP Relay Chain, thus forming a cross-chain network
        </td>
    </tr>
    <tr>
        <td>Target of Service</td>
        <td>
            Blockchain Mainnet Developers<br/>
            - Relay chain only serves mainnet developers, not Dapp developers.<br/>
            - Cross-chain Dapps can only deploy in cross-chain ecological sub-chains, increasing the difficulty of promotions. <br/> 
            - Take Thorchain as an example. Thorchain is an ecological sub-chain of Cosmos. But Thorchain has to use its self-developed MPC as the cross-chain solution instead of using Cosmos’ cross-chain services. Same situation happens to Terra as well.<br/>
            - No cross-chain Dapps on Polkadot so far.
        </td>
        <td>
            Cross-chain Dapp Developers<br/>
            Native cross-chain applications are developed directly on MAP Relay Chain (EVM).<br/>
            The relay chain should become the assembly for cross-chain assets and data, which Cosmos and Polkadot mistakenly ignored. <br/> 
            Cross-chain Dapps can benefit from a shared cross-chain assets and data pool through the deployment on MAP Relay Chain.
        </td>
    </tr>
    <tr>
        <td>Coverage of Blockchains</td>
        <td>
            Minimal<br/>
            Only covers the chains that are willing to implement the cross-chain communication SDK on its chains and modify the bottom layer to be isomorphic with its relay chain.
        </td>
        <td>
            All Chains<br/>
            MAP Relay Chain is compatible and interoperable with all chains by proactively deploying cross-chain communication components on-chain or inter-chain and extending EVM capability with heterogeneous chains'features  through precompiled contracts to facilitate gas-efficient light client construction.
        </td>
    </tr>
</table>
