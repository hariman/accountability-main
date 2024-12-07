# accountability-main
Main repo for accountability project

# Purpose
The project purpose is to aid accountability on the Internet in general by providing a simple and free way of signing and checking signatures on documents on the Internet. 

## Free and open
Free means completely free, including no commersial tracking of users, as well as open as in Open Source. Note that there might be room for authentication and auxiliary services to charge from users, but the signing and checking signature parts are to be free and open. License at the moment is MIT, but could be something else if required to be "free enough".

# Formats

## Document formats
The format of the signed document obviously needs to be well defined. The idea is to work with two formats:
1. PAdES. This is an established format for signing PDF documents (https://en.wikipedia.org/wiki/PAdES). The advantage with this format is that there is already a standard.
2. Signed Markdown documents. Markdown is simpler than PDF and there is less of a vendor dependency reading and creating them. THe disadvantage is that there is no standard format for signed Markdown documents, so we have to create our own.

### Signed Markdown format
- Markdown document with metadata that is not shown on a normal Markdown reader
- Metadata is: "signed-metadata", i.e. metadata that is part of the signed payload but shown when just viewing the document. This is the signing certificate/chain and info from the authentication (display name, organisation etc); "signature", i.e. the signature value and a timestamp from the signature service.
- Markdown signing signes all the Markdown content and also any embedded file (typically images) in the document. Will have to look at most efficient and sensible way of doing this, whether it's b64 encoding and signing all or whether each embedded item gets their own hash which is added. The idea is though that the embedded items are signed with the master document, since there is otherwise a risk that signed embedded items change independently of the master document.

## Link formats
There also has to be a standard for going from a weblink to a Markdown/PDF version of the document. This is so links/QR codes behave in a consistent way, which is extra important for checking signatures in linked documents. Note that a document could exist in different versions, so there must be a way to either point to a specific version of a document or so that a base link to either the web or the accountability version shows the latest version of the document.
Suggestion at the moment is the following page parameters:  
```
a12y=true
a12y-version=<version GUID>
```

# Implementation
## Signing mechanism
The signing mechanism takes a document and a proof of authentication (SAML? OAuth2? OIDC?) and signs the document, appending the authentication information. See formats for more info.
The intention is that an article/content creator can use the signing mechanism with their favourite authentication method to sign their articles. Potentinally, CMS:s like Sanity could have the signing mechanism as a plugin to generate a signed Markdown version of the web content, and add-ins for i.e. Office365/Google docs could produce either signed PAdES versions or signed Markdown versions of the document.

### Usage flow
- User creates an article/document
- User presses "sign" in CMS/word processing software
- User gets redirected to authentication
- User authenticates
- Document is signed
- User can view the signed document (including meta information like timestamp, selected auth method, auth method certificate and signed ticket/assertion and which user information is shown on the signed document).

## Validation mechanism
The validation mechanism should be an app (computer or app) that displays the Markdown/PDF document, the result of the signature checking/trust and the info. This cannot be web based since a fraudulent actor could produce a deceptive web page. It might however be browser add-ins in a the desktop case and visionarily, it might be a part of the browsers themselves.
Note that an article/content can contain links to other articles/content and if se, the validation mechanism should validate any such links and see if they are signed with valid signatures.
The validation mechanism comes with root CA:s as part of the app and can thus check content signatures without any server component. This is good for keeping the source infra at a minimum.

### Usage flow
- User finds a signed document on the Internet.
- User uses either a link or a QR code to go to the validation mechanism app (on same device or another).
- User views the PDF/Markdown version of the document and the result of the signature checking. Note that there is nothing stopping this document from being different from the web document leading to this, so users need to understand that the verified version of the document is Markdown/PDF one in the app.

# Building trust
Note that this struction does not come with authentication methods, so the signing user has to select one amongst a set of allowed authentication methods and the end user will just see which authentication method has been used. The trust in this structure is just built on guaranteeing that an authentication provider in turn guarantees that a user with certain properties has signed in at a specific time.
This means there has to be a way to accredit authentication methods for use within this structure. The groundwork for this accreditation is a big and interesting issue. For now, we will start with a number of large providers being accredited from scratch.

# Project plan
1. Get an MVP signing and verification service up and running, using the same format.
2. Show the MVP to relevant stakeholders who might be interested in signing their information.
3. Getting the validation mechanism up on App store/equivalent.
4. Look at how to host and by extension fund the signing mechanism.
