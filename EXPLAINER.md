# Autofill API Provider

## Authors:

- Jonathan Garbee <jonathan.garbee@gmail.com>

## Participate
- [Issue tracker]
- [Discussion forum]

## Table of Contents [if the explainer is longer than one printed page]

[You can generate a Table of Contents for markdown documents using a tool like [doctoc](https://github.com/thlorenz/doctoc).]

## Introduction

Some native operating systems expose an [autofill](https://developer.android.com/guide/topics/text/autofill-services) framework that allows applications to expose portions of themselves to have input provided from an autofill manager. Allowing a web application to become an autofill manager would allow for easier cross-platform development and a more seamless user experience.

## Goals

People should have a seamless way to store credentials and have them provided to the appropriate domains or applications on their systems.

* Expose web apps to be able to provide autofill information to native OSes and other web apps
* Allow for secure authorization and selection of these credentials before they are handed over
* Expose a password generation mechanism from the apps for creating new entries

## Non-goals

TBD

## Autofill Manager Registration

Allow a web application to register itself as a provider of credentials to the host operating system/browser.

This could be done either by a key stored in the `manifest.json` of the web application or through a Service Worker API.


Service worker:

```js
ServiceWorker.registerAsAutofillManager('My Autofill Service');  // Should we pull the label name from the manifest?
```

Manifest:

```json
{
	name: 'My Autofill Service',
	provides: [
		'PasswordManager',
	]
}
```


## Autofill Request Event

When registered and selected as the autofill provider, when a user requests an autofill through the framework an event would be sent to the application to retrieve data.


Incoming Event Handler
```js
{targetObject}.addEventListener('autofillRequested', function(event) {
	const targetOrigin = event.originDomain; // undefined if request is coming from outside of the browser. If from within browser, the origin domain initiating the request. For example: 'example.com' or 'authentication.example.com'

	const targetAppIdentifier = event.originAppId; // undefined if request is coming from within the browser. Otherwise it is the identifying name or label associated with the application to the operating system.

	const requestedFields = event.fields; // Array of targetable entries needed to fulfill the request. i.e. ['username', 'password'] or ['flyerId', 'lastName', 'password']

	// App does work to select the proper credentials to feed back

	// Respond to the event with an object where the keys are the requested entries that have known values
	// Values may be `null` or `undefined` if not available.
	// Q: If no entries were found available should the value itself be an empty object or null?
	event.respondWith({
		'username': usernameValue,
		'password': passwordValue,
	});
});
```


## Key scenarios

A user is browsing the web on `somesuperstore.com` and they have an account there. They store their credentials within a web application called `passwords.me`. While on `somesuperstore` they go to the login form and need to get their data from `passwords.me` into this form. The user can activate the form, which when properly marked up would be recognized as an autofill target. This would trigger a browser UX requesting if the user wishes to provide credentials from their vault to this site. The user agrees which initiates the `autofillRequested` event against the registered target provider. The target provider gets some allocation of space visually on the screen to authorize the user can access the vault (pin/password, biometrics, etc). Once authorized they can provide a list of reasonable choices based on the context data sent in the request. Should none of those suffice, the user needs to be able to search and select from all of their stored content.

### Scenario 1

[Description of the end-user scenario]

```js
// Sample code demonstrating how to use these APIs to address that scenario.
```

### Scenario 2

[etc.]

## Detailed design discussion

### [Tricky design choice #1]

[Talk through the tradeoffs in coming to the specific design point you want to make.]

```js
// Illustrated with example code.
```

[This may be an open question,
in which case you should link to any active discussion threads.]

### [Tricky design choice 2]

[etc.]

## Considered alternatives

[This should include as many alternatives as you can,
from high level architectural decisions down to alternative naming choices.]

### [Alternative 1]

[Describe an alternative which was considered,
and why you decided against it.]

### [Alternative 2]

[etc.]

## Stakeholder Feedback / Opposition

[Implementors and other stakeholders may already have publicly stated positions on this work. If you can, list them here with links to evidence as appropriate.]

- [Implementor A] : Positive
- [Stakeholder B] : No signals
- [Implementor C] : Negative

[If appropriate, explain the reasons given by other implementors for their concerns.]

## References & acknowledgements

[Your design will change and be informed by many people; acknowledge them in an ongoing way! It helps build community and, as we only get by through the contributions of many, is only fair.]

[Unless you have a specific reason not to, these should be in alphabetical order.]

Many thanks for valuable feedback and advice from:

- [Person 1]
- [Person 2]
- [etc.]