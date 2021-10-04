# Technical Considerations

Like our user and product notes, what follows are a series of sketches, not an implementation roadmap. 

Our initial prototypes explored ways to represent and share attestation information when entering federal buildings. Because we chose to use Smart Health Card technologies at an early stage (an open standard for representing and sharing personal health information), our pivot to *evidence* of vaccination was smooth. Or, the ability to leverage our learning was smooth, but the systems design became more complex, as we would potentially be storing more, not less, PII/PHI (personally identifiable information, personal health information) in the evidentiary case. What follows is the breadth and depth (such as it is) of our considerations regarding the technologies that might support storing and reporting on an evidence-backed tracker of vaccination status for the federal workforce. 

## Goals of any technical solution

Before we delve into the specifics of technical approaches to vaccine attestation or certification of vaccine data, we like to consider our [principles](FIXME) and reaffirm that any work in this space will meet the highest moral and ethical standards. These broad principles to guide our technical work:

* **Safety**: Any system must support keeping people safe: both physical safety as well as _psychological safety_, or the perception that the system and the actions it enables will keep people safe.
* **Privacy**: Attestation information for employees, contractors, and visitors must be kept private; we recommend _auditable, open systems and protocols_ as the foundation for any technological solution adopted.
* **Transparency**: It must be clear to participants in and around the system as to _why_ information is gathered and _what_, if anything, is done with it.
* **Clarity**: There should be no uncertainty or lack of clarity regarding processes, be it digital or otherwise. From signage to consequences, _process clarity_ will provide confidence that the government is acting in an informed and consistent manner.

## Technical Design Considerations

We strongly believe that [Smart Health Cards](https://smarthealth.cards/) are the path forward for verification of any and all vaccine-related credentials. The Smart Health Card protocol was designed to be saved and shared, and comes with built in safeguards to avoid accidental medical information leakage. For instance, the user can selectively reveal their medical information (they do not have to present their entire health history when sharing vaccination details), and the card itself does not store any personal information beyond legal name and date of birth. 

Smart Health Cards can also be _certified_&mdash;that is, they come with an accompanying signature and chain of certification that can be traced back to the originator. This type of certification, we assert, is one of the few ways we can know for sure that the provided vaccination information is truthful. 

These cards are already in widespread use in five states, including California[^cadvr]. In terms of user paths in vaccine verification systems, the “easy” path would be for someone to scan in a certified Smart Health Card QR (quick response) code and that is all they would have to do. Accordingly, we believe that adoption of the Smart Health Cards protocol should be encouraged whenever possible: agencies may also want to re-issue validated Smart Health Cards with their agency-specific certification to allow other use cases such as public entry (and potentially further reduce the amount of personal information encoded in the credential).

We cannot, at the present time, recommend the [California Digital Vaccine Record](https://github.com/StateOfCalifornia/DigitalCovid19VaccineRecord-API) system because we do not think it is built on open standards. We would recommend developing an open reference system, built on open source tooling and embodying best development practices such as automated unit- and end-to-end tests as well as automated deployment and rollback. 

We would also encourage future implementers to avoid a “check-the-box” exercise and pursue auditing and certificate systems whenever possible. We believe that the outcomes of vaccination certification should be _meaningful_. Vaccination percentages are not optics. They are quantifiable metrics that say something about the public health of our nation. 

This means thinking thoroughly about how the people using the system (employees, supervisors, approving officials, etc.) interact with our system, and play out possible abuse vectors. A [recent case in New Jersey](https://www.nj.com/coronavirus/2021/09/university-hospital-fires-several-employees-over-fake-vaccination-cards.html) demonstrated the possibility of fraud at a rather high level. We acknowledge that a balance must be struck between usability and fraud prevention. That being said, the friction of submitting fraudulent information should be high enough to discourage most users.

We have also consulted with Linux Foundation Public Health who also endorse open standards: among these, their own [Good Health Pass Interoperability Blueprint](https://drive.google.com/file/d/14EzejZc_O_c3gThPFccO_mSyNkc-RVPs/view). Built on the [W3C verifiable credentials data model](https://www.w3.org/TR/vc-data-model/), the Blueprint is for slightly different purposes (health passes for travel) but is still a critical read and reference in this space.

## Design in a Nutshell

In broad strokes, we imagined a system that would *not* store PII/PHI. We believe that validating vaccination records **does NOT require** the Federal government to store PHI. We also believe a safer system can be designed if it stores less information, and it is unlikely to be abused at some later point in time[^exfil]. 

To achieve this, we saw a path where the Federal government helps states and businesses offer [Smart Health Cards](https://smarthealth.cards/). Why? 

1. States already hold digital vaccination records for their population. Those records may not be *accessible* to the residents of states, but the records exist. The Federal government could provide open tooling to make it easy for states to safely make a machine-readable version of those records available to residents.
1. Large businesses who are in the medical industry or provide shots (e.g. Cerner, Epic, Walmart, CVS) have already adopted these standards, and are able to issue a digital version of a vaccination card. Along with a state-issued pathway, it is possible for private businesses to offer digitally signed vaccination records.

This was not an overnight pathway, but it was one that **kept medical records in the hands of the public**, as opposed to centralizing the collection of those records within Federal agencies. 

Our incremental approach to this problem could be described as follows.

### Offer the GSAttestation Pass

The first step was to solve the problem of Federal employees (and, ultimately, contractors and visitors to Federal buildings) having a way to easily indicate their vaccination status when entering a building. (This was our charge.) An employee could enter their vaccination information into our system, and we could generate (and sign) a Smart Health Card that captured that information. The card would *not* be accepted for things like restaurant reservations, but *would* be usable for entering buildings. Because it is a standard, we could easily modify open tooling to support our "GSA health card." 

This would also support mandatory testing programs, as SHCs are capable of encoding tests as well as vaccination records. 

While this would not be a "validated" health credential, or (put another way) "proof" of vaccination status, it was a step, and solved for many use cases we identified during our discovery and research phase. 

It also put SHCs into use within the Federal government, which would normalize them as a tool.

### In parallel, accept state-issued SHCs

Once we have an infrastructure for SHCs, we can begin accepting state- and business-issued (valid) SHCs. For example, all Federal facilities in the states of California, Louisiana, and New York could *immediately* begin accepting SHCs for building entry. Why? Because these are cryptographically signed, validated records of vaccination status. 

This is possible because the "GSAttestation Pass" is really just a "self-signed" SMART Health Card. Because it is an open standard, we can easily accept "real" credentials from people in states where they are available.

### Clone the CA system

To support states in moving to SHCs, we clone the California system.

The CA system was issued as open software on September 10th. One path would be to run it "as-is." However, the system was implemented in .NET, and requires a proprietary toolchain for implementation. We would recommend reworking this system on an open toolchain, and (perhaps) making sure the system is FIPS-140-2 compliant along the way. 

In re-implementing the system, we do the DevSecOps work to enable states to run this system on their own systems/clouds, or we offer the service to them directly. Connectors to their datastores (or a mechanism for loading vaccination data in a privacy preserving manner, perhaps through PPRLs or similar) would need to be provided. 

We did not do any investigation into whether states would be interested in this kind of software/partnership. However, we believe that a good role for the Fed in this space is to *provide the infrastructure* that enables states and businesses to easily adopt open standards that keep medical health 1) decentralized and 2) in the hands of the public.

### Step 3: Profit

As the old internet joke goes:

1. Build the system.
2. Magic happens.
3. Profit!

We don't think it is quite "magic happens" so much as "lots of hard work and community building." However, we believe it is doable work. 

As we have uptake and adoption of verifiable credentials, Federal employees, contractors, and (if emergency OSHA guidance comes through) roughly 100M US workers will have a way to obtain credentials that *they control*. We can then use those verifiable credentials to easily ask the question "are you vaccinated?" We do not have to store the entire vaccination history (nor should we), but can instead store a simple boolean value: **YES**. (Or, as the case may be, **NO**, but we assume there may be accommodations at work in that case.)

### Conclusion

That's the design "in a nutshell." We implemented some light prototypes that demonstrated interaction loops, enough to communicate the what an SHC might look like (on a phone, on paper) as well as how it enables lightweight, contactless interactions with a minimum of software and hardware support.

Commercial marketplaces are emerging that fail to preserve people's privacy, and puts their health data in the hands of private corporations. Unfortunately, the US has *exceedingly poor* laws to protect people's PII, and as a result, a new marketplace of vaccination histories is likely to emerge (if it has not already). We remain hopeful that solutions and implementations will emerge that protect people's privacy while meeting the needs of employers and the Federal government.

# Early ATO Considerations

Based on a reading of [FIPS-199](https://nvlpubs.nist.gov/nistpubs/fips/nist.fips.199.pdf), we believe we were likely building a FISMA Moderate system. (In part, this was when we were considering a system that might have to store the images of CDC cards.) 

We assumed we would need to be [FIPS-140-2 compliant](https://csrc.nist.gov/publications/detail/fips/140/2/final). It turns out that there aren't a lot of (any?) turnkey systems we could build on top of to achieve this. We were looking at:

* [Ubuntu Pro](https://ubuntu.com/aws/fips) (which provides a certified FIPS-140-2 OpenSSL module), so we might work with an open language of our choice and be certain (?) to be getting FIPS-140-2 crypto.
* [Bouncy Castle](https://www.bouncycastle.org/fips_faq.html), a FIPS certified crypto environment for Java and .NET. We would need a license, it seems, which we were uncertain how we would go about obtaining/using. We assumed we would build our crypto work as an API, and perhaps call it from elsewhere?
* Login.gov's "envelopes". Login has a nifty "enveloping" process, whereby they encrypt user's PII/images of credentials in a way that only the user can decrypt. We wondered about yanking that code out into an API, and calling it from our app. It would save some dev time (perhaps?), but would still require an Ubuntu Pro environment. 

We were unsure who the owner of the system would be, even as we began exploring an ATO. (And, it would need to be a fast ATO process to meet the deadlines we were facing.) If this work is picked up, the ATO and ISSO need to be identified *right away*, because the systems design should be done *in concert* with those people, so that they know what they're considering from the beginning.

We learned that different agencies are more or less "strict" on some aspects of ATOs. Hence, any future team working (we assume within the GSA) would need to get on this *pronto* so that the complex compliance pieces can be navigated early, and (if need be) the architecture and design altered to reduce the complexity of compliance. (Or, if compliance is considered from the beginning, it is possible that different design/implementation choices can be made that meet the needs/function of the application as well as lower risk bar for compliance. This should be a discursive/collaborative process with the product owner and ISSO.)

# Information Architecture

As we were moving into design and build, we were working on a data model that would help us protect PII/PHI.

## Separating IDs from records

Conceptually, the heart of this would be PPRLs, or privacy-preserving record linkages [^1]. PPRLs involve taking a subset of data in one table, hashing it with a secret, and using that value to look up linked data in a second table. 

For example, consider two tables, one with identifiers and one with vaccination records.

| Name      | DOB | Phone |
| ----------- | ----------- | -------- |
| Molari | 2/3/40 | 555-1213 |
| Sheridan | 1/2/30 | 555-1212 |

| PPRL | Record |
| ---- | ------ |
| 5ab2e8ac6897e2211 | { "vaccine" : "J&J", ... } |
| 252882ae60c86a532 | { "vaccine" : "Pfizer", ... } |

Hashing the name, DOB, and phone of an individual in our first table (along with a secret) generates a value that can be used for lookup in a second table. (This is a very fast-and-loose, representative example; FIPS-140-2 compliant implementations will have additional considerations, and additional design work is certainly needed for a secure and privacy-preserving PPRL design.) 

We considered this kind of design for two reasons. One was in the event that we needed to develop a system that would store PHI. Using a design of this sort would allow us to separate out names/PII from the PHI. Although we assumed that a DB would be encrypted at rest, we thought this design might help protect us from an internal actor who had access to the data, but not the secrets. If they exfiltrated the DB, they would get a list of names and a separate list of vaccination records... but, there would be no way to connect one to the other. This design also allowed us to think about reporting as queries over the vaccination records, such that someone responsible for reporting could have full access to the vaccination records, but would not need/have permission to work with the linked data. (That would be work for a compliance officer.)

We also considered this design as a way for states to tie into a federally provided Smart Health Card system. If we shared a secret with a state, we could provide a way for individuals to obtain verifiable digital versions of their health information from the state (through federally provided software), but we as the Fed would not actually need to have access to the source vaccination data. (We spent very little time on this design.) However, PPRLs are intended to help facilitate exactly this kind of linkage, and would represent a good design space to pursue further if a SHC framework between the fed/states is ever pursued.

## Envelope protection of CDC card images

Login.gov uses a double-encryption mechanism of images of personal IDs (driver's licenses, passports) that keeps the encryption keys in the hands of the individual.

When we were considering the possibility of having to build a system that would hold images of CDC cards, we considered using a similar technique. That way, if we had to store this information, it would be under an encryption key only the user controlled. 

However, we knew the images would need to be shared with supervisors; this led to questions of whether we would need to share a key with a user (e.g. six words chosen from a 7000-word list), so that they could share those words with a supe *once*. After sharing the words, they would then be issued a new "passphrase," as we would want to re-encrypt the image in a way that the user (once again) had control over who saw the image/decrypted it.

Again, our threat model was a disgruntled employee who decides to exfiltrate all of the images. (Or, a misconfigured system that a nation state attacks because they know the level of public perception damage leaking the data would cause.) Allowing this data to sit in plaintext bothered us greatly, and we very much wanted the user (not the government) to have control over when the images could be viewed. This made for a complex systems design/encryption model.

## All in RAM (verify in-session)

Unfortunately, we were under the impression that we **must** store images of CDC cards.

We also considered designs where a user would upload their CDC card image, and it would be stored in RAM. The user could "key" that data with a simple passphrase. A supervisor would be given the passphrase, and we would share the image once and only once. The image would therefore never hit storage, and remain entirely ephemeral. (We would also purge images after, say, 2 minutes.) In this way, an employee would be able to share the CDC card image with a supe, who could say "yep, that is *not* a picture of a cat!," and then we'd be done. No storage of PHI.

## Why?

Why all the convolution around PHI?

Because we were trying to think about systems designs that would be safe for use by contractors and visitors, not just employees. We wanted something that we could consider using to solve the larger problem, and that involved thinking about 1) trust, and 2) privacy, and 3) the threat model around this data. On one hand, we think the data itself is relatively low risk. On the other, we think that setting precedents where we expect employees to hand over PHI is problematic, and therefore we worked hard to come up with models and solutions that would keep PHI in the hands of employees (and not in the hands of the Federal government). 

Solutions that involve storing images in systems like a human resources system (because it is the only system rated to handle PHI) or, worse, a Google Drive/Sharepoint type system worries us greatly. We believe that significant harm would come from the exfiltration or hacking of a system that stores federal employee health information. That harm would not just be to individuals, but also to the work going on nationally to 1) try and get people vaccinated and 2) encourage the uptake of verifiable credentials based on open standards. A mistake with this system will set back both of those agendas.

[^1]: https://www.jmir.org/2020/6/e16757/

# Prototypes

We developed a [rough prototype](https://federalist-0c90a10a-2681-4916-b579-d349724daf4e.app.cloud.gov/site/18f/vaccine-attestation-v1/v2.html) for vaccination attestation for the purpose of federal building entry. 

In this prototype, no data is stored whatsoever. Indeed, the prototype consists of a simple dropdown of attestation choices, and selecting any option will populate a QR code. This QR code encodes a _behaviorial_ consequence of an attestation, and does not store the chosen attestation either. Thus, if someone selects “I have an accommodation from my employer”, the behavioral consequence in the QR code will say “The individual bearing this attestation should wear a mask regardless of local conditions” and nothing else.

We believe this prototype adheres to our principles and is as privacy preserving as possible. A separate mobile application was also developed to scan in the QR code and present the behavioral consequence. 

![a mobile application reading in a qr code and subsequently displaying a masked emoji](/images/mobile-attestation-pathway.png)

# Resources

Below are some links and resources to organizations who are active in the vaccine certification space, as well as links to open standards that are similarly used in this space. 

## Organizations

- [Linux Foundation Public Health](https://www.lfph.io/), “builds, secures, and sustains open source software to help public health authorities (PHAs) combat COVID-19 and future epidemics.”
- [Vaccine Credential Initiative](https://vci.org/), in their own words, “a voluntary coalition of public and private organizations committed to empowering individuals access to a trustworthy and verifiable copy of their vaccination records in digital or paper form using open, interoperable standards.”
- [Covid Credentials Initiative](https://www.covidcreds.org/), “an open global community collaborating to enable the interoperable use of open-standard-based privacy-preserving credentials and other related technologies for public health purposes.”
- [Microsoft Research](https://www.microsoft.com/en-us/ai/ai-for-health), AI for health
  - We particularly like their [Vaccine Credential Technology Principles](https://arxiv.org/pdf/2105.13515.pdf)

## Open Standards

- [W3C Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model/)
Cryptographically secure, privacy respecting, and machine verifiable credentials
- [Smart Health Card](https://smarthealth.cards/index.html) framework
  - Designed for storing, sharing, and presenting clinical/medical information
  - Already in use in several states

[^cadvr]: The California Digital Vaccine Record is based on Smart Health Cards, and even proprietary systems like New York’s Excelsior system have [made changes](https://www.governor.ny.gov/news/governor-cuomo-announces-launch-excelsior-pass-plus-support-safe-secure-return-tourism-and) to comply with the SHC specification.
[^exfil]: We consider disgruntled employees exfiltrating data amongst our threat models.
