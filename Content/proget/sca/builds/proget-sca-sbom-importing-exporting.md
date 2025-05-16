---
title: "SBOM Importing & Exporting"
order: 2
---

ProGet's Software Composition Analysis (SCA) can leverage Software Bill of Materials (SBOM) documents that identify the open-source and third-party software components (i.e. packages) that your applications use. 

This article will explain what an SBOM document is, and how importing and exporting these documents works in ProGet.

## What is a "Software Bill of Materials" (SBOM)?

A "Software Bill of Materials" (SBOM) is a document that lists all of all the open-source and third-party components (i.e. library packages) that are used in a software application. This component list includes specific package versions and their license agreements, as well as any known vulnerabilities in the components.

ProGet uses the [OWASP CycloneDX Specification](https://github.com/CycloneDX/specification) for SBOM documents. This specification also followed by the US Federal Government, and meets several SBOM-related governance requirements, including [Executive Order 14028](https://www.whitehouse.gov/briefing-room/presidential-actions/2021/05/12/executive-order-on-improving-the-nations-cybersecurity/) (also known as "SBOMs for National Security).

To learn more about the technical details of SBOM documents and different specifications, see the [US government's SBOM Research List](https://ntia.gov/SBOM).


## Importing SBOM Documents
You can import SBOM documents for a release from the web UI, or using the [SCA API](/docs/proget/api/sca). 

Behind the scenes, this is how `pgscan` functions (i.e. it generates a document and the imports it). But you may wish to import SBOM documents if you're already using a tool like [CycloneDX](https://cyclonedx.org/) in your CI/CD pipeline.

When importing an SBOM document, ProGet will:
1. Create a Project (based on `Metadata.Component.Version` name), if it doesn't already exist
2. Create a Release (based on `Metadata.Component.Version`), in the project if it doesn't already exist
3. Add Packages to the Release (based on `Components`)
4. Store the SBOM document on the Release
5. Analyze the Release for Issues


### Importing Third-Party SBOM Files

ProGet is designed to help you track *your own* applications, built with components (packages) that your store/cache in feeds. It was not designed to track third-party software that you do not build in-house.

For example, if you were to import an SBOM imported SBOM documents for applications provided to you by a vendor, you would likely end up with a lot of "Missing Package" issues. Those missing packages also wouldn't be scanned for vulnerabilities or license issues.

If you need to track SBOM documents for third-party applications that you do not build, consider a tool like [Dependency-Track](https://dependencytrack.org/)


## Exporting SBOM Documents
You can export an SBOM document for a release from the web UI, or using the [SCA API](/docs/proget/api/sca). This may be required by internal or external auditors, customers, or other users of your software.

When exporting an SBOM document, ProGet will merge metadata from the information stored in ProGet (such as the project name, release number, package licenses, etc.) as well as any additional component metadata found in the imported SBOM documents.

This merging functionality may be particularly useful if you're required to maintain metadata fields such as Author, Copyright, Pedigree, Publisher, ReleaseNotes, etc., that an automated tool like `pgscan` cannot generate.