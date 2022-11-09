1.

Grammer issues in comments in:

1.1

Contract: DiamondProxy.sol

1.1.1

	line 07

	"Contract" is misspelled as "Cotract"
	
Modified Comment:

	/// @title Diamond Proxy Contract (EIP-2535)

1.2

Contract: Storage.sol

1.2.1

	line 14

	the "the" before "trusted" is unnecessary

Modified Comment:

	/// @param securityCouncilMembers The set of trusted addresses that can instantly finish upgrade (diamond cut)

1.2.2

	lines 33, and 34
	
	"was" should be "were"

Modified Comments:

	/// @param key The 32 bytes of information that were sent in the log

	/// @param value The 32 bytes of information that were sent in the log

1.2.3

	line 36

	"addresses" is misspelled as "addreses"

Modified Comment:

	/// @dev The sender is an `address` type, although we are using `uint256` for addresses in `L2CanonicalTransaction`.

1.3

Contract: DiamondCut.sol

1.3.1

	line 84

	the "the" before "future" is unnecessary

Modified Comment:

	// Limited-time freezing feature will be added in future upgrades, so keeping this variable for simplification

1.4

Contract: Getters.sol

1.4.1

	line 83

	"non executed" should be hyphenated as "non-executed"

Modified Comment:

	/// @notice For unfinalized (non-executed) blocks may change

1.5

Contract: Mailbox.sol

1.5.1

	line 65

	the "on" before "the Merkle" is unnecessary

Modified Comment:

	// any shorter/longer paths attack the Merkle path validation

1.5.2

	line 173

	"offchain" should be hyphenated as "off-chain"

Modified Comment:

	// Data that needed for operator to simulate priority queue off-chain

1.6

Contract: Diamond.sol

1.6.1

	line 36

	"proxy associated" should be hyphenated as "proxy-associated"

Modified Comment:

	/// @notice The structure that holds all diamond proxy-associated parameters

1.6.2

	line 63

	"delegate" is misspelled as "dellegate"

Modified Comment:
	
	/// @param initAddress The address that's delegate called after setting up new facet changes

1.6.3

	line 199

	"by" before "another" should be "with"
	
Modified Comment:

	/// - `_selector` is NOT associated with another facet

1.6.4

	line 255

	"with" is misspelled as "wih"

Modified Comment:

	/// NOTE: It is expected but NOT enforced that there are no selectors associated with `_facet`

1.6.5

	line 275
	
	the "to" before "the initialization" is unnecessary

Modified Comment:

	/// @dev Delegates call the initialization address with provided calldata

1.7

Contract: IMailbox.sol

1.7.1

	line 46

	it is unnecessary to have "use-case" hyphenated and should be two words "use case"

Modified Comment:

	// Reserved dynamic type for the future use case. Using it should be avoided,

1.8

Contract: L1ERC20Bridge.sol

1.8.1

	line 19

	"reference" is misspelled as "refference"

Modified Comment:

	/// @dev It is standard implementation of ERC20 Bridge that can be used as a reference

1.8.2

	line 76

	"exact" is misspelled as "exect"

	"initialize" is misspelled as "initiailize"

Modified Comment:

	// We are expecting to see the exact two bytecodes that are needed to initialize the bridge

1.8.3

	line 196

	"rollup" should be hyphenated as "roll-up"

Modified Comment:

	// - l2ShardId = 0 (means that L1 -> L2 transaction was processed in a roll-up shard, other shards are not available yet anyway)

1.8.4
	
	line 197

	"transaction" should be "transactions"

Modified Comment:

	// - txNumberInBlock = number of transactions in the block

1.9

Contract: L1EthBridge.sol

1.9.1

	line 16

	"contracts" should be "contract"

	the "one" before "way" is unnecessary

Modified Comment:

	/// @notice This smart contract is the only way to get native ether inside the L2.

1.9.2

	line 155

	"rollup" should be hyphenated as "roll-up"

Modified Comment:

	// - l2ShardId = 0 (means that L1 -> L2 transaction was processed in a roll-up shard, other shards are not available yet anyway)

1.9.3

	line 156

	"transaction" should be "transactions"

Modified Comment:

	// - txNumberInBlock = number of transactions in the block

1.9.4

	line 242

	the "a" before "only" is unnecessary

Modified Comment:

	/// @dev Ignore function input and always return zero address as only one token that the bridge process

1.10

Contract: AllowList.sol

1.10.1

	line 19

	"as" should be "is"

Modified Comment:

	/// @notice The address that the owner proposed is one that will replace its

1.10.2

	line 86

	"are" should be "is"

Modified Comment:

	/// @param _targets The array of smart contract addresses, of which access to the call is to be changed

1.11

Contract: ReentrancyGuard.sol

1.11.1

	line 30

	"value" should be "values"

Modified Comment:

	// The values being non-zero values makes deployment a bit more expensive,

1.11.2

	line 32

	"to" should be "at"

Modified Comment:

	// amount. Since refunds are capped at a percentage of the total

1.11.3

	line 62

	"make" should be "making"

Modified Comment:

	* by making the `nonReentrant` function external, and making it call a

1.12

Contract: L2ERC20Bridge.sol

1.12.1

	line 45

	"initiate" should be "initiates"

Modified Comment:

	/// @param _l1Sender The account address that initiates the deposit on L1

1.13

Contract: L2ETHBridge.sol

1.13.1

	line 37

	"initiate" should be "initiates"

Modified Comment:

	/// @param _l1Sender The account address that initiates the deposit on L1

1.14

Contract: L2StandardERC20.sol

1.14.1

	line 28

	"constant" should be "constants"

Modified Comment:

	/// @notice OpenZeppelin token represents `name` and `symbol` as storage variables and `decimals` as constants.