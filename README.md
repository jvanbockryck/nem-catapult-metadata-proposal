# nem-catapult-metadata-proposal
## Define metadata space
There should be a transaction to declare your metadata-namespace to which you can add our metadata properties, the "keys" of the key-value metadata pairs.

    Example of a metadata-namespace: "defined-id"

A prefix of such metadata-namespace can be used to put in front of a metadata key.

    Example of a prefix of the "defined-id" metadata-namespace: "d-id"

## Define metadata properties within a metadata space
There should be a transaction to declare metadata properties within a metadata space.

    Example of a property with name being "hash" in the "defined-id": "d-id:hash".
    
## Add constraints on a metadata property
There should be a transaction to add a constraint to a metadata property. This would be predefined metadata that would be from the NEM metadata-namespace (with same prefix, being "nem").

    Example 1: "nem:isRequired"
    Example 2: "nem:isUnique"
    Example 3: "nem:mayBeEmpty"

These are predefined NEM metadata properties that should trigger some internal Catapult business logic. 
If these NEM metadata properties are set on user-defined metadata properties, they will trigger this business logic depending on the default settings that can be overwritten in a define-metadata transaction.
If the default value is "false" (recommended), no business logic will be executed. This means that, to activate the business logic, such value should be set to "true".

