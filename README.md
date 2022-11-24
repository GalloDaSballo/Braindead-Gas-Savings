# Braindead-Gas-Savings
List of Gas Savings that you should apply without thinking too much, with math

## ++i for Loops instead of i++

Saves 5 gas

## unchecked ++i / i++

Saves 80 gas on average


## `immutable` If you have a constructor and you set a variable once

Saves 2100 per first SLOAD, 100 for each subsequent SLOAD


## Cache Storage Variables if you ever read them more than once

The first read will cost an extra 6 gas (MSTORE + MLOAD)
Saves 97 gas (3 is MLOAD) for each subsequent read


# Nuanced
## Use XYZ = XYZ + A instead of +=

This only works in certain cases, either low optimizations or outside an unchecked block, also dependent on solidity version, always test it

## Use Calldata instead of Memory
Generally will save gas as Memory is copied from the calldata into memory, vs calldata being read directly.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.13;

import "../../lib/test.sol";
import "../../lib/Console.sol";

contract Exploit is DSTest {
    GasSample testC;

    function testExploit(uint256 nonce) public {
        testC = new GasSample();
        
        uint256[] memory temp = new uint256[](3);
        temp[0] = 12;
        temp[1] = 32;
        temp[2] = 54;
        testC.getData(temp);
    }
}

contract GasSample {

   
    function getData(uint256[] memory values) external returns (uint256) {
        uint256 length = values.length;
        uint256 acc;

        for(uint i; i < length; ) {
            acc += values[i];
            unchecked { ++i; }
        }

        return acc;
    }
}
```

### With Calldata
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ getData                                   ┆ 999             ┆ 999 ┆ 999    ┆ 999 ┆ 1       │

### With Memory
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ getData                                   ┆ 1364            ┆ 1364 ┆ 1364   ┆ 1364 ┆ 1    


# Mythbusting

## Require(x != 0) no longer works if solidity >= 0.8.12
https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94

## Immutable and Constant have different costs for "expressions" - BUSTED
https://twitter.com/GalloDaSballo/status/1543729080926871557
They cost the same at runtime

## >= is cheaper than > - BUSTED
https://twitter.com/GalloDaSballo/status/1543729467465629696
`>` is cheaper (less opcodes)
