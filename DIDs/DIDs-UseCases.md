# Focal Use Cases for Decentralized Identifiers

A good use case is one that is:
A. Unique -- minimal overlap with other use cases
B. Relevant -- highlights the particular value of DIDs
C. Value Creating -- there is demonstrable value to the people at the heart of the use case
D. Simple yet Sticky -- simple enough to be accessible, but also captures a potentially complicated edge case. 
E. Specific -- Uses real names and real situations to help readers empathize with the human requirements

For D, it's great when the basic functionality is straightforward and we fold in a question of "but what if..." and illustrate how DIDs handle a particular real-world problem better than existing approaches.


## Life-long, Recipient-owned Credentials

Educational Verifiable Credentials offer benefits over traditional educational credentials in that the recipient is able to store and share their credentials, and a third party may independently verify the claim (without necessarily consulting the issuer). This provides the promise of recipient-owned long-lived credentials that the recipient may use even if the issuing institution goes out of business. 

Usability issues around cryptographic keys introduce a threat to achieving this goal; if the recipient loses their private key, they lose ability to prove ownership of a credential. The existing ways to re-obtain the credential include re-requesting the credential from the issuer, or TBD.

If a credential is issued to a recipient's DID, the recipient has the ability to prove ownership of a credential even if the recipient loses a private key used to show ownership of a credential referenced in a claim.

Yanny uses DIDs as subjects of its Verifiable Credentials. By using DIDs, Yanny is able to prove “ownership” of a credential. Even if Yanny loses the private keys corresponding to all keys referenced in the DID document, Yanny is able to invoke the update method on the DID to once again gain control over the DID, and therefore continue to use the Verifiable Credential.

## Self-Sovereign Investor Compliance

There are certain Third World countries where successfully investing in cryptocurrencies could lead to kidnapping or even government sanctioned extortion. In these cases, the identities of early cryptocurrency developers who chose to mine Bitcoin, but who live in Third World countries, must be safeguarded. Certain global security regulations demand revealing his identity to parties that have, frankly, been hacked in the past. Those failures to preserve privacy, which are only a nuisance in America, could potentially lead to deadly consequences in the Third World.  In this case, revealing his identity could lead to the kidnapping of loved ones, or even extortion by tribal leaders or corrupt governmental officials. After all Bitcoin is pre-formatted as the perfect untraceable ransom currency.

As an early Bitcoin enthusiast and miner, Kablan wants to diversify his holdings by supporting promising ICOs so that he can reduce his concentration risk in Bitcoin, however, he needs to preserve his anonymity as a safeguard. In this case, his pseudonymous DID holds a verifiable claim that provides an attestation by a licensed and bonded EU attorney, which should fulfill the KYC/AML requirements to invest in ICOs in Europe. Thus, the government is assured he is not a terrorist or money launderer, and he is able to invest more effectively with greater personal security. This will allow him to be an effective member of the crypto community. Kablan’s initial investment, if he were free to optimize via a portfolio strategy and invest in better ICOs launched in the US and EU, could theoretically eventually make him one of the top venture capitalists in his own country, who could fund multiple startups to break the cycle of terrorism and despair. Stifling crypto innovation has an impact globally.

The attorney is required to disclose the legal nexus for the subject. Since Kablan is a resident of a country that is on a watch list, so it will make it harder for the attorney to meet KYC/AML requirements.

By constraining the rights of certain people to invest freely and lift themselves out of poverty, AML laws are actually strengthening terrorism in an indirect way.

## Single Sign On

Passwords are notoriously misused ("123456"), stolen from the supposedly-secure database on the server-side, easy to forget when sufficiently secure, and never the last word in authentication for forgotten password situations.  Proving control of a DID can replace storage and retrieval of a shared secret.

Use DID as single-sign-on to a website, using DID Auth (especially cases like the example between a web page and web browser with a mobile identity app) directly or via the Credential Handler API.  When desirable, the relationship can add a shared secret for 2FA (except does DID Auth include any extensions that enable this subsequent ascension without starting another socket?).

Note that what is stored on the server-side is a DID, so in cases where a successful attack reads (but does not mutate) the database, all that is revealed is linkage of the DID to use the site (so probably as a consequence the user should spawn a unique DID to reveal to that site), and any 2FA secret (which should be a random number for TOTP, instead of a phone number susceptible to carrier social engineering and SS7 bugs).  In other words, the user's DID Auth procedures remain valid after the hack, even for that site.

Transfer sign-on capability from control of a password to control of the DID, as shown in DID-Auth, using appropriate devices.  Optionally include 2FA, although DID Auth doesn't handle that well, yet.

This use case describes the most common authentication action for people on the Internet.

## Institutional Library Users

A member of an authorized user community, such as a University Library, gains access to subscription resources provided by multiple publishers. When the user is within the library’s physical walls, she can access the materials with her authorized credentials. When she is on her mobile phone, she can access the same materials remotely. She is required to provide her credentials as a member of the authorized community to access the materials without paying a fee for the content.

Notes: There could be a single identifier that students use to sign in to library resources, no matter which attend.  The IDs are NOT about the privileges attached to their relationship with any given university, nor are they attached to the relationship between a university and a resource provider. Those privileges would be associated with such IDs, but the IDs themselves are independent.

* Universities A, B, C issue a “shared resource” credential to their students
* Students with the shared resource credential have access to libraries that recognize it.
* University D would like to issue the shared resource credential to their students
* University A,B,C give University D tools to issue the shared resource credential
* University B leaves the group, and their students are no longer allowed access to the shared resources

## Profile Preferences

Under the current system, our personal data (de-personalized or not) is the product being sold by platforms like FB and Google to data warehousing companies like Axciom, who create consumer e-profiles that they sell to marketers. These profiles are also used to program algorithms that are used in employment, finance, insurance sectors, among many others, to sort and eliminate applicants. 

The way our consumer profiles are being used against us is the direct result of a data ecosystem built around the small privacy requirements established in early days of internet. If, instead of giving away data that we insist be anonymized, we were to own our own personal e-profiles via persistent DID, we would be able to correct simple mistakes like someone who shares our name but isn’t us. (I think there was another use case like this, perhaps we can combine them. Collecting and controlling an e-profile on ourselves could become an assertion of at least who we are NOT. False negatives effect black people disproportionately, often because zip codes are used as a proxy to race. This ‘birds of a feather’ system becomes a sentence to those trying to move out of poverty.

After dealing with a mistaken identity entanglement in which her name and that of a felon showed up on many of the same searches that banks and creditors use, Sherry decided to take more control over her identity. She uses a service that collects consumer information on her in the same way advertisers and creditors do. At first she signed up to this service to clear her name, but now she cashes in on the perks of controlling her identity by participating in the verification or objection of third party data profile information, she has built her own trusted profile which keeps information that has been collected without her knowledge or approval from entering her verified profile.































