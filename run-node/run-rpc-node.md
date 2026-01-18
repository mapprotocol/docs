## What is an RPC node

RPC (Remote Procedure Call) is a protocol for requesting services from a remote computer over a network without needing
knowledge of the underlying network technology. RPC assumes the existence of certain protocols, such as TCP/UDP, to
carry the information between communicating programs. In the OSI network model, RPC spans the transport layer and the
application layer, making it easier to develop applications, including network distributed programs.

In blockchain technology, an RPC node plays a crucial role in communication. It serves as a specialized server that can
connect decentralized applications (DApps) and the blockchain network. RPC nodes enable these applications to interact
with the blockchain, facilitating tasks such as transaction verification and data retrieval.

## How RPC nodes work

Understanding the operation of an RPC node requires knowledge of the client-server model that supports its
functionality. In this architecture, the RPC node acts as a server, receiving requests from client applications. These
requests range from querying blockchain data to executing transactions. Once the server receives a request, it processes
the necessary operations and returns the results to the client application, completing the communication cycle.

The mechanism of this interaction is governed by a set of rules, often encapsulated in a protocol. In the case of
blockchain, the JSON-RPC protocol is an industry standard. This lightweight protocol uses JSON encoding to facilitate
data exchange between client and server. It defines a set of methods that client applications can invoke, whether to
initiate transactions or retrieve specific blockchain data. The JSON-RPC protocol ensures standardized and efficient
interaction between applications and the blockchain network.

The operation of an RPC node is straightforward. The client application sends a request to the RPC node, specifying the
functionality it wants to perform. This request is formatted according to the JSON-RPC protocol. The server processes
the request, executes the function, and returns the result (also in JSON format). This request and response cycle allows
decentralized applications to seamlessly operate, access blockchain data, and execute transactions.

RPC nodes operate on the client-server model, facilitating communication between decentralized applications and the
blockchain network. The JSON-RPC protocol serves as the standard for these interactions, ensuring unified and efficient
data exchange.

## Running an RPC node

```shell
atlas --datadir ./node --syncmode "full" --http --http.addr "127.0.0.1" --http.port 7445 
```