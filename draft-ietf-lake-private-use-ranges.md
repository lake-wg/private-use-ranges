---
v: 3

title: Additional Private Use Ranges in the IANA Registries of the Lightweight Authenticated Key Exchange (LAKE) Protocol
abbrev: Additional Private Use Ranges for LAKE
docname: draft-ietf-lake-private-use-ranges-latest
cat: info
submissiontype: IETF

ipr: trust200902
area: Security
wg: LAKE Working Group
kw: Internet-Draft

coding: utf-8

author:
      -
        ins: M. Tiloca
        name: Marco Tiloca
        org: RISE AB
        street: Isafjordsgatan 22
        city: Kista
        code: SE-164 40
        country: Sweden
        email: marco.tiloca@ri.se
      -
        ins: T. Müller
        name: Tobias Müller
        org: Robert Bosch GmbH
        street: Markwiesenstraße 58
        city: Reutlingen
        code: 72770
        country: Germany
        email: Tobias.Mueller9@de.bosch.com

normative:
  RFC8126:
  RFC9528:
  External.Authorization.Data:
    author:
      org: IANA
    date: false
    title: EDHOC External Authorization Data
    target: https://www.iana.org/assignments/edhoc/edhoc.xhtml#edhoc-ead
  Method.Types:
    author:
      org: IANA
    date: false
    title: EDHOC Method Types
    target: https://www.iana.org/assignments/edhoc#edhoc-method-types
  Error.Codes:
    author:
      org: IANA
    date: false
    title: EDHOC Error Codes
    target: https://www.iana.org/assignments/edhoc/edhoc.xhtml#edhoc-error-codes

informative:
  RFC8949:
  RFC9668:
  Auth.Cred.Types:
    author:
      org: IANA
    date: false
    title: EDHOC Authentication Credential Types
    target: https://www.iana.org/assignments/edhoc#edhoc-authentication-credential-types

entity:
  SELF: "[RFC-XXXX]"

--- abstract

This document adds Private Use ranges to IANA registries that pertain to the Lightweight Authenticated Key Exchange (LAKE) protocol.

--- middle

# Introduction # {#intro}

The Lightweight Authenticated Key Exchange (LAKE) protocol {{RFC9528}} provides points of extensibility, for which related IANA registries exist.

When they were created, some of those registries did not include ranges with registration procedure "Private Use" (see {{Section 4.1 of RFC8126}}). Later on, it was brought to attention that having such ranges available would be convenient for private deployments.

Private Use ranges enable controlled proprietary ecosystems to use LAKE extensibility points consistently without making their application-specific protocol semantics public. They also support the development and testing of additional LAKE-based procedures before a decision is made to specify them publicly. Furthermore, Private Use ranges separate locally chosen values from IANA-assigned values, preserving the other ranges for publicly specified uses. Deployments using Private Use values are responsible for preventing collisions within their intended scope; independent deployments might use the same value differently.

This document rectifies the original situation: As specified in {{iana}}, it adds Private Use ranges to three IANA registries of the LAKE protocol that originally did not include such ranges.

## Terminology ## {#terminology}

This document uses the acronym LAKE, expanded to Lightweight Authenticated Key Exchange, to denote the protocol specified as EDHOC in {{RFC9528}}. Identifiers defined literally in {{RFC9528}} or in IANA registries (e.g., the names of the EDHOC registries themselves) are unchanged.

# Use of Values from Private Use Ranges

## Range Boundaries and Encoding

The Private Use ranges defined in {{iana}} extend beyond the values that are admitted by the existing ranges with other registration policies in the same IANA registries.

The values admitted by the new Private Use ranges are bounded by the values that can be encoded as CBOR integers with a four-byte argument (see {{Section 3.1 of RFC8949}}). For an unsigned integer N, a four-byte argument makes it possible to represent values of N up to 4294967295. For a negative integer N, a four-byte argument encodes the absolute value of N minus 1 and thus makes it possible to represent values of N down to -4294967296.

Together with the initial byte of the CBOR integer, these encodings result in a total of five bytes. These bounds preserve the existing ranges for registry values that result in shorter encodings, while limiting the newly defined Private Use ranges to values with five-byte encodings. In contrast, the "EDHOC Authentication Credential Types" registry {{Auth.Cred.Types}} defined in {{RFC9668}} includes open-ended Private Use ranges. With respect to the Private Use ranges defined in this document, no values are designated whose CBOR encoding requires an eight-byte argument.

When using values from these Private Use ranges, implementations need to account for the additional message size compared with the case where values with shorter encodings are used. Method types and error codes in the new ranges are each encoded in five bytes. For EAD items, positive labels in the new range are each encoded in five bytes as well. When using a critical EAD item (see {{Section 3.8 of RFC9528}}), the negative ead_label in the EAD item is encoded in five bytes, except for -65536 that is encoded in three bytes (i.e., its two-byte argument is 65535).

The four-byte argument does not imply that the decoded integer fits in a signed 32-bit type. In particular, neither 4294967295 nor -4294967296 is representable in such a type. Implementations supporting the newly defined Private Use ranges in full also need to support a representation that preserves all the values admitted by those ranges, such as a signed 64-bit integer or a separate sign and unsigned argument. Range checks and, for EAD labels, absolute-value calculations also need to preserve the full values of decoded integers.

## EAD Labels

As specified in {{Section 3.8 of RFC9528}}, the "EDHOC External Authorization Data" registry {{External.Authorization.Data}} records the absolute values of the integer values used as ead_label in EAD items. Consistent with that, the Private Use range added to this registry pertains to both positive and negative ead_label values used in EAD items on the wire, with the absolute values listed in that range. The sign of ead_label retains its existing meaning: A negative value indicates that the EAD item is critical, and a nonnegative value indicates that the EAD item is non-critical.

For example, the ead_label values 65536 and -65536 identify the same privately-defined EAD item as non-critical and critical, respectively. The processing rules for critical and non-critical EAD items in {{Section 3.8 of RFC9528}} continue to apply.

# Security Considerations

This document and the Private Use ranges defined in {{iana}} do not impact the security of the LAKE protocol. When using values from such Private Use ranges, the security considerations compiled in {{Section 9 of RFC9528}} still apply.

# IANA Considerations {#iana}

This document has the following actions for IANA.

Note to RFC Editor: Please replace all occurrences of "{{&SELF}}" with the RFC number of this specification and delete this paragraph.

## EDHOC Method Types Registry

IANA is asked to add the following two ranges to the "EDHOC Method Types" registry {{Method.Types}} within the "Ephemeral Diffie-Hellman Over COSE (EDHOC)" registry group:

* -4294967296 to -65537
* 65536 to 4294967295

For both ranges, the registration procedure is "Private Use" per {{Section 4.1 of RFC8126}}.

Consistent with the above, the following two entries are added to the registry, thereby registering the indicated values as reserved for private use:

* Value: -4294967296 to -65537
* Initiator Authentication Key: N/A
* Responder Authentication Key: N/A
* Reference: {{&SELF}}

<br>

* Value: 65536 to 4294967295
* Initiator Authentication Key: N/A
* Responder Authentication Key: N/A
* Reference: {{&SELF}}

## EDHOC Error Codes Registry

IANA is asked to add the following two ranges to the "EDHOC Error Codes" registry {{Error.Codes}} within the "Ephemeral Diffie-Hellman Over COSE (EDHOC)" registry group.

* -4294967296 to -65537
* 65536 to 4294967295

For both ranges, the registration procedure is "Private Use" per {{Section 4.1 of RFC8126}}.

Consistent with the above, the following two entries are added to the registry, thereby registering the indicated values as reserved for private use:

* ERR_CODE: -4294967296 to -65537
* ERR_INFO Type: N/A
* Description: Reserved for Private Use
* Change Controller: IETF
* Reference: {{&SELF}}

<br>

* ERR_CODE: 65536 to 4294967295
* ERR_INFO Type: N/A
* Description: Reserved for Private Use
* Change Controller: IETF
* Reference: {{&SELF}}

## EDHOC External Authorization Data Registry

IANA is asked to add the following range to the "EDHOC External Authorization Data" registry {{External.Authorization.Data}} within the "Ephemeral Diffie-Hellman Over COSE (EDHOC)" registry group:

* 65536 to 4294967295

For this range, the registration procedure is "Private Use" per {{Section 4.1 of RFC8126}}.

Consistent with the above, the following entry is added to the registry, thereby registering the indicated values as reserved for private use:

* Name: N/A
* Label: 65536 to 4294967295
* Description: Reserved for Private Use
* Reference: {{&SELF}}

--- back

# Acknowledgments # {#acknowledgments}
{:numbered="false"}

The authors thank {{{Göran Selander}}} and {{{Mališa Vučinić}}} for their early feedback during the preparation of this document.
