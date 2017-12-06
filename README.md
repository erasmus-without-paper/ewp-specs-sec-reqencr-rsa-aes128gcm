`ewp-rsa-aes128cbc` Request Encryption
======================================

This document describes how to accomplish confidentiality of EWP HTTP requests
with the use of `ewp-rsa-aes128cbc` encryption.

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Introduction
------------

This method of request encryption can be used, when - for any reason - using
plain TLS is "not enough". It makes use of `ewp-rsa-aes128cbc` encryption, as
specified [here][encr-spec].

This method of request encryption works only for POST requests, because only
the body of the request is encrypted. Other request parameters, such as headers
and GET parameters remain unencrypted.


Implementing a server
---------------------

### Publish your key

In order to receive properly encrypted requests, first you need to publish your
server's public RSA key in your manifest file. This tells the clients that they
can encrypt their requests to your HTTP endpoints with this key.


### Verify encryption method used

Make sure that the list of encodings in the client's `Content-Encoding` request
header contains the `ewp-rsa-aes128cbc` key (case sensitive).

You are REQUIRED to support `Content-Encoding: ewp-rsa-aes128cbc` only. You are
NOT REQUIRED to support multiple `Content-Encoding`s. If the request contains
more that one (e.g. `Content-Encoding: gzip, ewp-rsa-aes128cbc`), then you MAY
respond with HTTP 400.

If `ewp-rsa-aes128cbc` is not present among the encodings in the
`Content-Encoding` request header, then the client doesn't use this encryption
method, or is using it incorrectly. In this case:

 * If you support other request encryption methods at this endpoint, then
   you SHOULD check if the client doesn't use any of those.

 * If the client doesn't use any of the supported authentication methods, then
   you MUST respond with HTTP 400 error response, with a proper
   `<developer-message>` (it SHOULD describe the reason why you consider the
   request to be invalid).


### Decrypt the payload

Read the body of the request. It SHOULD contain a valid `ewpRsaAesBody` block,
as specified in [`ewp-rsa-aes128cbc` Encryption Specs][encr-spec].

If `recipientPublicKeyFingerprint` doesn't match any of your public keys,
`ewpRsaAesBody` block is invalid, or cannot be decrypted for any reason, then
you MUST respond with HTTP 400 error response, with a proper
`<developer-message>`.


Implementing a client
---------------------

### Find your recipient's public key

First, you will need to check the Registry Service, and fetch the latest RSA
public key of your recipient. In case there are many, you can use any of them.


### Include all required headers

You MUST set your `Content-Encoding` header to `ewp-rsa-aes128cbc`. You SHOULD
NOT use multiple values in your `Content-Encoding` header, because the server
is not required to support it.


### Encrypt the payload

Encrypt your payload, as described in [`ewp-rsa-aes128cbc` Encryption
Specs][encr-spec]. Include the `ewpRsaAesBody` block in your request body.


Security considerations
-----------------------

### Main security questions

The [Authentication and Security][sec-intro] document
[requires][sec-method-rules] each request encryption method to explicitly
answer a couple of questions:

> How the client's request must look like? How can the server detect that the
> client is using this particular method of encryption?

Server detects this method by checking if `ewp-rsa-aes128cbc` is present
among the client's `Content-Encoding` request header values.

> How the server publishes his encryption key? How the client retrieves it
> securely?

The server publishes its key via its Discovery Manifest API. It is then picked
up by the Registry Service, and the Registry's catalogue is updated. The client
fetches these keys via the Registry API.

> How to encrypt and decrypt the request? Which parts are covered by the
> encryption and which are not?

Encryption and decryption is specified above. Only the body of the request is
encrypted. HTTP headers are not encrypted.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management/blob/stable-v1/README.md#statuses
[sec-intro]: https://github.com/erasmus-without-paper/ewp-specs-sec-intro
[sec-method-rules]: https://github.com/erasmus-without-paper/ewp-specs-sec-intro#rules
[encr-spec]: https://github.com/erasmus-without-paper/ewp-specs-sec-rsa-aes128cbc
