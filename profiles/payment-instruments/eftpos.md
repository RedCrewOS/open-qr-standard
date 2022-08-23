
# Eftpos Payment Instrument Definition

A definition of a Payment Instrument that can be used in conjuction with the [Point-of-sale Payment Profile](../payment-profile.md) of the [Open QR Standard](../open-qr-standard.md) to communicate a source of funds for a merchant payment.

This payment instrument definition relies upon the [Point-of-sale Payment Profile](../payment-profile.md).

Support for this payment instrument is optional.

**DRAFT**: v0.0.4

**Authors**: Jack Moggach

**Sponsor Organisation**: [eftpos](https://www.eftposaustralia.com.au)

**Payment Instrument URN**: `urn:cds-au:qr:pos-instrument:eftpos:1`

## Payment Instrument Schema

The following schema defines the fields that convey the information about the payment instrument to the Code Consumer to allow for the instrument to be successfully used for payment.

### Code Provider

#### Discovery Document

This section defines properties to be included in the Code Provider discovery document under the following URN:

`urn:cds-au:qr:pos-merchant:1`

Within this section information about this payment instrument will be included under the following URN:

`urn:cds-au:qr:pos-instrument:eftpos:1`

The supported properties are:

| Property       | Required? | Description                                                                                                             |
| -------------- | --------- | ----------------------------------------------------------------------------------------------------------------------- |
| informationUrl | Y         | URL of the documentation which provides information on how to approve and finalize payments using this instrument type. |
| cardSupported  | Y         | Boolean flag indicating if the Code Provider supports payment via card details.                                         |
| tokenSupported | Y         | Boolean flag indicating if the Code Provider supports payment via an Eftpos card formtoken.                             |

#### Session Info Endpoint

The following schema defines the content to be returned by the Code Provider in a request to the session info endpoint. This information will be included in the **urn:cds-au:qr:pos-merchant:1** 'profile' property within the 'instrumentTypes' section.

This schema will be returned with an object with the field name of:

`urn:cds-au:qr:pos-instrument:eftpos:1`

Response Schema [json-schema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-instrument/eftpos/1/response/session/info",
   "type": "object",
   "additionalProperties": false,
   "required": [
      "3dsEnabled"
   ],
   "properties": {
      "3dsEnabled": {
         "type": "boolean",
         "description": "Is 3DS enabled for the given session? This value does not indicate whether or not a 3DS challenge will be triggered - only that it may be triggered."
      }
   }
}
```

#### Session Claim Endpoint

The following schema defines the content to be returned by the Code Provider in a request to the session claim endpoint. This information will be included in the **urn:cds-au:qr:pos-merchant:1** ‘profile’ property within the ‘instrumentTypes’ section.

This schema will be returned with an object with the field name of:

`urn:cds-au:qr:pos-instrument:eftpos:1`

The schema will be included in the response object with an object with the field name of:

`urn:cds-au:qr:pos-instrument:eftpos:1`

Code consumers will be expected to complete the actual eftpos payment out-of-band. Information on how to use this endpoint with a card or token is outside the scope of this document.

Request Schema [json-schema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-instrument/eftpos/1/request/session/claim",
   "type": "object",
   "additionalProperties": false,
   "required": [],
   "properties": {}
}

```

Response Schema [json-schema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-instrument/eftpos/1/response/session/claim",
   "type": "object",
   "additionalProperties": false,
   "required": [
      "authorizeUri",
      "finalizeUri"
   ],
   "properties": {
      "authorizeUri": {
         "type": "string",
         "description": "URL of the API endpoint the Code Consumer must use to authorize a payment."
      },
      "finalizeUri": {
         "type": "string",
         "description": "URL of the API endpoint the Code Consumer must use to finalize a payment."
      }
   }
}
```

#### Session Status Endpoint

The following schema defines the content to be returned by the Code Provider in a request to the session status endpoint. This information will be included in the **urn:cds-au:qr:pos-merchant:1** ‘profile’ property within the ‘instrumentTypes’ section.

This schema will be returned with an object with the field name of:

`urn:cds-au:qr:pos-instrument:eftpos:1`

Response Schema [json-schema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-instrument/eftpos/1/response/session/status",
   "type": "object",
   "additionalProperties": false,
   "required": [
      "paymentStatus"
   ],
   "properties": {
      "paymentStatus": {
         "type": "string",
         "enum": [
            "created",
            "authorize_pending",
            "authorize_approved",
            "authorize_declined",
            "processing",
            "declined",
            "approved"
         ],
         "description": "The status of the payment request as it transitions through 'authorize' and 'finalize' stages."
      }
   }
}

```

### Code Consumer

The Code Consumer does not need to convey any information to the Code Provider to support discovery.
