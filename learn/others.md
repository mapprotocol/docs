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
