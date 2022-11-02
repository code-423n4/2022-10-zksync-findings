
## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

Instances:
-AllowList.sol lines 69, 103
-Executor.sol lines 111, 162, 180, 210, 240
-Getters.sol line 163
-Mailbox.sol line 223
-Diamond.sol line 94, 132, 153, 173, 23