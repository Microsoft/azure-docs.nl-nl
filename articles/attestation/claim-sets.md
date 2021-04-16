---
title: Azure Attestation-claimsets
description: De claimsets van Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517552"
---
# <a name="claim-sets"></a>Claimsets

Claims die worden gegenereerd in het proces van het attesten van enclaves met behulp van Microsoft Azure Attestation kunnen worden onderverdeeld in de volgende categorieën:

- **Binnenkomende claims:** de claims die worden gegenereerd door Microsoft Azure Attestation na het parseren van het attestation-bewijs en kunnen worden gebruikt door beleidsauteurs om autorisatieregels te definiëren in een aangepast beleid

- **Uitgaande claims:** de claims die worden gegenereerd door Azure Attestation en opgenomen in het Attestation-token

- **Eigenschapsclaims:** de claims die zijn gemaakt als uitvoer door Azure Attestation. Het bevat alle claims die eigenschappen van het Attestation-token vertegenwoordigen, zoals het coderen van het rapport, de geldigheidsduur van het rapport, enzovoort.

## <a name="incoming-claims"></a>Binnenkomende claims 

### <a name="sgx-attestation"></a>SGX-Attestation

Claims die door beleidsauteurs moeten worden gebruikt om autorisatieregels te definiëren in een SGX-attestation-beleid:

- **x-ms-sgx-is-debuggable:** een Booleaanse waarde die aangeeft of enclave-foutopsporing is ingeschakeld of niet.
  
  SGX-enclaves kunnen worden geladen met debugging uitgeschakeld of ingeschakeld. Wanneer de vlag is ingesteld op true in de enclave, schakelt deze functies voor het debuggen van de enclavecode in. Dit omvat de mogelijkheid om toegang te krijgen tot het geheugen van de enclave. Daarom is het raadzaam om de vlag alleen voor ontwikkelingsdoeleinden in te stellen op true. Als deze functie is ingeschakeld in een productieomgeving, blijven de beveiligingsgaranties van SGX niet behouden.
  
  Azure Attestation kunnen het Attestation-beleid gebruiken om te controleren of debuggen is uitgeschakeld voor de SGX-enclave. Zodra de beleidsregel is toegevoegd, mislukt de attestation wanneer een kwaadwillende gebruiker de ondersteuning voor foutopsporing in schakelt om toegang te krijgen tot de enclave-inhoud.

- **x-ms-sgx-product-id:** een geheel getal dat de product-id van de SGX-enclave aangeeft.

  De enclaveauteur wijst een product-id toe aan elke enclave. Met de product-id kan de enclaveauteur enclaves segmenteren die zijn ondertekend met dezelfde MRSIGNER. Door een validatieregel toe te voegen aan het Attestation-beleid, kunnen klanten controleren of ze de beoogde enclaves gebruiken. Attestation mislukt als de product-id van de enclave niet overeen komt met de waarde die is gepubliceerd door de enclaveauteur.

- **x-ms-sgx-mrsigner:** een tekenreekswaarde die de auteur van de SGX-enclave identificeert.

  MRSIGNER is de hash van de openbare sleutel van de enclaveauteur die wordt gebruikt om de binaire enclave te ondertekenen. Door MRSIGNER te valideren via een Attestation-beleid, kunnen klanten controleren of vertrouwde binaire bestanden worden uitgevoerd binnen een enclave. Wanneer de beleidsclaim niet overeen komt met de MRSIGNER van de enclaveauteur, betekent dit dat de enclave-binary niet is ondertekend door een vertrouwde bron en de attestation mislukt.
  
  Wanneer een enclaveauteur MRSIGNER uit veiligheidsoverwegingen liever roteert, moet Azure Attestation-beleid worden bijgewerkt om de nieuwe en oude MRSIGNER-waarden te ondersteunen voordat de binaire bestanden worden bijgewerkt. Anders mislukken autorisatiecontroles, wat resulteert in attestation-fouten.
  
  Attestation-beleid moet worden bijgewerkt met behulp van de onderstaande indeling. 
 
  #### <a name="before-key-rotation"></a>Vóór sleutelrotatie
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Tijdens sleutelrotatie

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Na sleutelrotatie

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mren enclavee:** een tekenreekswaarde die de code en gegevens identificeert die in het enclavegeheugen zijn geladen. 

  MREN ENCLAVEE is een van de enclavemetingen die kan worden gebruikt om de binaire enclave-bestanden te verifiëren. Het is de hash van de code die in de enclave wordt uitgevoerd. De meting verandert bij elke wijziging in de binaire enclavecode. Door MREN ENCLAVEE te valideren via een Attestation-beleid, kunnen klanten controleren of de beoogde binaire bestanden worden uitgevoerd binnen een enclave. Omdat MREN ENCLAVEE naar verwachting echter regelmatig zal veranderen met een triviale wijziging in de bestaande code, wordt het aanbevolen om binaire enclave-bestanden te controleren met behulp van MRSIGNER-validatie in een Attestation-beleid.

- **x-ms-sgx-svn:** een geheel getal dat het beveiligingsversienummer van de SGX-enclave aangeeft

  De enclaveauteur wijst een SVN (Security Version Number) toe aan elke versie van de SGX-enclave. Wanneer er een beveiligingsprobleem wordt ontdekt in de enclavecode, verhoogt de enclaveauteur de SVN-waarde na het oplossen van beveiligingsprobleem. Om interactie met onveilige enclavecode te voorkomen, kunnen klanten een validatieregel toevoegen aan het Attestation-beleid. Als de SVN van de enclavecode niet overeen komt met de versie die wordt aanbevolen door de enclaveauteur, mislukt de attestation.

Onderstaande claims worden beschouwd als afgeschaft, maar worden volledig ondersteund en zullen in de toekomst nog steeds worden opgenomen. Het is raadzaam om de niet-afgeschafte claimnamen te gebruiken.

Afgeschafte claim | Aanbevolen claim
--- | --- 
$is foutopsporingsbaar | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mren enclavee | x-ms-sgx-mren enclavee
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>TPM-attestation

Claims die door beleidsauteurs moeten worden gebruikt om autorisatieregels te definiëren in een TPM Attestation-beleid:

- **aikValidated:** Booleaanse waarde met informatie als het certificaat van de Attestation-identiteitssleutel (AIK) is gevalideerd of niet
- **aikPubHash: tekenreeks** met de base64(SHA256(openbare AIK-sleutel in DER-indeling))
- **tpmVersion:** geheel getal-waarde met de Trusted Platform Module (TPM)-hoofdversie
- **secureBootEnabled:** Booleaanse waarde om aan te geven of beveiligd opstarten is ingeschakeld
- **iommuEnabled:** Booleaanse waarde om aan te geven of Input-Output Memory Management Unit (Iommu) is ingeschakeld
- **bootDebuggingDisabled:** Booleaanse waarde om aan te geven of opstartopsporing is uitgeschakeld
- **notSafeMode:** Booleaanse waarde om aan te geven of Windows niet wordt uitgevoerd in de veilige modus
- **notWinPE:** Booleaanse waarde die aangeeft of Windows niet wordt uitgevoerd in de WinPE-modus
- **vbsEnabled:** Booleaanse waarde die aangeeft of VBS is ingeschakeld
- **vbsReportPresent:** Booleaanse waarde die aangeeft of het VBS-enclaverapport beschikbaar is

### <a name="vbs-attestation"></a>VBS-Attestation

Naast de claims van het TPM Attestation-beleid kunnen onderstaande claims worden gebruikt door beleidsauteurs om autorisatieregels te definiëren in een VBS-attestation-beleid.

- **enclaveAuthorId:** tekenreekswaarde met de base64Url-gecodeerde waarde van de enclaveauteur-id- de auteur-id van de primaire module voor de enclave
- **enclaveImageId:** tekenreekswaarde met de base64Url-gecodeerde waarde van de enclave-afbeeldings-id: de afbeeldings-id van de primaire module voor de enclave
- **enclaveOwnerId:** tekenreekswaarde met de base64Url-gecodeerde waarde van de enclave-eigenaar-id- de id van de eigenaar voor de enclave
- **enclaveFamilyId:** tekenreekswaarde met de base64Url-gecodeerde waarde van de enclave-familie-id. De familie-id van de primaire module voor de enclave
- **enclaveSvn:** geheel getal met het nummer van de beveiligingsversie van de primaire module voor de enclave
- **enclavePlatformSvn:** geheel getal met het beveiligingsversienummer van het platform dat als host voor de enclave wordt gebruikt
- **enclaveFlags:** de enclaveFlags-claim is een geheel getal met vlaggen die het runtimebeleid voor de enclave beschrijven

## <a name="outgoing-claims"></a>Uitgaande claims 

### <a name="common-for-all-attestation-types"></a>Algemeen voor alle attestation-typen

Azure Attestation bevat de onderstaande claims in het Attestation-token voor alle attestation-typen. 

- **x-ms-ver:** JWT-schemaversie (naar verwachting '1.0')
- **x-ms-attestation-type:** tekenreekswaarde die het attestation-type vertegenwoordigt 
- **x-ms-policy-hash:** Hash van Azure Attestation-evaluatiebeleid berekend als BASE64URL(SHA256(UTF8(BASE64URL(UTF8(beleidstekst)))))
- **x-ms-policy-signer: JSON-object** met een jwk-lid dat de sleutel vertegenwoordigt die een klant heeft gebruikt om het beleid te ondertekenen. Dit is van toepassing wanneer de klant een ondertekend beleid uploadt

Onderstaande claimnamen worden gebruikt vanuit [IETF JWT-specificatie](https://tools.ietf.org/html/rfc7519)

- **"jti" (JWT ID) Claim** - unieke id voor de JWT
- **"iss" (vereenigder) Claim** - de principal die de JWT heeft uitgegeven 
- **"iat" (uitgegeven op) Claim** : het tijdstip waarop de JWT is uitgegeven op 
- **"exp" (vervaltijd) Claim** - vervaltijd waarna de JWT niet geaccepteerd voor verwerking
- **"nbf" (niet vóór) Claim** - Niet vóór tijd waarvoor de JWT niet mag worden geaccepteerd voor verwerking 

Onderstaande claimnamen worden gebruikt vanuit [de conceptspecificatie van IETF-CONCEPT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce claim" (nonce)** - een niet-omgevormde directe kopie van een optionele nonce-waarde geleverd door een client 

Onderstaande claims worden beschouwd als afgeschaft, maar worden volledig ondersteund en zullen in de toekomst nog steeds worden opgenomen. Het is raadzaam om de niet-afgeschafte claimnamen te gebruiken.

Afgeschafte claim | Aanbevolen claim
--- | --- 
Ver | x-ms-ver
Tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
ingen-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>SGX-Attestation 

Onderstaande claims worden gegenereerd en opgenomen in het Attestation-token door de service voor SGX-attestation.

- **x-ms-sgx-is-debuggable:** een Booleaanse naam die aangeeft of de enclave foutopsporing al dan niet heeft ingeschakeld
- **x-ms-sgx-product-id: product-id-waarde** van de SGX-enclave 
- **x-ms-sgx-mrsigner:** hexgecodeerde waarde van het veld 'mrsigner' van de prijsopgave
- **x-ms-sgx-mren enclavee:** hexgecodeerde waarde van het veld 'mren enclavee' van de prijsopgave
- **x-ms-sgx-svn:** nummer van de beveiligingsversie dat is gecodeerd in de prijsopgave 
- **x-ms-sgx-ehd:** enclavegegevens opgemaakt als BASE64URL (enclavegegevens)
- **x-ms-sgx-objective: JSON-object** met een beschrijving van het object dat wordt gebruikt om attestation uit te voeren. De waarde voor de claim x-ms-sgx-claim claim is een genest JSON-object met de volgende sleutel-waardeparen:
    - **qeidcertshash:** SHA256-waarde van Quoting Enclave (QE)-identiteit verlenende certificaten
    - **qeidcrlhash:** SHA256 waarde van QE Identity verlenende certificaat CRL-lijst
    - **qeidhash:** SHA256-waarde van de QE Identity-waarde
    - **quotehash:** SHA256-waarde van de geëvalueerde prijsopgave
    - **tcbinfocertshash:** SHA256-waarde van de TCB-info verlenende certificaten
    - **tcbinfocrlhash:** SHA256-waarde van de TCB Info verlenende certificaat CRL-lijst
    - **tcbinfohash:** SHA256-waarde van de TCB Info-waarden

Onderstaande claims worden beschouwd als afgeschaft, maar worden volledig ondersteund en zullen in de toekomst nog steeds worden opgenomen. Het is raadzaam om de niet-afgeschafte claimnamen te gebruiken.

Afgeschafte claim | Aanbevolen claim
--- | --- 
$is foutopsporingsbaar | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mren enclavee | x-ms-sgx-mren enclavee
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-gaan

### <a name="tpm-and-vbs-attestation"></a>TPM- en VBS-attestation

- **cnf (bevestiging)**: de "cnf"-claim wordt gebruikt om de sleutel voor het bewijs van eigendom te identificeren. Bevestigingsclaim zoals gedefinieerd in RFC 7800, bevat het openbare deel van de attested enclave-sleutel die wordt weergegeven als een JSON Web Key-object (JWK) (RFC 7517)
- **rp_data (relying party-gegevens)**: Relying Party-gegevens, indien van derden, opgegeven in de aanvraag, die door de relying party worden gebruikt als een nonce om de nieuwheid van het rapport te garanderen. rp_data wordt alleen toegevoegd als er sprake is van rp_data

## <a name="property-claims"></a>Eigenschapsclaims

### <a name="tpm-and-vbs-attestation"></a>TPM- en VBS-attestation

- **report_validity_in_minutes:** een claim met gehele getallen om aan te geven hoelang het token geldig is.
  - **Standaardwaarde (tijd)** : Eén dag in minuten.
  - **Maximale waarde (tijd)** : Één jaar in minuten.
- **omit_x5c**: Een Booleaanse claim die aangeeft of het certificaat dat wordt gebruikt voor het controleren van de service-authenticiteit, door Azure Attestation moet worden weggelaten. Indien true, wordt x5t toegevoegd aan het Attestation-token. Indien false, wordt x5c toegevoegd aan het Attestation-token.

## <a name="next-steps"></a>Volgende stappen
- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
