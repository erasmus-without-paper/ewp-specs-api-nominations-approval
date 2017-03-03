OBSOLETE - Nominations Approval API
===================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

**OBSOLETE: This proposal has been merged with [Outgoing Mobilities API]
[mobilities-api].**

This document describes the **Nominations Approval API**. This API can be
implemented by a sending HEI if it wants to **allow** receiving HEIs to approve
and/or reject nominations via an API (as opposed to do doing so by phone or by
email).

Such approvals are NOT REQUIRED to be saved immediately. This means that the
results of this action don't have to be immediately reflected in mobility
`status` values in the Outgoing Mobilities API response. Some sending HEIs MAY
simply choose to notify their IRO members instead, so that the request gets
reviewed by a human first.


Request method
--------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.


Request parameters
------------------

Parameters MUST be provided in the regular `application/x-www-form-urlencoded`
format.


### `sending_hei_id` (required)

An identifier of the institution which is the sending partner of the mobilities
being approved. This parameter MUST be required by the server even if it covers
only a single institution.


### `receiving_hei_id` (required)

An identifier of the institution which is the receiving partner of the
mobilities being approved. The requester MUST cover this HEI.


### `mobility_id` (required, repeatable)

Identifiers of the mobilities being approved or rejected. The sending and
receiving partners of all referenced mobilities MUST match the HEIs referenced
in the `sending_hei_id` and `receiving_hei_id` parameters.

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server MUST process all of them. There is no limit on the number of
identifiers provided.

All referenced mobilities should **usually** refer to a single "batch" of
nominations (i.e. all mobilities with the `nomination` status related to a
single IIA). "Usually" means, that client implementers, in their UIs, SHOULD
*encourage* their IRO staff members to do so, but they SHOULD NOT *force* them
to do so. A human MAY choose do things differently (for example, to approve or
reject some nominations one by one).


### `status` (required)

This parameter MUST contain either `approved` or `rejected` string.

If nominations are being rejected, then it is RECOMMENDED to supply a reason
for rejection in the `comment` parameter.


### `comment` (optional)

An optional comment for the decision (supplied by the IRO staff member who
performs the action).


Permissions
-----------

All requests from the EWP Network MUST be allowed access to this API. Consult
the [Echo API][echo] specs for details on handling unprivileged requests.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * If the requester does not cover the HEI referenced in `receiving_hei_id`,
   then the server MUST respond with HTTP 400 error response.

 * If the sending and/or receiving partner of some of the referenced mobilities
   don't match the `sending_hei_id` and `receiving_hei_id` parameters, then the
   server MUST respond with the HTTP 400 error response.

 * If server implementers decide that provided `mobility_id` values do not
   constitute a "proper batch", and as such they cannot be processed, then the
   server MUST respond with the HTTP 400 error response. Additionally, error
   response MUST contain a proper `<user-message>` element (in addition to the
   regular `<developer-message>`). This additional message SHUOLD inform the
   partner IRO member how he should proceed:

   * It may explain **why** the given set of mobilities is not considered to be
     "proper" by the server.

   * It may contain a request that the IRO member should contact the sending
     HEI by email instead.


Response
--------

Servers MUST respond with a valid XML document described by the [response.xsd]
(response.xsd) schema. See the schema annotations for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[mobilities-api]: https://github.com/erasmus-without-paper/ewp-specs-api-mobilities
