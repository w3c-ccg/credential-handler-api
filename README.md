# credential-handler-api
Repository for the Credential Handler specification of the Credentials Community Group

A demo of the credential handler in action is here:

https://credential-repository.demo.digitalbazaar.com/

The demo works in Chrome, Firefox, and Edge. To make it work in Safari, you
will have to allow third party cookies "Always":

https://stackoverflow.com/questions/38584273/local-storage-cross-domain-safari-disables-it-by-default/38793832

This is because the Credential Handler polyfill uses a neutral, shared, third
party website to store the credential handler registrations users create. This
is not the credentials themselves, but a registration list of the one or more
credential handlers (i.e. digital wallet URLs) that you have previously
registered. Storage of this registration list enables the browser to show the
user a UI with the digital wallets that could possibly fulfill a request for
credentials from a relying party website. If this feature were implemented
directly in the browser (rather than in a polyfill), this settings change
would be unnecessary.

This change is not required in Chrome, Firefox, or Edge because they already
use this setting as their default for third party cookies and storage.
