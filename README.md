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


# Mythbusting

## Require(x != 0) no longer works if solidity >= 0.8.12
https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94
