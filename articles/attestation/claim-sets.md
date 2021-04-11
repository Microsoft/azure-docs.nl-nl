---
title: Azure Attestation-claimsets
description: De claimsets van Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 0d6d5a08ea85ebb666acc0336f1e1d7ec5e097da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044665"
---
# <a name="claim-sets"></a>Claimsets

Claims die worden gegenereerd in het proces van het attesten van enclaves met behulp van Microsoft Azure Attestation kunnen worden onderverdeeld in de volgende categorieën:

- **Binnenkomende claims**: de claims die zijn gegenereerd door Microsoft Azure Attestation na het parseren van het Attestation-bewijs en kunnen worden gebruikt door auteurs van beleid om autorisatie regels in een aangepast beleid te definiëren

- **Uitgaande claims**: de claims die zijn gegenereerd door Azure Attestation en opgenomen in het Attestation-token

- **Eigenschaps claims**: de claims die zijn gemaakt als uitvoer door Azure Attestation. Het bevat alle claims die eigenschappen van het Attestation-token vertegenwoordigen, zoals het coderen van het rapport, de geldigheidsduur van het rapport, enzovoort.

## <a name="incoming-claims"></a>Binnenkomende claims 

### <a name="sgx-attestation"></a>SGX-Attestation

Claims die door beleids ontwerpers moeten worden gebruikt voor het definiëren van autorisatie regels in een SGX-Attestation-beleid:

- **x-MS-SGX-is-** debuggable: een Booleaanse waarde die aangeeft of fout opsporing is ingeschakeld voor de enclave of niet
- **x-MS-SGX-product-id**: product-id-waarde van SGX enclave 
- **x-MS-SGX-mrsigner**: hex-gecodeerde waarde van het veld ' mrsigner ' van de prijs opgave
- **x-MS-SGX-mrenclave**: hex-gecodeerde waarde van het veld ' mrenclave ' van de prijs opgave
- **x-MS-SGX-svn**: beveiligings versie nummer dat is gecodeerd in de prijs opgave 

Onderstaande claims worden als afgeschaft beschouwd, maar worden volledig ondersteund en blijven in de toekomst opgenomen. Het is raadzaam om de niet-afgeschafte claim namen te gebruiken.

Afgeschafte claim | Aanbevolen claim
--- | --- 
$is: fouten opsporen | x-MS-SGX-is-fout opsporing
$product-id | x-MS-SGX-product-id
$sgx-mrsigner | x-MS-SGX-mrsigner
$sgx-mrenclave | x-MS-SGX-mrenclave
$svn | x-MS-SGX-svn

### <a name="tpm-attestation"></a>TPM-attestation

Claims die door beleids ontwerpers moeten worden gebruikt voor het definiëren van autorisatie regels in een beleid voor TPM-Attestation:

- **aikValidated**: Booleaanse waarde met informatie als het certificaat van de Attestation-identiteits sleutel (AIK) is gevalideerd of niet
- **aikPubHash**: teken reeks met de base64-(sha256-open bare AIK-sleutel in der notatie)
- **tpmVersion**: een geheel getal dat de primaire versie van de Trusted Platform Module (TPM) bevat
- **secureBootEnabled**: een Booleaanse waarde die aangeeft of beveiligd opstarten is ingeschakeld
- **iommuEnabled**: een Booleaanse waarde die aangeeft of de invoer-uitvoer geheugen beheer eenheid (IOMMU) is ingeschakeld
- **bootDebuggingDisabled**: een Booleaanse waarde die aangeeft of opstart fout opsporing is uitgeschakeld
- **notSafeMode**: een Booleaanse waarde die aangeeft of de Windows niet wordt uitgevoerd in de veilige modus
- **notWinPE**: Boolean-waarde die aangeeft of Windows niet wordt uitgevoerd in de WinPE-modus
- **vbsEnabled**: een Booleaanse waarde die aangeeft of vbs is ingeschakeld
- **vbsReportPresent**: Boolean-waarde die aangeeft of het vbs enclave-rapport beschikbaar is

### <a name="vbs-attestation"></a>VBS-Attestation

Naast de claims van het TPM-attest beleid kunnen onder claims worden gebruikt door auteurs van beleid om autorisatie regels in een VBS Attestation-beleid te definiëren.

- **enclaveAuthorId**: String-waarde met de Base64Url gecodeerde waarde van de enclave Author id: de auteur-id van de primaire module voor de enclave
- **enclaveImageId**: teken reeks waarde met de Base64Url gecodeerde waarde van de enclave-installatie kopie-id: de afbeeldings-id van de primaire module voor de enclave
- **enclaveOwnerId**: String-waarde met de Base64Url gecodeerde waarde van de enclave eigenaar-id: de id van de eigenaar van de enclave
- **enclaveFamilyId**: String-waarde met de Base64Url gecodeerde waarde van de enclave Family id. De familie-id van de primaire module voor de enclave
- **enclaveSvn**: een integer-waarde met het beveiligings versie nummer van de primaire module voor de enclave
- **enclavePlatformSvn**: een geheel getal dat het beveiligings versie nummer bevat van het platform dat als host fungeert voor de enclave
- **enclaveFlags**: de enclaveFlags-claim is een geheel getal dat vlaggen bevat waarmee het runtime beleid voor de enclave wordt beschreven

## <a name="outgoing-claims"></a>Uitgaande claims 

### <a name="common-for-all-attestation-types"></a>Gebruikelijk voor alle Attestation-typen

Azure Attestation omvat de onderstaande claims in het Attestation-token voor alle Attestation-typen. 

- **x-MS-ver**: JWT-schema versie (verwacht wordt ' 1,0 ')
- **x-MS-Attestation-type**: teken reeks waarde voor Attestation-type 
- **x-MS-Policy-hash**: hash van het Azure Attestation-evaluatie beleid berekend als BASE64URL (sha256 (UTF8 (BASE64URL) (UTF8 (Policy text))))
- **x-MS-Policy-Signer**: JSON-object met het lid ' jwk ' voor de sleutel die een klant heeft gebruikt om hun beleid te ondertekenen. Dit is van toepassing wanneer een gebruiker een ondertekend beleid uploadt

De onderstaande claim namen worden gebruikt vanuit de [specificatie IETF JWT](https://tools.ietf.org/html/rfc7519)

- **"JTI" (JWT-id) claim** -unieke id voor de JWT
- **claim ' ISS ' (verlener)** : de principal die de JWT heeft uitgegeven 
- **"iat" (uitgegeven op) claim** : de tijd waarop de JWT is uitgegeven 
- **"exp" (verval tijd) claim** verloop tijd waarna de JWT niet voor verwerking moet worden geaccepteerd
- **' NBF ' (niet vóór) claim** -niet vóór het tijdstip voordat de JWT niet moet worden geaccepteerd voor verwerking 

Onderstaande claim namen worden gebruikt vanuit de [concept specificatie](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) van de IETF-versie

- **"Nonce-claim" (nonce)** -een niet-getransformeerde directe kopie van een optionele nonce-waarde die door een client wordt verschaft 

Onderstaande claims worden als afgeschaft beschouwd, maar worden volledig ondersteund en blijven in de toekomst opgenomen. Het is raadzaam om de niet-afgeschafte claim namen te gebruiken.

Afgeschafte claim | Aanbevolen claim
--- | --- 
ver | x-MS-ver
Tee | x-MS-Attestation-type
policy_hash | x-MS-Policy-hash
maa-policyHash | x-MS-Policy-hash
policy_signer  | x-MS-Policy-Signer

### <a name="sgx-attestation"></a>SGX-Attestation 

Hieronder worden claims gegenereerd en opgenomen in het Attestation-token door de service voor SGX-Attestation.

- **x-MS-SGX-is-** debuggable: een Booleaanse waarde die aangeeft of fout opsporing is ingeschakeld voor de enclave of niet
- **x-MS-SGX-product-id**: product-id-waarde van SGX enclave 
- **x-MS-SGX-mrsigner**: hex-gecodeerde waarde van het veld ' mrsigner ' van de prijs opgave
- **x-MS-SGX-mrenclave**: hex-gecodeerde waarde van het veld ' mrenclave ' van de prijs opgave
- **x-MS-SGX-svn**: beveiligings versie nummer dat is gecodeerd in de prijs opgave 
- **x-MS-SGX-hetzij**: enclave opgeslagen gegevens die zijn ingedeeld als BASE64URL (enclave-gegevens)
- **x-MS-SGX-onderpand**: JSON-object dat het onderpand beschrijft dat wordt gebruikt om Attestation uit te voeren. De waarde voor de x-MS-SGX-onderpand claim is een genest JSON-object met de volgende sleutel/waarde-paren:
    - **qeidcertshash**: sha256 waarde van de uitgevende certificerings instantie van de aanhalings ENCLAVE (QE)
    - **qeidcrlhash**: sha256 waarde van de CRL-lijst voor het verlenen van certificaten van QE
    - **qeidhash**: sha256-waarde van de QE Identity onderpand
    - **quotehash**: sha256-waarde van de geëvalueerde prijs opgave
    - **tcbinfocertshash**: sha256-waarde van de TCB-gegevens die certificaten verlenen
    - **tcbinfocrlhash**: sha256-waarde van de TCB-gegevens die certificaten van de CRL-lijst uitgeven
    - **tcbinfohash**: sha256-waarde van de TCB info-onderpand

Onderstaande claims worden als afgeschaft beschouwd, maar worden volledig ondersteund en blijven in de toekomst opgenomen. Het is raadzaam om de niet-afgeschafte claim namen te gebruiken.

Afgeschafte claim | Aanbevolen claim
--- | --- 
$is: fouten opsporen | x-MS-SGX-is-fout opsporing
$product-id | x-MS-SGX-product-id
$sgx-mrsigner | x-MS-SGX-mrsigner
$sgx-mrenclave | x-MS-SGX-mrenclave
$svn | x-MS-SGX-svn
$maa-hetzij | x-MS-SGX-hetzij
$aas-hetzij | x-MS-SGX-hetzij
$maa-attestationcollateral | x-MS-SGX-onderpand

### <a name="tpm-and-vbs-attestation"></a>TPM en VBS-Attestation

- **cnf (bevestiging)**: de claim ' cnf ' wordt gebruikt om de bewijs-of-eigendoms sleutel te identificeren. Bevestigings claim zoals gedefinieerd in RFC 7800, bevat het open bare deel van de Attestation-sleutel enclave die wordt weer gegeven als een JSON Web Key (JWK)-object (RFC 7517)
- **rp_data (Relying Party gegevens)**: Relying Party gegevens, indien van toepassing, opgegeven in de aanvraag, die door de Relying Party als een nonce wordt gebruikt om de versheid van het rapport te garanderen. rp_data wordt alleen toegevoegd als er rp_data

## <a name="property-claims"></a>Eigenschapsclaims

### <a name="tpm-and-vbs-attestation"></a>TPM en VBS-Attestation

- **report_validity_in_minutes**: een geheel getal dat aangeeft hoe lang het token geldig is.
  - **Standaardwaarde (tijd)** : Eén dag in minuten.
  - **Maximale waarde (tijd)** : Één jaar in minuten.
- **omit_x5c**: Een Booleaanse claim die aangeeft of het certificaat dat wordt gebruikt voor het controleren van de service-authenticiteit, door Azure Attestation moet worden weggelaten. Indien true, wordt x5t toegevoegd aan het Attestation-token. Indien false, wordt x5c toegevoegd aan het Attestation-token.

## <a name="next-steps"></a>Volgende stappen
- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
