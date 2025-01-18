# $CHRCH-lottery


$CHRCH Lottery Contract (Anchor)
Framework:

Use Anchor for the Solana program.
Epoch Trigger:

A keeper or off-chain script will call the contract after every 3 epochs to perform the lottery draw.
Similarly, the off-chain script can also be used to periodically check for unclaimed prizes, etc.
Chainlink RNG:

Integrate with Chainlink VRF on Solana.
The caller of the RNG function pays in SOL for each randomness request.
Burn Mechanism:

“Burning” tokens means sending them to a designated burn address for $CHRCH.
Owner 2% Withdrawal:

This function can be called by anyone, but it will release the 2% to the owner. The function caller pays for the transaction fees.
Ticket Data Reset:

Keep the old ticket data until the draw is executed and the winners are determined. Then reset for the next cycle.
Prize Claim Window:

1 year is approximated as 100 epochs.
If a prize is unclaimed after 100 epochs from the draw date, it is burned.
Error/Feedback Mechanism:

Provide custom error messages (e.g., “InvalidTokenError”, “WrongMultipleError”, etc.) for transaction failures.
All Other Requirements:

Only accept $CHRCH (SPL token address Gk8neJFkvW1Cvm8ZrmpVDhnfBRkFQ5Tny1PRwDcqNz6H).
Accept multiples of 100 $CHRCH only, or reject with an error.
If fewer than 3 tickets are sold in a cycle, cancel and burn entire pot.
Distribution after each successful draw:
90%: Prize pool distribution (60/25/15).
8%: Burn.
2%: Accumulate for owner.
Winners have 100 epochs to claim their prizes, otherwise the prizes are burned.
The contract is immutable (no upgrades, no pausing). Ownership is non-transferable.
Users can query their current ticket counts and the current prize pool balance on-chain.
No on-chain historical data or notifications for unclaimed prizes.
Sample Anchor Smart Contract (Rust)
Note:

This sample code is illustrative. You will need to customize it to integrate with the actual Chainlink VRF on Solana (e.g., specifying the VRF client accounts, callback methods, etc.).
Adjust PDAs (Program-Derived Addresses), seeds, and other details to fit your specific deployment.
Ensure the $CHRCH mint authority is set up in such a way that the program can burn tokens by sending them to the designated burn address.


------------------->>>>>>>>>>>>>>>>>>>>>>  Check the smart contract.


Implementation Notes
Chainlink VRF:

You will need to replace the request_draw / fulfill_randomness logic with the actual Chainlink VRF client workflow for Solana. Typically, this involves:
Funding a VRF account subscription.
Calling the Chainlink VRF program with your subscription ID.
Getting the random result via a callback (the fulfill_randomness instruction in this example).
Epoch Handling:

We keep lottery_state.current_epoch and have an instruction advance_epoch for demonstration. Your off-chain keeper can call this, or you can integrate with a more robust approach (e.g., reading epoch info from a Sysvar or another system account).
Burning:

If $CHRCH tokens can be burned only via the mint authority using the Burn instruction, you must ensure the program is authorized to do so. If you prefer sending tokens to a “burn address,” the code above does a simple transfer.
Make sure the “burn address” is either a known black hole account or consistent with $CHRCH’s token design.
Immutability:

Anchor programs can be made immutable by closing upgrade authority on the deployed program. Once done, the code can’t be upgraded.
Owner Non-Transferable:

The LotteryState includes an owner: Pubkey. You can simply never allow any instruction to change this pubkey.
Logs and Events:

Anchor’s #[event] macros produce Solana logs that clients can listen to. This covers the “feedback mechanism” for major events. For direct error messages, custom errors are used.
No Historical Storage:

The state is only for the current lottery cycle. Past winners, past draws, etc. are not stored once reset.
Off-Chain Notification:

The contract does not contain any on-chain notifications for unclaimed prizes. Any alerts or reminders must be handled off-chain.

