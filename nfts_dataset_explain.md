
The Moonstream NFTs dataset
===========================

To load the NFTs dataset from a SQLite file, run:
>>> ds = nfts.dataset.FromSQLite(<path to sqlite database>)

This dataset consists of the following dataframes:

Dataframe: nfts
Load using:
	nfts_df = ds.load_dataframe(<sqlite connection or path to sqlite db>, "nfts")

Describes the NFT contracts represented in this dataset, with a name and symbol if they were available at time of crawl.

Columns:
1. address: The Ethereum address of the NFT contract.
2. name: The name of the collection of NFTs that the contract represents.
3. symbol: The symbol of the collection of NFTs that the contract represents.

- - -

Dataframe: mints
Load using:
	mints_df = ds.load_dataframe(<sqlite connection or path to sqlite db>, "mints")

All token mint events crawled in this dataset.

Columns:
1. event_id: A unique event ID associated with the event.
2. transaction_hash: The hash of the transaction which triggered the event.
3. block_number: The transaction block in which the transaction was mined.
4. nft_address: The address of the NFT collection containing the minted token.
5. token_id: The ID of the token that was minted.
6. from_address: The "from" address for the transfer event. For a mint, this should be the 0 address: 0x0000000000000000000000000000000000000000.
7. to_address: The "to" address for the transfer event. This represents the owner of the freshly minted token.
8. transaction_value: The amount of WEI that were sent with the transaction in which the token was minted.
9. timestamp: The time at which the mint operation was mined into the blockchain (this is the timestamp for the mined block).

- - -

Dataframe: transfers
Load using:
	transfers_df = ds.load_dataframe(<sqlite connection or path to sqlite db>, "transfers")

All token transfer events crawled in this dataset.

Columns:
1. event_id: A unique event ID associated with the event.
2. transaction_hash: The hash of the transaction which triggered the event.
3. block_number: The transaction block in which the transaction was mined.
4. nft_address: The address of the NFT collection containing the transferred token.
5. token_id: The ID of the token that was transferred.
6. from_address: The "from" address for the transfer event. This is the address that owned the token at the *start* of the transfer.
7. to_address: The "to" address for the transfer event. This is the address that owned the token at the *end* of the transfer.
8. transaction_value: The amount of WEI that were sent with the transaction in which the token was transferred.
9. timestamp: The time at which the transfer operation was mined into the blockchain (this is the timestamp for the mined block).

- - -

Dataframe: current_owners
Load using:
	current_owners_df = ds.load_dataframe(<sqlite connection or path to sqlite db>, "current_owners")

This table is derived from the nfts, mints, and transfers tables. It represents the current owner of each token in the dataset.

Columns:
1. nft_address: The address of the NFT collection containing the token whose ownership we are denoting.
2. token_id: The ID of the token (inside the collection) whose ownership we are denoting.
3. owner: The address that owned the token at the time of construction of this dataset.

- - -

Dataframe: current_market_values
Load using:
	current_market_values_df = ds.load_dataframe(<sqlite connection or path to sqlite db>, "current_market_values")

This table is derived from the nfts, mints, and transfers tables. It represents the current market value (in WEI) of each token in the dataset.

Columns:
1. nft_address: The address of the NFT collection containing the token whose market value we are denoting.
2. token_id: The ID of the token (inside the collection) whose market value we are denoting.
3. market_value: The estimated market value of the token at the time of construction of this dataset.

For this dataset, we estimate the market value as the last non-zero transaction value for a transfer involving this token.
This estimate may be inaccurate for some transfers (e.g. multiple token transfers made by an escrow contract in a single transaction)
but ought to be reasonably accurate for a large majority of tokens.

- - -

Dataframe: transfer_statistics_by_address
Load using:
	transfer_statistics_by_address_df = ds.load_dataframe(<sqlite connection or path to sqlite db>, "transfer_statistics_by_address")

This table is derived from the nfts, mints, and transfers tables. For each address that participated in
at least one NFT transfer between April 1, 2021 and September 25, 2021, this table shows exactly how many NFTs that address transferred to
other addresses and how many NFT transfers that address was the recipient of.

Columns:
1. address: An Ethereum address that participated in at least one NFT transfer between April 1, 2021 and September 25, 2021.
2. transfers_out: The number of NFTs that the given address transferred to any other address between April 1, 2021 and September 25, 2021.
3. transfers_in: The number of NFTs that any other address transferred to given address between April 1, 2021 and September 25, 2021.

- - -
