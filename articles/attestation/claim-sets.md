---
title: Azure Attestation-claimsets
description: De claimsets van Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 23bcfcb92a7fa642e111a67bf92c1306a606bb2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704800"
---
# <a name="claim-sets"></a>Claimsets

Claims die worden gegenereerd in het proces van het attesten van enclaves met behulp van Microsoft Azure Attestation kunnen worden onderverdeeld in de volgende categorieën:

- **Binnenkomende claims**: de claims die zijn gegenereerd door Microsoft Azure Attestation na het parseren van het Attestation-bewijs en kunnen worden gebruikt door auteurs van beleid om autorisatie regels in een aangepast beleid te definiëren

- **Uitgaande claims**: de claims die zijn gegenereerd door Azure Attestation en bevat alle claims die in het Attestation-token eindigen

- **Eigenschaps claims**: de claims die zijn gemaakt als uitvoer door Azure Attestation. Het bevat alle claims die eigenschappen van het Attestation-token vertegenwoordigen, zoals het coderen van het rapport, de geldigheidsduur van het rapport, enzovoort.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Algemene binnenkomende claims voor alle Attestation-typen

Onderstaande claims worden gegenereerd door Azure Attestation en kunnen worden gebruikt door auteurs van beleid om autorisatie regels te definiëren in een aangepast beleid voor alle Attestation-typen.

- **x-MS-ver**: JWT-schema versie (verwacht wordt ' 1,0 ')
- **x-MS-Attestation-type**: teken reeks waarde voor Attestation-type 
- **x-MS-Policy-hash**: hash van het Azure Attestation-evaluatie beleid berekend als BASE64URL (sha256 (UTF8 (BASE64URL) (UTF8 (Policy text))))
- **x-MS-Policy-Signer**: JSON-object met het lid ' jwk ' voor de sleutel van een klant die is gebruikt voor het ondertekenen van het beleid, wanneer de klant een ondertekend beleid uploadt

Onderstaande claims worden als afgeschaft beschouwd, maar worden volledig ondersteund. Het is raadzaam om de niet-afgeschafte claim namen te gebruiken.

Afgeschafte claim | Aanbevolen claim 
--- | --- 
ver | x-MS-ver
Tee | x-MS-Attestation-type
maa-policyHash | x-MS-Policy-hash
policy_hash | x-MS-Policy-hash
policy_signer | x-MS-Policy-Signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Veelvoorkomende uitgaande claims voor alle Attestation-typen

Onder claims zijn opgenomen in het Attestation-token voor alle Attestation-typen door de service.

Bron: zoals gedefinieerd door [IETF JWT](https://tools.ietf.org/html/rfc7519)

- **Claim ' JTI ' (JWT-ID)**
- **Claim ' ISS ' (verlener)**
- **Claim ' iat ' (uitgegeven op)**
- **Claim ' exp ' (verval tijd)**
- **Claim ' NBF ' (niet voor)**

Bron: zoals gedefinieerd door de [IETF-eten](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **Nonce-claim (nonce)**

Onder claims worden standaard opgenomen in het Attestation-token op basis van de binnenkomende claims:

- **x-MS-ver**: JWT-schema versie (verwacht wordt ' 1,0 ')
- **x-MS-Attestation-type**: teken reeks waarde voor Attestation-type 
- **x-MS-Policy-hash**: teken reeks waarde met SHA256 hash van de beleids tekst berekend door BASE64URL (sha256 (UTF8 (BASE64URL)))
- **x-MS-Policy-Signer**: bevat een JWK met de open bare sleutel of de certificaat keten die aanwezig is in de ondertekende beleids header. x-MS-Policy-Signer wordt alleen toegevoegd als het beleid is ondertekend

## <a name="claims-specific-to-sgx-enclaves"></a>Claims die specifiek zijn voor SGX-enclaves

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Binnenkomende claims die specifiek zijn voor SGX-Attestation

Onderstaande claims worden gegenereerd door Azure Attestation en kunnen worden gebruikt door auteurs van beleid om autorisatie regels te definiëren in een aangepast beleid voor SGX-Attestation.

- **x-MS-SGX-is-** debuggable: een Booleaanse waarde die aangeeft of fout opsporing is ingeschakeld voor de enclave of niet
- **x-MS-SGX-product-id**
- **x-MS-SGX-mrsigner**: hex-gecodeerde waarde van het veld ' mrsigner ' van de prijs opgave
- **x-MS-SGX-mrenclave**: hex-gecodeerde waarde van het veld ' mrenclave ' van de prijs opgave
- **x-MS-SGX-svn**: beveiligings versie nummer dat is gecodeerd in de prijs opgave 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Uitgaande claims die specifiek zijn voor SGX-Attestation

Hieronder worden claims gegenereerd en opgenomen in het Attestation-token door de service voor SGX-Attestation.

- **x-MS-SGX-is-** debuggable: een Booleaanse waarde die aangeeft of fout opsporing is ingeschakeld voor de enclave of niet
- **x-MS-SGX-product-id**
- **x-MS-SGX-mrsigner**: hex-gecodeerde waarde van het veld ' mrsigner ' van de prijs opgave
- **x-MS-SGX-mrenclave**: hex-gecodeerde waarde van het veld ' mrenclave ' van de prijs opgave
- **x-MS-SGX-svn**: beveiligings versie nummer dat is gecodeerd in de prijs opgave 
- **x-MS-SGX-hetzij**: enclave opgeslagen gegevens die zijn ingedeeld als BASE64URL (enclave-gegevens)
- **x-MS-SGX-onderpand**: JSON-object dat het onderpand beschrijft dat wordt gebruikt om Attestation uit te voeren. De waarde voor de x-MS-SGX-onderpand claim is een genest JSON-object met de volgende sleutel/waarde-paren:
    - **qeidcertshash**: sha256-waarde van QE Identity-certificaten
    - **qeidcrlhash**: sha256 waarde van de CRL-lijst voor het verlenen van certificaten van QE
    - **qeidhash**: sha256-waarde van de QE Identity onderpand
    - **quotehash**: sha256-waarde van de geëvalueerde prijs opgave
    - **tcbinfocertshash**: sha256-waarde van de TCB-gegevens die certificaten verlenen
    - **tcbinfocrlhash**: sha256-waarde van de TCB-gegevens die certificaten van de CRL-lijst uitgeven
    - **tcbinfohash**: JSON-object dat het onderpand beschrijft dat is gebruikt om Attestation uit te voeren

Onderstaande claims worden als afgeschaft beschouwd, maar worden volledig ondersteund en blijven in de toekomst opgenomen. Het is raadzaam om de niet-afgeschafte claim namen te gebruiken.

Afgeschafte claim | Aanbevolen claim
--- | --- 
$is: fouten opsporen | x-MS-SGX-is-fout opsporing
$sgx-mrsigner | x-MS-SGX-mrsigner
$sgx-mrenclave | x-MS-SGX-mrenclave
$product-id | x-MS-SGX-product-id
$svn | x-MS-SGX-svn
$tee | x-MS-Attestation-type
Maa-hetzij | x-MS-SGX-hetzij
aas-hetzij | x-MS-SGX-hetzij
maa-attestationcollateral | x-MS-SGX-onderpand

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>Claims die specifiek zijn voor Trusted Platform Module (TPM)/VBS-Attestation

### <a name="incoming-claims-for-tpm-attestation"></a>Binnenkomende claims voor TPM-Attestation

Claims die zijn uitgegeven door Azure Attestation voor TPM-Attestation. De beschik baarheid van de claims is afhankelijk van het bewijs materiaal dat is verstrekt voor Attestation.

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

### <a name="incoming-claims-for-vbs-attestation"></a>Binnenkomende claims voor VBS-Attestation

Claims die zijn uitgegeven door Azure Attestation voor VBS Attestation, zijn een aanvulling op de claims die beschikbaar worden gesteld voor TPM-Attestation. De beschik baarheid van de claims is afhankelijk van het bewijs materiaal dat is verstrekt voor Attestation.

- **enclaveAuthorId**: String-waarde met de Base64Url gecodeerde waarde van de enclave Author id: de auteur-id van de primaire module voor de enclave
- **enclaveImageId**: teken reeks waarde met de Base64Url gecodeerde waarde van de enclave-installatie kopie-id: de afbeeldings-id van de primaire module voor de enclave
- **enclaveOwnerId**: String-waarde met de Base64Url gecodeerde waarde van de enclave eigenaar-id: de id van de eigenaar van de enclave
- **enclaveFamilyId**: String-waarde met de Base64Url gecodeerde waarde van de enclave Family id. De familie-id van de primaire module voor de enclave
- **enclaveSvn**: een integer-waarde met het beveiligings versie nummer van de primaire module voor de enclave
- **enclavePlatformSvn**: een geheel getal dat het beveiligings versie nummer bevat van het platform dat als host fungeert voor de enclave
- **enclaveFlags**: de enclaveFlags-claim is een geheel getal dat vlaggen bevat waarmee het runtime beleid voor de enclave wordt beschreven

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>Uitgaande claims die specifiek zijn voor TPM en VBS-Attestation

- **cnf (bevestiging)**: de claim ' cnf ' wordt gebruikt om de bewijs-of-eigendoms sleutel te identificeren. Bevestigings claim zoals gedefinieerd in RFC 7800, bevat het open bare deel van de Attestation-sleutel enclave die wordt weer gegeven als een JSON Web Key (JWK)-object (RFC 7517)
- **rp_data (Relying Party gegevens)**: Relying Party gegevens, indien van toepassing, opgegeven in de aanvraag, die door de Relying Party als een nonce wordt gebruikt om de versheid van het rapport te garanderen. rp_data wordt alleen toegevoegd als er rp_data

### <a name="property-claims"></a>Eigenschapsclaims

- **report_validity_in_minutes**: Een geheel getal-claim die aangeeft hoe lang het token geldig is.
  - **Standaardwaarde (tijd)** : Eén dag in minuten.
  - **Maximale waarde (tijd)** : Één jaar in minuten.
- **omit_x5c**: Een Booleaanse claim die aangeeft of het certificaat dat wordt gebruikt voor het controleren van de service-authenticiteit, door Azure Attestation moet worden weggelaten. Indien true, wordt x5t toegevoegd aan het Attestation-token. Indien false, wordt x5c toegevoegd aan het Attestation-token.

## <a name="next-steps"></a>Volgende stappen
- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
