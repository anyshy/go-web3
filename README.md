
# Go-Web3

## JsonRPC

```
package main

import (
	"fmt"
	
	web3 "github.com/anyshy/go-web3"
	"github.com/anyshy/go-web3/jsonrpc"
)

func main() {
	client, err := jsonrpc.NewClient("https://mainnet.infura.io")
	if err != nil {
		panic(err)
	}

	number, err := client.Eth().BlockNumber()
	if err != nil {
		panic(err)
	}

	header, err := client.Eth().GetBlockByNumber(web3.BlockNumber(number), true)
	if err != nil {
		panic(err)
	}

	fmt.Println(header)
}
```

## ABI

The ABI codifier uses randomized tests with e2e integration tests with a real Geth client to ensure that the codification is correct and provides the same results as the AbiEncoder from Solidity. 

To use the library import:

```
"github.com/anyshy/go-web3/abi"
```

Declare basic objects:

```
typ, err := abi.NewType("uint256")
```

or 

```
typ = abi.MustNewType("uint256")
```

and use it to encode/decode the data:

```
num := big.NewInt(1)

encoded, err := typ.Encode(num)
if err != nil {
    panic(err)
}

decoded, err := typ.Decode(encoded) // decoded as interface
if err != nil {
    panic(err)
}

num2 := decoded.(*big.Int)
fmt.Println(num.Cmp(num2) == 0) // num == num2
```

You can also codify structs as Solidity tuples:

```
import (
	"fmt"
    
	web3 "github.com/anyshy/go-web3"
	"github.com/anyshy/go-web3/abi"
	"math/big"
)

func main() {
	typ := abi.MustNewType("tuple(address a, uint256 b)")

	type Obj struct {
		A web3.Address
		B *big.Int
	}
	obj := &Obj{
		A: web3.Address{0x1},
		B: big.NewInt(1),
	}

	// Encode
	encoded, err := typ.Encode(obj)
	if err != nil {
		panic(err)
	}

	// Decode output into a map
	res, err := typ.Decode(encoded)
	if err != nil {
		panic(err)
	}

	// Decode into a struct
	var obj2 Obj
	if err := typ.DecodeStruct(encoded, &obj2); err != nil {
		panic(err)
	}

	fmt.Println(res)
	fmt.Println(obj)
}
```

## ENS

Resolve names on the Ethereum Name Service registrar.

```
import (
    "fmt"

    web3 "github.com/anyshy/go-web3"
    "github.com/anyshy/go-web3/jsonrpc"
    "github.com/anyshy/go-web3/contract/builtin/ens"
)

var mainnetAddress = web3.HexToAddress("0x314159265dD8dbb310642f98f50C066173C1259b")

func main() {
	client, err := jsonrpc.NewClient("https://mainnet.infura.io")
    if err != nil {
        panic(err)
    }

	resolver := ens.NewENSResolver(mainnetAddress, client)
	addr, err := resolver.Resolve("ens_address")
	if err != nil {
		panic(err)
	}

    fmt.Println(addr)
}
```

## Tracker

Complete example of the tracker [here](./tracker/README.md)
