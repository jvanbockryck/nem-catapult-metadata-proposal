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

## Example of using metadata for an oracle mosaic (very drafty)
Putting an oracle - being a data source outside of a blockchain - on the NEM Catapult is another strong use case. To enforce the strenght of that data source, it should come from trusted "identified" parties.

For this purpose, an oracle mosaic can be created via multisig and each multisig account should be of a an "identified" type - see higher-  thus having an alias with a DID.
Furthermore, this DID should be able to be looked-up - so should be public - AND should contain a public Verifiable Claim (containing data that clearly should prove the identity of the oracle party).

For the metadata requirements, I will stick to the oracle mosaic and not go into detail about the Verifiable Claims of the multisig account holders.

In this, with respect, I am strongly inspired by the work of the Komodo platform on this matter (see https://github.com/KomodoPlatform/Mastering_CryptoConditions/blob/master/Chapter%2011%20-%20oracles%20example)
Nevertheless, to put it in the NEM Catapult context, I suggest the following.

### Add additional metadata on an account to make it an oracle-account
Let's assume 3 oracle parties need to sign to do any kind of oracle operation e.g. create/register (an new oracle), data-event (adds new data events), subscribe (listening to a data-event stream), etc.

Each oracle party should have an oracle-account. This is an account with a DID as alias (see higher) but with extra metadata properties added:

    Oracle metadata property 1, "nem:isOracleAccount": Should be "true" (off course)
    Oracle metadata property 2, "nem:oracleClaim": Should contain a reference to a Verifiable Claim detailing the identity of the oracle party
    
So these oracle parties are the responsible/accountable entities behind the delivery of oracle data.
    
### Add metadata to an oracle mosaic
For this, I would like to take an example from a real-world asset that is simple to understand: electricity (e.g. from a renewable source). This could further be specified by the namespace of the entity that delivers the electricity, e.g. "localWind".
So this example local electricity company produces electricity from wind and want to issue the mosaic "localWind.electricity" but based on real data from the electricity generator, so that's the oracle in this case.

The "localWind.electricity" mosaic then can bear additional metadata to make it usefull for oracle purposes. The metadata namespace prefix we set to "lwe" and then we can create a number of metadata on this metadata-namespace (off course, this metadata can be generalised for any kind of sensor-based metadata):

    Metadata property 1, "lwe:sensorDID": Identifier of the sensor that created the data. (It would be good if this sensor would also have a Verifiable Claim, but we leave this out of the example. If the sensor is fixed, and this is usually the case for electricity, then the location can just be part of the claim.)
    Metadata property 2, "lwe:sensorDataEvent": The measured data (can be a data structure on its own or can reference a data file)
    Metadata property 3, "lwe:sensorEventTime": The dateTime the sensor recorded the data event

### Oracle metadata to trigger release of other mosaics
The prime purpose for having pretty detailed metadata on an oracle mosaic is to use the values of that metadata to trigger other transactions. The granular the metadata, the better that works.

In our example, we thus better make "lwe:sensorDataEvent" more granular and replace it with a specific metadata property - "lwe:producedElectricity" - that we can be use to create a devisable mosaic that represent a value, in this case, the produced electricity. This must be an number.

LocalWind will issue a mosaic for this purpose with the name (alias) "localWind.e-token". This can trigger a complex NEM Catapult transaction that devides this mosaic to multiple stakeholders (existing function of NEM Catapult).

To bind the oracle mosaic "localWind.electricity" with the issueing of the "localWind.e-token" mosaic we can use metadata that should be added to the "localWind.e-token" mosaic at creation of that mosaic type:
    
    Metadata property 3, "nem:oracleSource": Reference to the NEM Catapult oracle mosaic that can trigger the issuance of another mosaic.
    Metadata property 4, "nem:oracleInput: Reference to the metadata that should be used as input for the issuance of another mosaic.
    Metadata property 5, "nem:oracleDevider": Formula to devide the input to issue a number of mosaics
    
Applied to our "localWind" example, the metadata values for the "localWind.e-token" would be
* nem:oracleSource=localWind.electricity
* nem:oracleInput=lwe:producedElectricity
* nem:oracleDevider=100 (or anything that makes sense)













