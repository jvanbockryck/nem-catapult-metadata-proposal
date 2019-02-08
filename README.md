# nem-catapult-metadata-requirements-proposal
## Define metadata space
There should be a transaction to declare a metadata-namespace in which an account holder can define metadata properties, beying the "keys" of the key-value metadata pairs.
This probably makes sense only for accounts that own a mosaic so that the metadata is bound that such mosaic.

    Example of a metadata-namespace: "defined-id"

A prefix of such metadata-namespace can be used to put in front of a metadata key.

    Example of a prefix of the "defined-id" metadata-namespace: "d-id"

## Define metadata properties within a metadata-space
There should be a transaction to define metadata properties within a metadata-space.

    Example of a property with name being "hash" in the "defined-id": "d-id:hash".
    
## Add (simple) conditions on a metadata property
There should be a transaction to add (simple) conditions to a metadata property. 
These conditions should be from the NEM metadata-namespace (with same prefix, being "nem") as they will be triggering business logic developed on the NEM Catapult blockchain.

Here are a number of potential types of such conditions:

    Condition type 1: "nem:isRequired": A transaction will be triggered only of a metadata value is provided 
    Condition type 2: "nem:isUnique": A tranasction will be triggered if a metadata value is unique accross all transactions containing the same metadata. This also means that if a new transaction contains the same metadata value, that new transaction will be blocked.
    Condition type 3: "nem:mayBeEmpty": A transaction will be triggered if a metadata value is empty
    
By default, their value is "false" and no business logic will be executed.
The account holder that puts these conditions on a metadata property has the entention of using the business logic, so will has to set their value to "true".
