
# Core QR Profile

A profile of the [Open QR Standard](../open-qr-standard.md) that defines the base end points and discovery fields that allow for Code Providers and Code Consumers to communicate and discover the capabilities and characteristics supported by both parties.

This profile MUST be supported by both Code Providers and Code Consumers.

**DRAFT**: v1.0.0

**Authors**:	James Bligh, Kareem Al-Bassam, Jack Moggach

**Code Provider Profile URN**: `urn:cds-au:qr:core-provider:1`

**Code Consumer Profile URN**: `urn:cds-au:qr:core-consumer:1`

## Code Provider Discovery Document

This section defines properties to be included in the Code Provider discovery document under the *Code Provider Profile URN*

The supported discovery properties are:

| Property | Optionality | Description |
|----------|-------------|-------------|
|sessionInfoUri|REQUIRED|URL of the Session Info endpoint hosted by the Code Provider.|
|sessionClaimUri|REQUIRED|URL of the Session Claim endpoint hosted by the Code Provider.|
|sessionStatusUri|REQUIRED|URL of the Session Status endpoint hosted by the Code Provider.|
|jwksUri|REQUIRED|URL of the JWKS endpoint for the Code Provider.  MUST include appropriate keys to support the algorithms supported.|
|signingAlgorithms|REQUIRED|JSON array containing a list of supported JWS [RFC7515] signing algorithms.  Values to be taken from the JWA standard [RFC7518].  Only asymmetric algorithms may be supported.|
|encryptionAlgorithms|REQUIRED|JSON array containing a list of supported JWE [RFC7516] encryption algorithms.  Values to be taken from the JWA standard [RFC7518].  Only asymmetric algorithms may be supported.|
|displayName|REQUIRED|The name of the Code Provider to use in display to an end user.|
|description|OPTIONAL|A short sentence or paragraph describing the Code Provider to use in display to an end user.|
|logoUri|OPTIONAL|URL to a logo for the Code Provider.  If present, MUST link to a PNG or JPEG image with maximum width of 250 pixels and maximum height of 150 pixels.|

## Code Provider Endpoints

### Session Info Endpoint

An API used to obtain information about the session associated with a nominated QR ID.

#### HTTP Method

GET

#### Query Parameters

| Parameter | Optionality | Description |
|-----------|-------------|-------------|
|qr-id|REQUIRED|The QR ID that information is requested for.|

#### Error Responses

HTTP Status: 401, Validation of the provided authorisation JWT failed

HTTP Status: 422, The provided QR ID is not known

#### Successful Response

HTTP Status: 200

Content-Type: application/json

Successful Response Schema [JSONSchema]:

```
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/core-provider/1/response/session/info",
   "type": "object",
   "additionalProperties": false,
   "required": [
      "data",
      "meta"
   ],
   "properties": {
      "data": {
         "type": "object",
         "additionalProperties": true,
         "required": [
            "qrId",
            "provider",
            "profiles"
         ],
         "properties": {
            "qrId": {
               "type": "string",
               "description": "The qr-id that the information was requested for"
            },
            "provider": {
               "type": "string",
               "description": "The unique identifier of the code provider.  This is defined as the base location where the discovery document can be found, i.e. ‘https://<provider base>’"
            },
            "displayName": {
               "type": "string",
               "description": "An optional, short, display name that can be presented to the customer by the consumer.  May be the name of the provider or the name of the organisation leveraging the provider platform (e.g. the name of a merchant)"
            },
            "displayDescription": {
               "type": "string",
               "description": "An optional paragraph of text that can be displayed to the customer that describes the context of the use of the QR"
            },
            "informationUri": {
               "type": "string",
               "format": "uri",
               "description": "Link to further information related to the QR code"
            },
            "expiryTime": {
               "type": "string",
               "format": "date-time",
               "description": "The expiry time of the QR code.  After this time the qr-id cannot be used to claim a session "
            },
            "profiles": {
               "type": "object",
               "description": "An object with details for each of the qr profiles supported by this qr-id.  Each property of this object uses the urn of the profile and contains properties defined by the qr profile that the urn denotes",
               "additionalProperties": false,
               "patternProperties": {
                  "^urn:.*$": {
                     "type": "object",
                     "additionalProperties": true
                  }
               }
            }
         }
      },
      "meta": {
         "type": "object",
         "description": "Used to hold any additional meta data that the provider wishes to provide",
         "additionalProperties": true
      }
   }
}
```

### Session Claim Endpoint

An API used to claim a session under a specific QR Profile using a nominated QR ID.

#### HTTP Method

POST

#### Query Parameters

| Parameter | Optionality | Description |
|-----------|-------------|-------------|
|qr-id|REQUIRED|The QR ID that a session is to be claimed for.|

#### Request

Request Schema [JSONSchema]:

```
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/core-provider/1/request/session/claim",
   "type": "object",
   "additionalProperties": false,
   "required": [
      "data",
      "meta"
   ],
   "properties": {
      "data": {
         "type": "object",
         "additionalProperties": true,
         "required": [
            "consumer",
            "profiles"
         ],
         "properties": {
            "consumer": {
               "type": "string",
               "description": "The unique identifier of the QR consumer.  This is defined as the base location where the discovery document can be found, i.e. ‘https://<consumer base>’"
            },
            "displayName": {
               "type": "string",
               "description": "An optional, short, display name of the consumer that can be presented to the customer by the provider"
            },
            "displayDescription": {
               "type": "string",
               "description": "An optional paragraph of text that can be displayed to the customer that describes the purpose of the consumer"
            },
            "profiles": {
               "type": "object",
               "description": "An object with details for each of the qr profiles supported by this qr-id.  Each property of this object uses the urn of the profile and contains properties defined by the qr profile that the urn denotes",
               "additionalProperties": false,
               "patternProperties": {
                  "^urn:.*$": {
                     "type": "object",
                     "additionalProperties": true
                  }
               }
            }
         }
      },
      "meta": {
         "type": "object",
         "description": "Used to hold any additional meta data that the provider wishes to provide",
         "additionalProperties": true
      }
   }
}
```

#### Error Responses

HTTP Status: 400, The specified QR Profile is not supported for this QR ID or the data provided was not valid

HTTP Status: 401, Validation of the provided authorisation JWT failed

HTTP Status: 422, The provided QR ID is not known

#### Successful Response

HTTP Status: 200

Content-Type: application/json

Successful Response Schema [JSONSchema]:

```
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/core-provider/1/response/session/claim",
   "type": "object",
   "additionalProperties": false,
   "required": [
      "data",
      "meta"
   ],
   "properties": {
      "data": {
         "type": "object",
         "additionalProperties": true,
         "required": [
            "sessionId",
            “provider”,
            "profiles"
         ],
         "properties": {
            "sessionId": {
               "type": "string",
               "description": "The session-id created by claiming the session.  This ID represents this claim event and must be unique.  Can be used for subsequent calls for status"
            },
            "provider": {
               "type": "string",
               "description": "The unique identifier of the code provider.  This is defined as the base location where the discovery document can be found, i.e. ‘https://<provider base>’"
            },
            "profiles": {
               "type": "object",
               "description": "An object with details for each of the qr profiles supported by this qr-id.  Each property of this object uses the urn of the profile and contains properties defined by the qr profile that the urn denotes",
               "additionalProperties": false,
               "patternProperties": {
                  "^urn:.*$": {
                     "type": "object",
                     "additionalProperties": true
                  }
               }
            }
         }
      },
      "meta": {
         "type": "object",
         "description": "Used to hold any additional meta data that the provider wishes to provide",
     	"additionalProperties": true
      }
   }
}
```

### Session Status Endpoint

An API used to obtain information about a specific session that was previously claimed.

#### HTTP Method

GET

#### Query Parameters

| Parameter | Optionality | Description |
|-----------|-------------|-------------|
|session-id|REQUIRED|The Session ID that information is requested for that was previously obtained from a call to the Session Claim endpoint.|

#### Error Responses

HTTP Status: 401, Validation of the provided authorisation JWT failed

HTTP Status: 422, The provided QR ID is not known

#### Successful Response

HTTP Status: 200

Content-Type: application/json

Successful Response Schema [JSONSchema]:

```
{
   "$schema": "http://json-schema.org/draft-07/schema",
   "$id": "https://cds-au/qr/core-provider/1/response/session/status",
   "type": "object",
   "additionalProperties": false,
   "required": [
      "data",
      "meta"
   ],
   "properties": {
      "data": {
         "type": "object",
         "additionalProperties": true,
         "required": [
            "sessionId",
            “provider”,
            "profiles"
         ],
         "properties": {
            "sessionId": {
               "type": "string",
               "description": "The session-id that the status has been requested for"
            },
            "provider": {
               "type": "string",
               "description": "The unique identifier of the code provider.  This is defined as the base location where the discovery document can be found, i.e. ‘https://<provider base>’"
            },
            "profiles": {
               "type": "object",
               "description": "An object with details for each of the qr profiles supported by this qr-id.  Each property of this object uses the urn of the profile and contains properties defined by the qr profile that the urn denotes",
               "additionalProperties": false,
               "patternProperties": {
                  "^urn:.*$": {
                     "type": "object",
                     "additionalProperties": true
                  }
               }
            }
         }
      },
      "meta": {
         "type": "object",
         "description": "Used to hold any additional meta data that the provider wishes to provide",
     	"additionalProperties": true
      }
   }
}
```

### JWKS Endpoint

A static JSON resource used to obtain the keys required for authentication and encryption.  Defined according to [RFC7517].

The JWKS endpoint for the Code Provider MUST include sufficient keys to accommodate the algorithms supported by the provider.





## Normative References

| Label | Reference |
|-------|-----------|
|[RFC2119]|Bradner, S, "Key words for use in RFCs to Indicate Requirement Levels", [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119), March 1997.|
|[ISO18004]|ISO/IEC JTC 1/SC 31, "ISO/IEC 18004:2015 Information technology - Automatic identification and data capture techniques - QR Code bar code symbology specification", [ISO/IEC 18004:2015](https://www.iso.org/standard/62021.html), February 2015.|
|[RFC8529]|Bray, T., Ed., "The JavaScript Object Notation (JSON) Data Interchange Format", [RFC 8259](https://datatracker.ietf.org/doc/html/rfc8259), December 2017.|
|[RFC7519]|Jones, M., Bradley, J., and N. Sakimura, “JSON Web Token (JWT)” [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519), May 2015.|
|[RFC7515]|Jones, M., Bradley, J., and N. Sakimura, “JSON Web Signature (JWS),” [RFC 7515](https://datatracker.ietf.org/doc/html/rfc7515), May 2015.|
|[RFC7516]|Jones, M., Hildebrand, J., “JSON Web Encryption (JWE),” [RFC 7516](https://datatracker.ietf.org/doc/html/rfc7516), May 2015.|
|[RFC7517]|Jones, M., “JSON Web Key (JWK),” [RFC 7517](https://datatracker.ietf.org/doc/html/rfc7517), May 2015.|
|[RFC7518]|Jones, M., “JSON Web Algorithms (JWA),” [RFC 7518](https://datatracker.ietf.org/doc/html/rfc7518), May 2015.|

## Informative References

| Label | Reference |
|-------|-----------|
|[JSONSchema]|Wright, A., Andrews, H., Hutton, B., and G. Dennis, "JSON Schema: A Media Type for Describing JSON Documents", draft-bhutton-json-schema-00 (work in progress), December 2020.|
