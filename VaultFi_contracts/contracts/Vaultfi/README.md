# VaultMod

VaultMod is central to the VaultFi admin user(s). It also serves as a proxy to minting new vault instances that relay liquidity commissions back to this contract.

&nbsp;

## Upgrade Strategy

When a new version of the vault contract is available, admin user(s) will call `SetVaultCodeId` on `vaultmod_contract_address`. All instances of the vault contract created subsequently use the latest `vault_code_id`.

This makes the protocol more resilient to forks as vault owners can choose to maintain their old vault instances, transfer their assets from their old vaults, or manage both the new and old vaults simultaneously.

