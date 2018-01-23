Release notes
=============

This document describes all the changes made to the *`ewp-rsa-aes128gcm`
Request Encryption* document, starting from its first released version.


0.4.0
-----

* Use a proper HTTP 415 error response code when unsupported `Content-Encoding`
  is encountered.

* Bugfix: Content-coding values are case-insensitive (per RFC), yet this
  specification required them to be case-sensitive. The specification was
  changed to match the RFC standard.


0.3.0
-----

* Indicated, that this encryption method can be used with other HTTP request
  types besides POST. (Previous version suggested that only POST request may
  contain a meaningful body.)
* Clarified what to do in case of some specific errors (e.g. receiving a GET
  request).
* Clarified that `Content-Type` must not be obfuscated.


0.2.0
-----

* Switched from AES CBC to AES GCM (see
  [here](https://github.com/erasmus-without-paper/ewp-specs-sec-rsa-aes128cbc/issues/1)).


0.1.0
-----

Initial release.
