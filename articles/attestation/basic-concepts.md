---
title: Azure Attestation basisbegrippen
description: Basisbegrippen van Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 3cd7d2541cb980fc5ca6a1a9c42a430eac1ecb1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99429276"
---
# <a name="basic-concepts"></a>Basisbegrippen

Hieronder vindt u enkele basisbegrippen met betrekking tot Microsoft Azure Attestation.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON Web Token](https://jwt.io/) (JWT) is een open standaard [RFC7519](https://tools.ietf.org/html/rfc7519)-methode voor het veilig verzenden van informatie tussen partijen als een JavaScript Object Notation (JSON)-object. Deze informatie kan worden geverifieerd en vertrouwd omdat deze digitaal is ondertekend. JWTs kunnen worden ondertekend met behulp van een geheim of een openbaar/persoonlijk sleutelpaar.

## <a name="json-web-key-jwk"></a>JSON Web Key (JWK)

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) is een JSON-gegevensstructuur die een cryptografische sleutel vertegenwoordigt. Deze specificatie definieert ook een JWK Set JSON-gegevensstructuur die een set JWK’s vertegenwoordigt.

## <a name="attestation-provider"></a>Attestation-provider

Attestation-provider behoort tot de Azure-resourceprovider met de naam Microsoft.Attestation. De resourceprovider is een service-eindpunt dat Azure Attestation REST-contract voorziet en wordt geïmplementeerd met behulp van [Azure Resource Manager](../azure-resource-manager/management/overview.md). Elke Attestation-provider respecteert een specifiek, detecteerbaar beleid. Attestation-providers worden gemaakt met een standaardbeleid voor elk type Attestation (houd er rekening mee dat VBS-enclave geen standaardbeleid heeft). Zie [Voorbeelden van een Attestation-beleid](policy-examples.md) voor meer informatie over het standaardbeleid voor SGX.

### <a name="regional-shared-provider"></a>Regionale gedeelde provider

Azure Attestation biedt een regionale gedeelde provider in elke beschik bare regio. Klanten kunnen ervoor kiezen de regionale gedeelde provider voor Attestation te gebruiken of hun eigen providers te maken met aangepast beleid. De gedeelde providers zijn toegankelijk voor elke Azure AD-gebruiker en het bijbehorende beleid kan niet worden gewijzigd.

| Regio | Attestation-URI | 
|--|--|
| VS - oost | `https://sharedeus.eus.attest.azure.net` | 
| VS - west | `https://sharedwus.wus.attest.azure.net` | 
| Verenigd Koninkrijk Zuid | `https://shareduks.uks.attest.azure.net` | 
| Verenigd Koninkrijk West| `https://sharedukw.ukw.attest.azure.net  ` | 
| Canada - oost | `https://sharedcae.cae.attest.azure.net` | 
| Canada - midden | `https://sharedcac.cac.attest.azure.net` | 
| Europa - noord | `https://sharedneu.neu.attest.azure.net` | 
| Europa -west| `https://sharedweu.weu.attest.azure.net` | 
| US - oost 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| Central US | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>Attestation-aanvraag

Attestation-aanvraag is een geserialiseerd JSON-object dat is verzonden door de client-toepassing naar de Attestation-provider. Het object Request voor SGX enclave heeft twee eigenschappen: 
- "Quote" - de waarde van de eigenschap "quote" is een tekenreeks met een Base64URL-gecodeerde representatie van de Attestation-offerte
- "EnclaveHeldData" - de waarde van de eigenschap "EnclaveHeldData" is een tekenreeks met een Base64URL-gecodeerde representatie van de enclave-gegevens.

Azure Attestation valideert de opgegeven "Prijsopgave" en zorgt er vervolgens voor dat de SHA256-hash van de opgegeven enclavegegevens wordt weergegeven in de eerste 32 bytes van het veld reportData in de prijsopgave. 

## <a name="attestation-policy"></a>Attestation-beleid

Attestation-beleid wordt gebruikt voor het verwerken van het Attestation-bewijs en kan worden geconfigureerd door klanten. De kern van Azure Attestation is een beleidsengine die claims verwerkt die het bewijs vormen. Beleid wordt gebruikt om te bepalen of Azure Attestation een Attestation-token afgeeft op basis van een bewijs (of niet) en de Attester ondersteund (of niet). Als gevolg hiervan is het niet mogelijk om alle beleidsregels door te geven, waardoor er geen JWT-token wordt uitgegeven.

Als het standaardbeleid van de Attestation-provider niet voldoet aan de behoeften, kunnen klanten aangepaste beleidsregels maken in een van de regio's die worden ondersteund door Azure Attestation. Beleidsbeheer is een belangrijke functie de aan klanten door Azure Attestation wordt geleverd. Beleidsregels zijn specifiek voor het type Attestation en kunnen worden gebruikt om enclaves te identificeren of claims toe te voegen aan het uitvoertoken of claims te wijzigen in een uitvoertoken. 

Zie voor [beelden van een Attestation-beleid](policy-examples.md) voor beleids voorbeelden.

## <a name="benefits-of-policy-signing"></a>Voordelen van beleidsondertekening

Een Attestation-beleid is wat uiteindelijk bepaalt of een Attestation-token wordt uitgegeven door Azure Attestation. Beleid bepaalt ook welke claims moeten worden gegenereerd in het Attestation-token. Het is daarom van groot belang dat het beleid dat door de service wordt geëvalueerd, in feite het beleid is dat door de beheerder is geschreven, waarmee niet is geknoeid en dat niet is gewijzigd door externe entiteiten. 

Het vertrouwensmodel definieert het autorisatiemodel van de Attestation-provider om beleid te definiëren en bij te werken.  Twee modellen worden ondersteund - één op basis van Azure AD-autorisatie en één op basis van het bezit van door de klant beheerde cryptografische sleutels (aangeduid als geïsoleerd model).  Met een geïsoleerd model wordt Azure Attestation ingeschakeld om ervoor te zorgen dat het door de klant ingediende beleid niet is gewijzigd.

In geïsoleerd model maakt de beheerder een Attestation-provider die een set vertrouwde ondertekenende X.509-certificaten in een bestand opgeeft. De beheerder kan vervolgens een ondertekend beleid toevoegen aan de Attestation-provider. Tijdens de verwerking van de Attestation-aanvraag valideert Azure Attestation de handtekening van het beleid met behulp van de openbare sleutel die wordt vertegenwoordigd door de parameter "jwk" of "x5c" in de koptekst.  Azure Attestation controleert ook of de openbare sleutel in de aanvraag-header zich in de lijst met vertrouwde handtekeningcertificaten bevindt die zijn gekoppeld aan de Attestation-provider. Op deze manier kan de Relying Party (Azure Attestation) een beleid vertrouwen dat is ondertekend met de X. 509-certificaten die het kent. 

Zie [Voorbeelden van certificaat beleidsondertekenaar](policy-signer-examples.md) voor voorbeelden.

## <a name="attestation-token"></a>Attestation-token

Azure Attestation-reactie is een JSON-tekenreeks waarvan de waarde JWT bevat. De claims worden door Azure Attestation ingepakt en er wordt een ondertekende JWT gegenereerd. De ondertekeningsbewerking wordt uitgevoerd met behulp van een zelfondertekend certificaat met de objectnaam die overeenkomt met het AttestUri-element van de Attestation-provider.

De Get OpenID Connect Metadata API Get retourneert een OpenID-configuratieantwoord zoals opgegeven door het [OpenID Connect Discovery-Protocol](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). De API haalt metagegevens op over de handtekeningcertificaten die worden gebruikt door Azure Attestation.

Voorbeeld van een JWT-generatie voor een SGX-enclave:

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0"
}.[Signature]
```
Sommige van de eerder gebruikte claims worden als afgeschaft beschouwd, maar worden volledig ondersteund.  Het is raadzaam dat alle toekomstige code en hulpprogram ma's gebruikmaken van de niet-afgeschafte claim namen. Zie [Claims die zijn uitgegeven door Azure Attestation](claim-sets.md) voor meer informatie.

## <a name="encryption-of-data-at-rest"></a>Versleuteling van data-at-rest

Om klantgegevens te beschermen, blijven de gegevens van Azure Attestation in Azure Storage. Azure Storage biedt versleuteling van data-at-rest die naar datacentra worden geschreven, en ontsleutelt deze voor klanten om toegang te krijgen. Deze versleuteling vindt plaats met behulp van een door Microsoft beheerde versleutelingssleutel. 

Naast het beveiligen van gegevens in Azure Storage, maakt Azure Attestation ook gebruik van Azure Disk Encryption (ADE) voor het versleutelen van service-VM's. Voor Azure Attestation dat wordt uitgevoerd in een enclave in Azure-omgevingen met vertrouwelijke computing, wordt de ADE-extensie momenteel niet ondersteund. In dergelijke scenario's wordt het wisselbestand uitgeschakeld om te voorkomen dat gegevens worden opgeslagen in het geheugen. 

Er worden geen klantgegevens bewaard op de lokale harde schijven van het Azure Attestation-exemplaar.


## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
