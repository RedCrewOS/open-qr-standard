
# Open QR Standard

**An open standard for the publication of interoperable QR codes for payments and other use cases**

**DRAFT**: v0.1.0

**Authors**:	James Bligh, Kareem Al-Bassam, Jack Moggach

## Abstract

This document defines a standard for the publication and use of QR Codes between different parties to securely accomplish multi-party journeys such as point of sale payments, location check-in, offer redemption or provision of digital identity.

## Introduction

The use of QR Codes to facilitate a range of user journeys is now ubiquitous and has good understanding and acceptance amongst consumers.  Support for the launching of applications using QR Codes is also available on all widely adopted mobile platforms.

QR Codes used via a mobile device usually have tight coupling between the system publishing the QR Code and the system consuming the QR Code.  The ability for a QR Code to be scanned, interpreted and used by an arbitrary client without a pre-existing relationship with the publisher is less common.

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

## Generic Sequence Diagram

An example sequence of activity supported by this standard is as follows:

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
      | New QR Code Issued                            |                                              |            |
      |--------------------------------------------------------------------------------------------->|            |
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
      |                    Request details of session |                                              |            |
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
      | Validate the Consumer certification           |                                              |            |
      |---------------------------------------------------------------------------------------------------------->|
      |                                               |                                              |            |
      | Provide details of session                    |                                              |            |
      |---------------------------------------------->|                                              |            |
      |                                               |                                              |            |
      |                                               | Validate session type is supported           |            |
      |                                               |-----------------------------------           |            |
      |                                               |                                  |           |            |
      |                                               |<----------------------------------           |            |
      |                                               |                                              |            |
      |           "Claim" session and provide details |                                              |            |
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

The `<QR ID>` is used to obtain access to a data entity held by the Code Provider referred to in this standard as a session.  QR IDs MAY be single or multiple use depending on the use case being supported.

Sessions MUST be single use.

The Code Provider SHOULD superimpose a logo on the QR code to help Customers recognise trusted brands.  This logo SHOULD be tested to ensure the logo does not introduce too much error to the QR Code and the QR Code remains readable. In situations where the effect of scanning a QR code is unclear the Code Provider SHOULD provide guidance as to how the QR Code can be successfully used.
