
# Point-of-sale Payment Profile

A profile of the [Open QR Standard](../open-qr-standard.md) that allows for a merchant to offer a payment that needs to be made that can then be claimed by a wallet to provide the payment method for the payment.  This profile is designed to support both online checkout and merchant point of sale scenarios.

Note that this profile should read as an addendum to the [Open QR Standard](../open-qr-standard.md) and inherits it's references and definitions.

This profile relies upon, and extends the [Core QR Profile definition](./core-profile.md).

This profile is optional.

**DRAFT**: v1.0.0

**Authors**:	James Bligh, Kareem Al-Bassam, Jack Moggach

**Code Provider Profile URN**: `urn:cds-au:qr:pos-merchant:1`

**Code Consumer Profile URN**: `urn:cds-au:qr:pos-wallet:1`

## Payment Instruments

Note that a key feature of this profile is the concept of a *Payment Instrument*.  A Payment Instrument is constituted by a URN defining the type of the instrument and a set of data describing the instrument that matches a schema defined by the instrument originator.

The intention is that payment processors and schemes will define a structure for a Payment Instrument that communicates to the provider the information needed to be able to obtain and use that instrument to execute a payment via a back channel process.  The actual back channel process is outside the scope of this standard.

For instance, a token representing a Credit Card could be presented via this profile.  This token could then be exchanged, via a secure back channel defined by the instrument provider, for the actual Credit Card details.

## Code Provider Discovery Document

This section defines properties to be included in the Code Provider discovery document under the *Code Provider Profile URN*.

The supported discovery properties are:

| Property | Optionality | Description |
|----------|-------------|-------------|
|multiUse|OPTIONAL|Boolean flag indicating if QR codes are able to support multiple payment sessions (such as in the case of permanent, or semi-premanent, QR codes).  If absent a value of ‘false’ is assumed.|
|loyaltyPrograms|OPTIONAL|Array of strings indicating the loyalty programs supported for payments.  The values in the array are string indicators nominated by the loyalty program organisation.|
|instrumentTypes|OPTIONAL|Map indicating the types of payment instruments supported by the code provider.  The keys in the map are string indicators nominated by the organisation that manages the instrument type. Keys follow a URN format and support versions. Metadata about the instrument type may be included in the mapped value object.|

## Code Provider Endpoints

### Session Info Endpoint

The following schema defines the content to be returned by the Code Provider in a request to the session info endpoint under the ‘profiles’ property.  This schema will be returned with an object with the field name of the *Code Provider Profile URN*.

Profile specific response schema [JSONSchema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-merchant/1/response/session/info",
   "type": "object",
   "additionalProperties": true,
   "required": [
      "merchantId",
      "instrumentTypes"
   ],
   "properties": {
      "multiUse": {
         "type": "boolean",
         "description": "Flag indicating that the QR is able to be reused so multiple claims will succeed but provide different session IDs. Assumed to be false if not present"
      },
      "merchantId": {
         "type": "string",
         "description": "Identifier of the merchant for the payment"
      },
      "merchantName": {
         "type": "string",
         "description": "Display name of the merchant for the payment"
      },
      "grossAmount": {
         "type": "string",
         "description": "Optional gross amount for the payment. If this value is not present then the final amount for the payment is not yet known"
      },
      "currency": {
         "type": "string",
         "description": "A code representing the currency of the gross amount [ISO4217]. If not set AUD is assumed."
      },
      "merchantReference": {
         "type": "string",
         "description": "Optional reference identifier which represents the QR session in a merchant's internal systems."
      },
      "synchronous": {
         "type": "boolean",
         "description": "Flag indicating that a payment with this QR code will be synchronous and a final status will be provided when the session is claimed. Assumed to be false if absent"
      },
      "instrumentTypes": {
         "type": "object",
         "description": "Map of instrument types supported for the payment",
         "additionalProperties": true
      }
   }
}
```

### Session Claim Endpoint

The following schemas define the content to be included in the request and response payloads for the session claim endpoint under the ‘profiles’ property.

The following schema will be included in the request object with an object with the field name of the *Code Consumer Profile URN*.

Profile specific request schema [JSONSchema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-wallet/1/request/session/claim",
   "type": "object",
   "additionalProperties": false,
   "required": [
     "customerId",
     "instrument"
   ],
   "properties": {
      "customerId": {
         "type": "string",
         "description": "Identifier of the customer for the payment"
      },
      "instrument": {
         "type": "object",
         "description": "Object containing the details of the payment instrument. The contents of the object are dependent on the value of the type property. Fields required to be present will vary based on the instrument type",
         "additionalProperties": true,
         "required": [
            "type"
         ],
         "properties": {
            "type": {
               "type": "string",
               "description": "The type of the instrument to be used for the payment. Additional fields such as tokens or identifiers will be included as defined for the specific instrument type. The instrument type must be one of the types indicated as being supported in the provider discovery document and in the response to a call to session info"
            }
         }
      }
   }
}
```

The following schema will be included in the response object with an object with the field name of the *Code Provider Profile URN*.

Profile specific response schema [JSONSchema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-merchant/1/response/session/claim",
   "type": "object",
   "additionalProperties": true,
   "required": [
     "merchantId",
     "status"
   ],
   "properties": {
      "merchantId": {
         "type": "string",
         "description": "Identifier of the merchant for the payment"
      },
      "merchantName": {
         "type": "string",
         "description": "Display name of the merchant for the payment"
      },
      "grossAmount": {
         "type": "string",
         "description": "Optional gross amount for the payment. If this value is not present then the final amount for the payment is not yet known"
      },
      "currency": {
         "type": "string",
         "description": "A code representing the currency [ISO4217] of the amounts in the response. If not set AUD is assumed."
      },
      "basket": {
         "description": "An array of items in the basket",
         "type": "array",
         "items": {
            "required": [
               "label",
               "totalPrice"
            ],
            "type": "object",
            "properties": {
               "label": {
                  "description": "Short label for the basket item",
                  "type": "string"
               },
               "description": {
                  "description": "Longer description of the basket item",
                  "type": "string"
               },
               "quantity": {
                  "description": "The number of units of the item in the basket if multiple is possible. Is a number to accommodate items charged by weight or length",
                  "type": "number"
               },
               "unitPrice": {
                  "description": "The unit price of the item excluding tax. May be positive or negative. A negative values indicates a discount or rebate",
                  "type": "number"
               },
               "unitTax": {
                  "description": "The unit tax of the item. May be positive or negative. A negative values indicates a discount or rebate",
                  "type": "number"
               },
               "unitMeasure":{
                  "description": "Optional display string for the measure of the unit",
                  "type": "string"
               },
               "totalPrice": {
                  "description": "The total price of the item excluding tax. May be positive or negative. A negative values indicates a discount or rebate",
                  "type": "number"
               },
               "totalTax": {
                  "description": "The total tax of the item. May be positive or negative. A negative values indicates a discount or rebate",
                  "type": "number"
               }
            }
         }
      },
      "status": {
         "description": "The current status of the session",
         "type": "string",
         "enum": [
            "CLAIMED",
            "PROCESSING",
            "APPROVED",
            "DECLINED",
            "CANCELLED",
            "EXPIRED"
         ]
      }
   }
}
```

### Session Status Endpoint

The following schema defines the content to be returned by the Code Provider in a request to the session status endpoint under the ‘profiles’ property.  This schema will be returned with an object with the field name of the *Code Provider Profile URN*.

Profile specific response schema [JSONSchema]:

```json
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/pos-merchant/1/response/session/status",
   "type": "object",
   "properties": {
      "additionalProperties": true,
      "required": [
         "merchantId",
         "status"
      ],
      "merchantId": {
         "type": "string",
         "description": "Identifier of the merchant for the payment"
      },
      "merchantName": {
         "type": "string",
         "description": "Display name of the merchant for the payment"
      },
      "grossAmount": {
         "type": "string",
         "description": "Optional gross amount for the payment. If this value is not present then the final amount for the payment is not yet known"
      },
      "currency": {
         "type": "string",
         "description": "A code representing the currency [ISO4217] of the amounts in the response. If not set AUD is assumed."
      },
      "basket": {
         "description": "An array of items in the basket",
         "type": "array",
         "items": {
            "required": [
               "label",
               "totalPrice"
            ],
            "type": "object",
            "properties": {
               "label": {
                  "description": "Short label for the basket item",
                  "type": "string"
               },
               "description": {
                   "description": "Longer description of the basket item",
                   "type": "string"
                },
                "quantity": {
                   "description": "The number of units of the item in the basket if multiple is possible. Is a number to accommodate items charged by weight or length",
                   "type": "number"
                },
                "unitPrice": {
                   "description": "The unit price of the item excluding tax. May be positive or negative. A negative values indicates a discount or rebate",
                   "type": "number"
                },
                "unitTax": {
                   "description": "The unit tax of the item. May be positive or negative. A negative values indicates a discount or rebate",
                   "type": "number"
                },
                "unitMeasure":{
                   "description": "Optional display string for the measure of the unit",
                   "type": "string"
                },
                "totalPrice": {
                   "description": "The total price of the item excluding tax. May be positive or negative. A negative values indicates a discount or rebate",
                   "type": "number"
                },
                "totalTax": {
                   "description": "The total tax of the item. May be positive or negative. A negative values indicates a discount or rebate",
                   "type": "number"
                }
            }
         }
      },
      "status": {
         "description": "The current status of the session",
         "type": "string",
         "enum": [
            "CLAIMED",
            "PROCESSING",
            "APPROVED",
            "DECLINED",
            "CANCELLED",
            "EXPIRED"
         ]
      }
   }
}
```

## Code Provider Callback

Code Providers which support the Point of Sale Payment MUST be able to callback to a nominated Code Consumer endpoint when information about a claimed Session changes.

Code Consumers can optionally include a ‘callbackUri’ within their discovery document. If this value is set the Code Provider MUST send callback requests. The ‘callbackUri’ can be found in the Code Consumer profile with the *Code Provider Profile URN*.

The callback request MUST use the POST method and adhere to the Session Status schema.

Any change to the Session information MUST trigger a callback request. This includes (but is not limited to):

* The Session transitioning to a new status
* The total amount of the Session changing
* The basket items being updated

The Code Provider MUST send a callback but is not required to consider the response given by the Code Consumer endpoint.

## Code Consumer Discovery Document

This section defines properties to be included in the Code Consumer discovery document under the *Code Provider Profile URN*.

The supported discovery properties are:

| Property | Optionality | Description |
|----------|-------------|-------------|

|loyaltyPrograms|OPTIONAL|Array of strings indicating the loyalty programs supported for payments.  The values in the array are string indicators nominated by the loyalty program organisation.|
|instrumentTypes|OPTIONAL|Map indicating the types of payment instruments supported by the code provider.  The keys in the map are string indicators nominated by the organisation that manages the instrument type. Keys follow a URN format and support versions. Metadata about the instrument type may be included in the mapped value object.|
|callbackUri|OPTIONAL|A URL where the wallet can receive status callbacks. If supplied the merchant should send a POST request to this URL using the ‘Session Status’ schema whenever information about a session is changed.|

## Code Consumer Endpoints

None specified

## Normative References

None specified

## Informative References

| Label | Reference |
|-------|-----------|
|[JSONSchema]|Wright, A., Andrews, H., Hutton, B., and G. Dennis, "JSON Schema: A Media Type for Describing JSON Documents", draft-bhutton-json-schema-00 (work in progress), December 2020.|
