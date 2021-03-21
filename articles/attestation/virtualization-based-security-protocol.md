---
title: VBS-Protocol (virtualisatie-based Security) voor Azure Attestation
description: VBS-attestatieprotocol
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507876"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>Trusted Platform Module (TPM) en enclave Attestation-Protocol (virtualisatie Security) 

Microsoft Azure Attestation om een sterke beveiligings garantie te bieden, is afhankelijk van het controleren van een vertrouwens keten van een basis van vertrouwen (TPM) tot het starten van de Hyper Visor en beveiligde kernel. Ter verkrijging van deze Azure-Attestation moet de opstart status van de machine worden verzorgd voordat er een vertrouwens relatie kan worden ingesteld in de beveiligde enclave. De binaire bestanden van het besturings systeem, Hyper Visor en beveiligde kernel moeten worden ondertekend door de juiste officiële micro soft-instanties en op een veilige manier worden geconfigureerd. Zodra er een vertrouwens relatie is tussen de Trusted Platform Module (TPM) en de status van de Hyper Visor, we kunnen de enclave IDKs van virtualisatie op basis van het gemeten opstart logboek vertrouwen, waardoor we kunnen valideren dat een sleutel paar is gegenereerd door de enclave en licht een Attestation-rapport dat een vertrouwens relatie in die sleutel verbindt en andere claims bevat, zoals het beveiligings niveau en de eigenschappen van de opstart Attestation. 

VBS enclaves vereist een TPM om de meting te bieden om de Security Foundation te valideren. VBS enclaves worden door het TPM-eind punt verklaard met een toevoeging aan het object Request in het protocol. 

## <a name="protocol-messages"></a>Protocolberichten

### <a name="init-message"></a>Init-bericht

#### <a name="direction"></a>Richting

Client-> Azure-Attestation

#### <a name="payload"></a>Nettolading

```
{ 
  "type": "aikcert" 
} 
```

"type" (ASCII-teken reeks): vertegenwoordigt het aangevraagde type Attestation. Op dit moment wordt alleen ' aikcert ' ondersteund.

### <a name="challenge-message"></a>Vraag bericht

#### <a name="direction"></a>Richting

Azure-Attestation->-client

#### <a name="payload"></a>Nettolading

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**Challenge** (BASE64URL (octetten)): wille keurige waarde die door de service is uitgegeven.

**service_context** (BASE64URL (octetten)): ondoorzichtige context gemaakt door de service.


### <a name="request-message"></a>Aanvraag bericht

#### <a name="direction"></a>Richting

Client-> Azure-Attestation 

#### <a name="payload"></a>Nettolading

```
{
  "request": "<JWS>"
}
```

**Request** (JWS): de aanvraag bestaat uit een JWS-structuur (JSON Web Signature). De JWS beveiligde header en de JWS-nettolading worden hieronder weer gegeven. Net als bij een JWS-structuur bestaat de uiteindelijke waarde uit:

BASE64URL (UTF8 (JWS beveiligde header)) | | '.' ||

BASE64URL (JWS Payload) | | '.' ||

BASE64URL (JWS-hand tekening)

##### <a name="jws-protected-header"></a>Beveiligde header JWS

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS-nettolading

De JWS-payload kan van het type Basic of VBS zijn. Basic wordt gebruikt wanneer Attestation-bewijzen geen VBS-gegevens bevatten. 

Voor beeld van alleen TPM: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM + VBS enclave-voor beeld: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI): Relying Party-id. Gebruikt door de service in de berekening van de claim van de machine-ID

**rp_data** (BASE64URL (octetten)): ondoorzichtige gegevens die door de Relying Party worden door gegeven. Dit wordt gewoonlijk door de Relying Party als nonce gebruikt om de versheid van het rapport te garanderen

**Challenge** (BASE64URL (octetten)): wille keurige waarde die door de service is uitgegeven

**tpm_att_data**: aan TPM gerelateerde Attestation-gegevens

- **srtm_boot_log (BASE64URL (octetten))**: SRTM-opstart logboeken zoals opgehaald door functie Tbsi_Get_TCG_Log_Ex met logboek type = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (octetten))**: SRTM hervat het logboek als opgehaald door de functie Tbsi_Get_TCG_Log_Ex met logboek type = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (octetten))**: drtm-opstart logboeken zoals opgehaald door functie Tbsi_Get_TCG_Log_Ex met logboek type = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (octetten))**: drtm hervat het logboek als opgehaald door de functie Tbsi_Get_TCG_Log_Ex met logboek type = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (octetten))**: het X. 509-certificaat voor de AIK dat wordt geretourneerd door de functie NCryptGetProperty met eigenschap = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: het open bare deel van de AIK dat wordt weer gegeven als een JSON Web Key-object (JWK) (RFC 7517)

- **current_claim (BASE64URL (octetten))**: de Attestation-claim voor de huidige PCR-status die is geretourneerd door de functie NCryptCreateClaim met dwClaimType = NCRYPT_CLAIM_PLATFORM en para meter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ingesteld op include all PCRs. De Challenge die door de service wordt verzonden, moet ook worden gebruikt in de berekening van deze claim

- **boot_claim (BASE64URL (octetten))**: de Attestation-claim voor de PCR-status bij het opstarten zoals geretourneerd door de functie NCryptCreateClaim met dwClaimType = NCRYPT_CLAIM_PLATFORM en para meter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ingesteld op include all PCRs

**vsm_report**   (BASE64URL (OCTETten)): het VBS enclave-Attestation-rapport dat wordt geretourneerd door de functie EnclaveGetAttestationReport. De para meter EnclaveData moet de SHA-512-hash zijn van de waarde van report_signed (inclusief de accolades openen en sluiten). De invoer van de hash-functie is UTF8 (report_signed)

**attest_key**: het open bare deel van de enclave-sleutel wordt weer gegeven als een JSON Web Key (JWK)-object (RFC 7517)

**custom_claims**: matrix met aangepaste enclave-claims die worden verzonden naar de service en die door het beleid kunnen worden geëvalueerd. De claim

- **naam (teken reeks)**: naam van de claim. Deze naam wordt toegevoegd aan een URL die wordt bepaald door de Attestation-service (om conflicten te voor komen) en de aaneengeschakelde teken reeks wordt het type van de claim die in het beleid kan worden gebruikt

- **waarde (teken reeks)**: waarde van de claim

- **value_type (teken reeks)**: gegevens type van de claim waarde

**service_context** (BASE64URL (octetten)): ondoorzichtige context gemaakt door de service.

### <a name="report-message"></a>Rapport bericht

#### <a name="direction"></a>Richting

Azure-Attestation->-client

#### <a name="payload"></a>Nettolading

```
{
  "report": "<JWT>"
}
```

**rapport** (JWT): het Attestation-rapport in JSON Web token (JWT)-indeling (RFC 7519).

## <a name="next-steps"></a>Volgende stappen

- [Azure Attestation-werk stroom](workflow.md)
