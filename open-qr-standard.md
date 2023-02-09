# Open QR Standard

**An open standard for the publication of interoperable QR codes for payments and other use cases**

**DRAFT**: v1.0.0

**Authors**:	James Bligh, Kareem Al-Bassam, Jack Moggach

## Abstract

This document defines a standard for the publication and use of QR Codes between different parties to securely accomplish multi-party journeys such as point of sale payments, location check-in, offer redemption or provision of digital identity.

## Introduction

The use of QR Codes to facilitate a range of user journeys is now ubiquitous and has good understanding and acceptance amongst consumers.  Support for the launching of applications using QR Codes is also available on all widely adopted mobile platforms.

QR Codes used via a mobile device usually have tight coupling between the system publishing the QR Code and the system consuming the contents of the QR Code.  The ability for a QR Code to be scanned, interpreted and used by an arbitrary client without a pre-existing relationship with the publisher is less common.

For some use cases, such as point of sale payments, this limits value and results in proprietary silos.

This standard defines a framework for the publisher and consumer of a QR Code - for an arbitrary use case - to recognise each other, communicate securely without prior registration and provide mutual discovery of capability.  The framework does not require a central registry or trust mechanism for providers and consumers to interact successfully.

The discovery mechanisms are generic and therefore allow the framework to be extended to additional use cases over time.  Discovery also facilitates the management of updates and new versions over time as known use cases functionally evolve.


## Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119].

| Term | Definition |
|------|------------|
|QR Code|A “quick response code” created according to [ISO18004].|
|Code Provider|The system that represents the initiating side of the user journey.  This is the system that creates and publishes the QR Code.  For a point-of-sale payment this would be the merchant checkout system.|
|Code Consumer|The system that represents the acceptance, or client, side of the user journey.  This is the system that scans and processes the QR Code.  For a point-of-sale payment this would be the customer’s mobile wallet application.|
|QR Profile|A profile for defining the format of the various actions and meta data that can be transferred using this standard for a specific type of use case or user journey.|
|Offer|The information about the profiles and capabilities associated with a specific QR Code.|
|Session|An established connection between a QR Provider and a QR Consumer.|

## Generic Sequence Diagram

An example sequence of activity supported by this standard is as follows ([PNG version is here](./resources/sequence.png)):

```
+-----------+                                   +-----------+                                   +---------+ +-----------+
| Provider  |                                   | Consumer  |                                   | Display | | Authority |
+-----------+                                   +-----------+                                   +---------+ +-----------+
      |                                               |                                              |            |
      | Journey Initiated                             |                                              |            |
      |------------------                             |                                              |            |
      |                 |                             |                                              |            |
      |<-----------------                             |                                              |            |
      |                                               |                                              |            |
      | QR Code Issued                                |                                              |            |
      |--------------------------------------------------------------------------------------------->|            |
      |                                               |                                              |            |
      | (optional: Transitory QR Codes) New QR Code Issued                                           |            |
      |--------------------------------------------------------------------------------------------->|            |
      |                                               | -------------------------------------\       |            |
      |                                               |-| Consumer scans QR Code with device |       |            |
      |                                               | |------------------------------------|       |            |
      |                                               |                                              |            |
      |                                               | QR Code Scanned                              |            |
      |                                               |--------------------------------------------->|            |
      |                                               |                                              |            |
      |                                               |      Base path and ID extracted from QR code |            |
      |                                               |<---------------------------------------------|            |
      |                                               |                                              |            |
      |          Provider capabilities from discovery |                                              |            |
      |<----------------------------------------------|                                              |            |
      |                                               |                                              |            |
      | Capabilities provided                         |                                              |            |
      |---------------------------------------------->|                                              |            |
      |                                               |                                              |            |
      |                                               | Ensure capabilities match                    |            |
      |                                               |--------------------------                    |            |
      |                                               |                         |                    |            |
      |                                               |<-------------------------                    |            |
      |                                               |                                              |            |
      |                                               | Extract security configuration               |            |
      |                                               |-------------------------------               |            |
      |                                               |                              |               |            |
      |                                               |<------------------------------               |            |
      |                                               |                                              |            |
      |                      Request details of Offer |                                              |            |
      |<----------------------------------------------|                                              |            |
      |                                               |                                              |            |
      | Consumer capabilities from discovery          |                                              |            |
      |---------------------------------------------->|                                              |            |
      |                                               |                                              |            |
      |                         Capabilities provided |                                              |            |
      |<----------------------------------------------|                                              |            |
      |                                               |                                              |            |
      | Validate credentials based                    |                                              |            |
      |---------------------------                    |                                              |            |
      |                          |                    |                                              |            |
      |<--------------------------                    |                                              |            |
      |                                               |                                              |            |
      | (optional: Use of Central Authority) Validate the Consumer certification                     |            |
      |---------------------------------------------------------------------------------------------------------->|
      |                                               |                                              |            |
      | Provide details of Offer                      |                                              |            |
      |---------------------------------------------->|                                              |            |
      |                                               |                                              |            |
      |                                               | Validate Offer type is supported             |            |
      |                                               |---------------------------------             |            |
      |                                               |                                |             |            |
      |                                               |<--------------------------------             |            |
      |                                               |                                              |            |
      |           "Claim" Session and provide details |                                              |            |
      |<----------------------------------------------|                                              |            |
      |                                               |                                              |            |
      | Callback to indicate journey completion       |                                              |            |
      |---------------------------------------------->|                                              |            |
      |                                               |                                              |            |
```

## QR Code

The Code Provider will publish a QR Code that MUST conform with the requirements of [ISO18004].

This QR Code MUST contain a URL in the following format:

`https://<provider base>/<QR ID>`

The Code Provider MUST provide a valid web page at the location specified by `<provider base>` with instructions for a user how the QR Code can be successfully used.

The `<QR ID>` is used to obtain access to a data entity held by the Code Provider referred to in this standard as an `Offer`.  QR IDs MAY be single or multiple use depending on the use case being supported.

The Code Provider SHOULD superimpose a logo on the QR code to help Customers recognise trusted brands.  This logo SHOULD be tested to ensure the logo does not introduce too much error to the QR Code and the QR Code remains readable. In situations where the effect of scanning a QR code is unclear the Code Provider SHOULD provide guidance as to how the QR Code can be successfully used.

## Offers

An Offer is the information associated with a specific QR Code that represnts the profiles that the QR Code allows access to.  A Code Consumer obtains the Offer information for a QR Code and then determines how to proceed based on the information the Offer contains and what there service supports.

Offers MAY be single use or MAY be multi-use.

## Sessions

After obtaining the Offer information a Code Consumer may claim that Offer.  This will result in a Session being established between the Code Provider and the Code Consumer with the Code Provider being the owner of the Session.

Sessions MUST be single use.

## Offer and Session Endpoints

The Code Provider MUST support an Offer Info endpoint that the Code Consumer can call to obtain information about the Offer using the QR ID obtained from the QR Code.  This endpoint SHOULD be idempotent and should not have side effects on the underlying Offer.

The Code Provider MUST support an Offer Claim endpoint that the Code Consumer can use to claim a Offer for use using a QR ID and thus establishing a Session.  This endpoint MUST provide a unique Session ID that can be used for subsequent interactions.

The Code Provider MUST support a Session Status endpoint to identify the status of a Session using a Session ID.

The Code Provider MUST support the ability to perform a callback to the Code Consumer when the status of the Session changes.

The Code Consumer MAY provide a callback as metadata when a session is claimed.

The detailed requirements for these endpoints are defined in the [Core QR Profile definition](./profiles/core-profile.md).

## Code Provider Capability Discovery

The Code Provider MUST make a static JSON [RFC8259] file available at:

`https://<provider base>/.discovery.json`

where `<provider base>` is the same path that appears in the QR Code.

Note that `<provider base>` MUST be communicated to the Code Consumer as the client identifier when calling any endpoint hosted by the Code Consumer in the “Client-ID” http header.

This discovery file will contain details to be used for security, communicating supported capability and providing locations of profile specific endpoints.

This discovery file MUST contain a single root object containing a “services” property.  The “services” property will contain series of properties each named according to the following URN format:

`urn:cds-au:qr:<profile>:<version>`

Note that using cds-au has the advantage that it gives a globally unique prefix that aligns with the model already in use nationally (ie. the CDR regime).  The taxonomy for this namespace is managed by the [Data Standards Body](https://consumerdatastandards.gov.au/).

This discovery file MAY contain an “authority” property in the root object.  The “authority“ property will be an array of strings with each element containing the URL of an authority file that the Code Provider is registered with using the optional central authority mechanism.

Common configuration applicable to all profiles are described in the ‘core-provider’ profile defined in the [Core QR Profile definition](./profiles/core-profile.md).

```
Non-normative example of a code provider discovery file:
{
   “authority”: [
      “https://www.authorityprovider.com”
   ],
   “services”: {
      “urn:cds-au:qr:core-provider:1”: {
         ...
      },
      “urn:cds-au:qr:pos-merchant:1”: {
         ...
      }
   }
}
```

## Code Consumer Capability Discovery

The Code Consumer MUST make a static JSON [RFC8259] file available at:

`https://<consumer base>/.discovery.json`

where `<consumer base>` is a path that MUST be communicated to the Code Provider as the client identifier when calling any endpoint hosted by the Code Provider in the “Client-ID” http header.

This discovery file will contain details to be used for security, communicating supported capability and providing locations of profile specific endpoints.

This discovery file MUST be formatted in the same way as the Code Provider discovery file.

Common configuration applicable to all profiles are described in the ‘core-consumer’ profile in the [Core QR Profile definition](./profiles/core-profile.md).

An entity that is operating as both a Code Provider and a Code Consumer MAY use the same discovery file for both roles by including both the ‘core-provider’ profile and the ‘core-consumer’ profile.

## Central Authority

To allow for increased trust a central authority may optionally be used by the participating Code Providers and Code Consumers.  A variety of trusted organisations may offer a central authority service to ecosystem participants.

A central authority would implement registration processes and policies to ensure trust at their own discretion.

To act as a central authority and advertise the entities that they have registered they MUST make a static JSON [RFC8259] file available at:

`https://<authority base>/.authority.json`

This authority file MUST contain a single root object containing a “providers” property and a “consumers” property.

The “providers” property and “consumers” property will both have the same structure.  Both contain a series of properties each named with the base URL (including protocol) for the registered entities.  The contents of these entity specific properties is not specified and may be extended by the central authority to provide additional information regarding their registration process.

```
Non-normative example of a central authority file:
{
   “providers”: {
      “https://www.codeprovider1.com”: {
         ...
      },
      “https://www.codeprovider2.com”: {
         ...
      }
   “consumers”: {
      “https://www.codeconsumer1.com”: {
         ...
      },
      “https://www. codeconsumer1.com”: {
         ...
      }
   }
}
```

## Additional References

This standard is augmented by definitions of profiles.  These profiles define a specific set of capabilities that may be supported by a QR Code published in alignment with this standard.

There is a core profile that is MUST be supported by both Code Providers and Code Consumers.  This core profile defines the end points that are critical for the operation of the standard and allow for extension by capability profiles.

Currently defined profiles:

* **[Core QR Profile definition](./profiles/core-profile.md)**<br/>Core profile that defines the base end points and discovery fields that allow for Code Providers and Code Consumers to communicate and discover the capabilities and characteristics supported by both parties.

* **[Payment Profile](./profiles/payment-profile.md)**<br/>A profile that allows for a merchant to offer a payment that needs to be made that can then be claimed by a wallet to provide the payment method for the payment.  This profile is designed to support both online checkout and merchant point of sale scenarios.

## Normative References

| Label | Reference |
|-------|-----------|
|[RFC2119]|Bradner, S, "Key words for use in RFCs to Indicate Requirement Levels", [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119), March 1997.|
|[ISO18004]|ISO/IEC JTC 1/SC 31, "ISO/IEC 18004:2015 Information technology - Automatic identification and data capture techniques - QR Code bar code symbology specification", [ISO/IEC 18004:2015](https://www.iso.org/standard/62021.html), February 2015.|
|[RFC8259]|Bray, T., Ed., "The JavaScript Object Notation (JSON) Data Interchange Format", [RFC 8259](https://datatracker.ietf.org/doc/html/rfc8259), December 2017.|

## Informative References

None
