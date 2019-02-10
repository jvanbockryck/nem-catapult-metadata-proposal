# nem-catapult-metadata-requirements-proposal
## Metadata namespace
The NEM concept of "namespace" should be enhanced with a prefix that can be used to register metadata properties.

As example, for identity purposes, the following namespace and prefix can be used:
* namespace: "defined-id"
* metadata-prefix: "d-id"

## Metadata properties within a metadata-namespace
There should be a NEM Catapult operation to define metadata properties within a metadata-namespace. 
Property names - the "keys" of a key/value pair - include the metadata-prefix.

For identity purposes, these two metadata-properties are useful:
> * "d-id:documentHash"
>  * "d-id:status"

## NEM metadata-namespace
A NEM metadata-namespace should be added for canonical metadata. The metadata-namespace prefix should be "nem".

## Conditions on a metadata property
There should be an operation to add (simple) conditions on a metadata property. 
These conditions should be on the NEM metadata-namespace as their purpose is to trigger business logic developed on the NEM Catapult blockchain.

This is a list of potential  NEM conditions:

> * **"nem:isRequired"**: A transaction will be triggered only of a metadata value is provided 
> * **"nem:isUnique"**: A tranasction will be triggered if a metadata value is unique accross all transactions containing the same metadata. This also means that if a new transaction contains the same metadata value, that new transaction will be blocked.
> * **"nem:mayBeEmpty"**: A transaction will be triggered if a metadata value is empty
> * **"nem:enumeration"**: A transaction will be triggered if a metadata value is one - and only one - of the enumeration values
    
The defaul value for the boolean types is "false" and no business logic will be executed.
The account holder that puts these conditions on a metadata property has the entention of using the business logic, so will set their value to "true".

## NEM Catapult account alias as metadata
The documentations on the upcoming COW NEM Catapult contains the concept of "account alias" (https://github.com/nemtech/nem2-docs/blob/cow/source/concepts/namespace.rst).

Such account alias can also be encoded as NEM metadata:
> **"nem:accountAlias"**

The account alias could be used for binding a DID with a NEM account.
Example: 
* Account SCVG35-ZSPMYP-L2POZQ-JGSVEG-RYOJ3V-BNIU3U-N2E6 
* "nem:accountAlias" ="did:defined:1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2" (a valid DID).

Metadata conditions can be applied to this "nem:accountAlias" to add a constraint, e.g. to make it unique, via the "nem:isUnique" condition. Using such condition, a value of a "nem:accountAlias" can only be used once, which is exactly the behaviour that  is required for an identity property.

## Example of using conditions on identity metadata
The "d-id:documentHash" metadata property should have the following conditions:
* nem:isRequired=true
* nem:isUnique=true
* nem:mayBeEmpty=true

The "d-id:status" metadata property should have these conditions:
* nem:isRequired=true
* nem:enumeration=["created", "updated", "revoked"]

With these two metadata properties "d-id:documentHash" and "d-id:status" used on an instance of an "identity-doc" mosaic, we can support all DID operations (as described in https://w3c-ccg.github.io/did-spec/#did-operations), not on the actual DID Documents, but at least on the document hashes on the NEM Catapult blockchain.

## Summary of NEM Catapult operations for identity
The following 4 steps show all transaction steps for identity purpose:
![identity steps](images/step1.png)

### Note on "update" and "revoked (or delete)" operation for identity management
Any blockchain struggles with the concept of mutability as it is contrary to the inherent immutability of a blockchain.
Off course a transaction remains immutable, but by adding some metadata and conditions, the concept of an "update" and "revocation (or delete)" can be implemented to a certain degree (needs to be in concert how an application reacts to that, unfortunately).

### Note on "verify" operation for identity managemen
A DID verify operation can look at all transactions with mosaic "identity-doc" coming from an account containing a DID as alias and then can look at the "d-id:status" metadata. 
The last transaction will contain the status that the verifier should use.

## Conclusion on using metadata for identity purposes
Using the proposed metadata and conditions, we can modify the NEM Catapult blockchain so that becomes more suitable for identity purposes:
1. By attaching an alias to an account containing a DID and resticting this to be unique and
2. By using this account for transactions of mosaic "identity-doc" with metadata "d-id:documentHash" and "d-id:status" and respective conditions.

## Metadata for an oracle account
Putting an **oracle - a data source outside of a blockchain** - on the NEM Catapult is another strong use case in which metadata can play a role. To enforce the strenght of an oracle data source, it should be issued by a trusted "identified" party (can be more parties, using co-signing).

Therefore such oracle account should contain an account alias with a DID (see higher).
This DID should be public and be the owner of a public Verifiable Claim (containing data that proves the identity of the oracle).

To indicate an account is an oracle account, we suggest adding this metadata to it:
 > **"nem:isOracleAccount"**
## Oracle example: LocalWind
### Context of the example
To demonstrate an oracle use case, we use the fictitious company "LocalWind".
LocalWind is a renewable electricity producer and wants to setup a NEM Catapult oracle as a mosaic and create a transaction every x time new data is available.
Then the creation of each instance of this electricity mosaic should trigger the creation of a number of electricity tokens that represent shareholder value.

LocalWind now has two mosaics (within the "localwind" namespace):
* localwind.electricity: This will be the **oracle**
* localwind.e-token: This will be the **(security) token** released based on the issuing of oracle data

### An oracle account for localwind.electricity
First thing that LocalWind needs to do is to create an oracle account with an account alias containing a LocalWind identity (DID).
![oracle account creation](images/step2.png)
    
### Identity documents for the LocalWind company
The oracle account holders of LocalWind are the responsible/accountable entities behind the delivery of oracle data.
To proof their identity, a public DID Document and Verifiable Claim should be registered on a NEM Catapult.

![identity registration for the oracle account](images/step3.png)
    
### Metadata for the oracle and e-token mosaics
The "localwind.electricity" mosaic should bear additional metadata to make it usefull for oracle purposes. We propose the following metadata property in the NEM namespace for this purpose:
> **"nem:isOracleMosaic"**

The "localwind.electricity" mosaic should also contain metadata about the electricity generator and the data it produces at a certain time.

LocalWind should then register their metadata-prefix which they name "lwe". 
These are the "lwe" metadata properties:
>* **"lwe:generatorDID"**: Identifier of the electricty generator that created the data.
> * **"lwe:producedElectricity"**: The produced electricity 
>* **"lwe:generatorEventTime"**: The dateTime the electricity generator recorded the produced electricity.

These are the steps Localwind  should take to define their metadata:
![localwind metadata](images/step5.png)

On the other hand, the ""localwind.e-token" mosaic" should have metadata to define **how e-tokens will be created** by the "localwind.electricity" oracle.

For that purpose, we suggest the following NEM Catapult metadata:
> * **"nem:oracleSource"**: Reference to the NEM Catapult oracle mosaic that triggers the issuance of another mosaic.
> * **"nem:oracleInput**: Reference to the metadata that is used as input for the issuance of another mosaic.
>* **"nem:oracleDevider"**: Formula that devides the input to issue mosaics

### Identity documents for the LocalWind energy generator (device)
The generator DID is a public DID of a device that is owned Localwind. Localwind could re-use their oracle account to register the DID Document and Verifiable Claim to proof for this electricity generator device.

![identity registration for the electricity generator device](images/step4.png)

There is no personal data involved in these device and business identities, so there is no harm on publishing all this metadata publicly, on the contrary, it allows fully transparent discovery.

### Generic, desciptive, standard metadata
More metadata can be added to further describe both the "localwind.electricity" oracle mosaic and the "localwind.e-token" mosaic, so interested parties can understand what this token is about.
For that kind of purpose, standardised **Dublin Core** metadata should be used that is widely accepted.
That Dublin Core metadata uses the prefix "dc". More info about Dublin Core can be find here: http://www.dublincore.org/documents/dcmi-terms/

### LocalWind mosaic creation
Now with all metadata and identities in place, Localwind can create their mosaics:
![localwind mosaics creation](images/step6.png)

### Transactions with the Localwind mosaics
The transaction that creates an instance of a "localwind.electricity" oracle mosaic only needs to contain metadata values that are different for each transaction, being the "lwe:producedEnergy" and "lwe:generatorEventTime. 

To indicate which "localwind.electricity" transaction caused the creation of which "localwind.e-token" transaction, the following metadata property is proposed:
> **"nem:causingTransaction"**

![localwind transactions](images/step7-2.png)

## General remark on NEM Catapult metadata
**To make all this metadata usefull for discovery, it should be queryable.**
