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
    Condition type 4: "nem:enumeration": A transaction will be triggered if a metadata value is one - and only one - of the enumeration values
    
By default, for the boolean types, their value is "false" and no business logic will be executed.
The account holder that puts these conditions on a metadata property has the entention of using the business logic, so will has to set their value to "true".

## NEM Catapult 'accounty alias' as metadata
I see some overlap in recent COW NEM Catapult release with the concept of alias, as documented in 
https://github.com/nemtech/nem2-docs/blob/cow/source/concepts/namespace.rst

In fact, this could be encoded as NEM metadata like "nem:accountAlias".

Let's build an example to demonstrate my point:
The account being "SCVG35-ZSPMYP-L2POZQ-JGSVEG-RYOJ3V-BNIU3U-N2E6" and the "nem:accountAlias" on that account having the value "did:defined:1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2" (a valid DID).

If the "nem:isUnique" conditions would be set on this "nem:accountAlias", then the value "did:defined:1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2" can only be used once, which is exactly the behaviour that  is required for an identity property.

## Example of using metadata for an identity mosaic
In our "defined" metadata namespace - with "d-id" prefix -, used for the "defined.id" project, we want to create metadata with specific behavour on a mosaic with name (or alias) "identity-doc".

We define the "d-id:documentHash" metadata property and put the following conditions on it:
* nem:isRequired=true
* nem:isUnique=true
* nem:mayBeEmpty=true

This means that a transaction of an "identity-doc" mosaic must contain the "d-id:documentHash" metadata AND
the value may be empty AND if it contains a value that value must be unique within the "identity-doc" mosaic space.

We can define the "d-id:status" metadata property and put the following conditions on it:
* nem:isRequired=true
* nem:enumeration=["created", "updated", "revoked"]

This means that a transaction of a "identity-doc" mosaic must contain the "d-id:status" metadata AND the values of that metadata should be one of the enumeration. 

If we apply just these two metadata properties "d-id:documentHash" and "d-id:status" on an instance of an "identity-doc" mosaic, we can support all DID operations (as described in https://w3c-ccg.github.io/did-spec/#did-operations), not on the actual DID Documents, but at least on the document hashes on the NEM Catapult blockchain.

**Note on "update" and "revoked (or delete)" operation for identity management:**
Any blockchain struggles with the concept of mutability as it is contrary to the inherent immutability of a blockchain.
Off course a transaction remains immutable, but by adding some metadata and conditions, the concept of an "update" and "revocation (or delete)" can be implemented to a certain degree (needs to be in concert how an application reacts to that, unfortunately).

**Note on "verify" operation for identity management:**
A DID verify operation can look at all transactions with mosaic "identity-doc" coming from an account containing a DID as alias and then can look at the "d-id:status" metadata. 
The last transaction will contain the status that the verifier should use.

## Conclusion for identity usage
Using these metadata and conditions on metadata, we can modify the NEM Catapult blockchain so that is becomes suitable for identity purposes:
1. By attaching an alias to an account containing a DID and resticting this to be unique and
2. By using this account then for making transactions on a mosaic "identity-doc" with specific metadata for this purpose, being "d-id:documentHash" and "d-id:status" and respective conditions.

## Example of using metadata for an oracle mosaic

TODO
