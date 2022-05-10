## MAP Protocol VS cross-chain solutions without relay chain

The lightweight solutions, including MAP Protocol, achieved cross-chain functionality by deploying cross-chain communication components on-chain or inter-chain, which are better solutions than Cosmos & Polkadot (require embedding SDK to the bottom layer of the blockchain). MPC (Multi-Party Computation) technology is easy to develop but has obvious security risks. Using Oracle to conduct cross-chain verification is ambiguous , and the risk of colluding with messengers always exists. Using Light-client to conduct cross-chain verification is the ultimate security mechanism adopted in MAP Protocol, Cosmos, and Polkadot.

The Light-client verification mechanism will lead to massive consumption of gas fees for cross-chain activities between heterogeneous chains. The better way is to use a relay chain, compatible with multiple chains' algorithm, thus enabling a homogeneous cross-chain and forming the ultimate cross-chain verification network. MAP Protocol has developed the MAP Relay Chain as such. As the cross-chain asset and data assembly, MAP Relay Chain supports the native deployment of cross-chain Dapps, including cross-chain bridge applications,  differentiating MAP Protocol from all the other competitors.

## Comparisons between MAP Protocol and other lightweight cross-chain solutions with no relay chain

<table>
    <tr>
        <th></th>
        <th>Asset Management</th>
        <th>Cross-chain Verification</th>
        <th>Security</th>
        <th>Security In Details</th>
    </tr>
    <tr>
        <td>Thorchain</td>
        <td rowspan="2"> MPC </td>
        <td rowspan="2">MPC Node Self-verification</td>
        <td rowspan="2">Lowest</td>
        <td rowspan="2">
            - MPC nodes are responsible for managing cross-chain assets and verifying the cross-chain validity of cross-chain transactions.<br>
            - MPC nodes are vulnerable to cyber-attacks.
        </td>
    </tr>
    <tr>
        <td>Multichain</td>
    </tr>
    <tr>
        <td> Celer </td>
        <td rowspan="2"> MPC&PoS </td>
        <td rowspan="2"> MPC Node Self-verification</td>
        <td rowspan="2"> Low </td>
        <td rowspan="2"> 
            - With PoS, MPC nodes are rotated based on certain mechanisms and required to stake tokens to reduce the risk of embezzlement. <br>
            - Off-chain ledger and MPC nodes are still vulnerable to cyber-attacks.
        </td>
    </tr>
    <tr>
        <td>Axelar</td>
    </tr>
    <tr>
        <td>Layerzero (Stargate) </td>
        <td> Non-custodial </td>
        <td> Oracle Verification </td>
        <td> Non-finality </td>
        <td> 
            - Oracle verification is ambiguous and not final. <br>
            - Oracle requires asset staking, which reduces the risk of cheating. <br>
            - The risk of oracle colluding with messengers always exists. <br>
        </td>
    </tr>
    <tr>
        <td>MAP Protocol</td>
        <td> Non-custodial</td>
        <td> Light-client Verification</td>
        <td> Finality </td>
        <td> Light Client adopts mature SPV technology, an independent self-verification mechanism without any security blind spots.</td>
    </tr>
</table>
