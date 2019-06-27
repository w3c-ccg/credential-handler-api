# credential-handler-api
Repository for the Credential Handler specification of the Credentials Community Group

## Background

A Credential Handler is an event handler for credential request and
credential storage events. The [Credential Handler API][] helps
solve the [Nascar Problem](https://indieweb.org/NASCAR_problem). The
[Credential Handler API][] enables websites to install Credential Handlers that
can respond when users visit other websites that request or store credentials.

For example, a user may visit a website that wants them to login using
OpenIdConnect, provide an OAuth Token, authenticate using a [DID][], or present
some [Verifiable Credentials][]. When these other websites use the [Credential
Handler API][], the user is shown an in-browser selection screen with visual
representations (e.g. icons and origin information) of only those
Credential Handlers that they have been previously installed by the user and
that are compatible with the website's request. Once the user makes a choice,
the appropriate Credential Handler is loaded and a credential event is sent
to it.

The Credential Handler receives the event via a
[Service Worker](https://w3c.github.io/ServiceWorker) or, if the
[Credential Handler Polyfill][] is used, a simple page with no UI elements is
loaded that uses the polyfill to receive and respond to the event.

The Credential Handler must respond to the event with a credential that
fulfills the request. If necessary, the Credential Handler may open a window
on its website's origin to allow the user to interact with its website prior
to responding. This UI can be styled and shaped according to the website
owner's brand using arbitrary JavaScript and HTML like any other webpage.

## Demo

***We provide a demo of this potential future Web feature below. Please note that the demo uses a polyfill that provides UI that emulates secure browser chrome. To repeat, this polyfill UI is an emulation and IS NOT actually implemented by the browser. Support for this API could make this UI (or most likely a much better one!) a reality in browsers in the future.***

The demo of the credential handler in action is here:

https://credential-repository.demo.digitalbazaar.com/

A video of the demo is here:

https://www.youtube.com/watch?v=bm3XBPB4cFY

The demo works in Chrome, Firefox, Edge, and the latest version of Safari/iOS
(Safari must have support for the Storage Access API):

The Storage Access API is required in Safari because the Credential Handler
polyfill uses a neutral, shared, third party website to store the credential handler
registrations users create. This is not the credentials themselves, but a
registration list of the one or more credential handlers (i.e. digital wallet
URLs) that you have previously registered. Storage of this registration list
enables the browser to show the user a UI with the digital wallets that could
possibly fulfill a request for credentials from a relying party website.

Prior to implementing the Storage Access API, Safari partitioned its third
party storage and cookies in such a way that the polyfill could not function
without the user manually changing settings in the browser.

## Explainer

## Roles

The Credential Handler API's design is such that there are four roles that are
played when passing credentials over the Web:

### Credential Repository

This role handles credential storage and requests for a user, acting as their
"wallet". This role is implemented as Web App on a credential wallet website.

### Credential Issuer

This role issues credentials to a user for them to put in a wallet.

### Credential Verifier

This role requests credentials from a user. Users fulfill this request via a
wallet of the user's choice.

### Mediator

This role mediates all credential storage and credential requests for a user.
It is responsible for helping the user make wallet choices and proxying
requests and responses to/from relying party websites (credential issuers and
credential verifiers) and the user's chosen wallet. This role is played by the
user agent (browser).

## Typical Flow

1. A user visits a "credential wallet" website.
2. The wallet web app registers a "Credential Handler".

During this step, the web app calls a new JavaScript API to register a piece of
code (a "Credential Handler") that will execute when the user later elects to
use the web app to fulfill a request for credentials or a request to store
credentials.

3. The user visits a Credential Issuer website to receive a credential.

The Credential Issuer website calls the Credential Management API to store a
new type of Web-based credential (i.e. essentially a third party credential, or
a credential that may be used across domains).

4. The browser shows the user a UI with information (icons, text, origins)
  representing previously registered credential handlers that can store their
  credential for them.

When the user makes a selection, the credential to be stored is forwarded to
the chosen handler which then allows the user to interact with the associated
Web App from the wallet origin as needed to complete the storage request.

The browser is playing the "Mediator" role here: forwarding a request from the
Credential Issuer to a third party Credential Repository of the user's choice.
The Credential Issuer doesn't care which wallet is used. All the mediator needs
to do is be able to match up credential storage requests with wallets that
support their storage and present these options to the user.

5. The user visits a Credential Verifier website which requests third party
  (Web-based) credentials.

Here the Credential Verifier website calls the Credential Management API using
a query for a new type of Web-based credential (i.e. essentially a third party
credential, or a credential that may be used across domains).

6. The browser shows the user a UI with information (icons, text, origins)
  representing previously registered credential handlers.

When the user makes a selection, the Credential Verifier's request is forwarded
to the chosen credential handler.

The browser is playing the "Mediator" role here: forwarding a request from the
Credential Verifier to a third party Credential Repository of the user's
choice. The Credential Verifier doesn't care which wallet is used; they have
a trust relationship with Credential Issuers, not the wallet provider. All the
mediator needs to do is be able to match up requests for credentials to the
user's preferred wallets for fulfilling those requests.

7. The user interacts (as needed) with the wallet Web App and chooses the
  credentials to fulfill the request.

Here the Web App resolves a Promise with the user's response. The response
may include additional authentication information is necessary to prove
ownership over the credentials. This is up to the specific Web credential
scheme/protocol.

8. The browser forwards the credential response to the Credential Verifier.

9. The Credential Verifier verifies the credentials and proceeds however
  it sees fit.

This flow works for DID-Auth, passing Verifiable Credentials, or using other
systems such as OpenIDConnect.

TODO: Links to DID-Auth, Verifiable Credentials, and OpenIDConnect

TODO: Diagrams of the roles and data flows

[DID]: https://w3c-ccg.github.io/did-spec
[Verifiable Credentials]: https://w3c.github.io/vc-data-model
[Decentralized Identifiers (DIDs)]: https://w3c-ccg.github.io/did-spec
[Credential Handler API]: https://w3c-ccg.github.io/credential-handler-api
[Credential Handler API Repo]: https://github.com/w3c-ccg/credential-handler-api
[Credential Handler API Demo]: https://github.com/digitalbazaar/credential-handler-demo
[Credential Handler Polyfill]: https://github.com/digitalbazaar/credential-handler-polyfill

