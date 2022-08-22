
# <Instrument Name> Payment Instrument Definition

A definition of a Payment Instrument that can be used in conjuction with the [Point-of-sale Payment Profile](../payment-profile.md) of the [Open QR Standard](../open-qr-standard.md) to communicate a source of funds for a merchant payment.

This payment instrument definition relies upon the [Point-of-sale Payment Profile](../payment-profile.md).

Support for this payment instrument is optional.

**DRAFT**: v1.0.0

**Authors**:	<author1>, <author2>

**Sponsor Organistaion**:	<organisation>

**Payment Instrument URN**: `urn:cds-au:qr:pos-instrument:<name>:<version>`

## Payment Instrument Schema

The following schema defines the fields that convey the information about the payment instrument to the merchant to allow for the instrument to be successfully used for payment.

<add any other clarifying information here>

Payment instrument schema [JSONSchema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-instrument/<name>/<version>",
   "type": "object",
   "additionalProperties": true,
   "required": [
      <specify required fields>
   ],
   "properties": {
      <specify properties>
   }
}
```

## Payment Instrument Overview

<Provide any overview information related to the Payment Instrument>

## Additional references

<Links to additional information such as T&Cs, specifications, default contracts, etc>
