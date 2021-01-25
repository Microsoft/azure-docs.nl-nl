---
title: Azure Attestation-werkstroom
description: De werkstroom van Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 27a97ceb2ca9a7b58df7200930e4e47d89c9ae89
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762205"
---
# <a name="workflow"></a>Werkstroom

Microsoft Azure Attestation ontvangt bewijs van enclaves en evalueert de gegevens op basis van de basislijn van de Azure-beveiliging en configureerbaar beleid. Bij een geslaagde verificatie genereert Azure Attestation een Attestation-token om de betrouwbaarheid van de enclave te bevestigen.

De volgende actoren zijn betrokken bij een Azure Attestation-werkstroom:

- **Relying party**: Het onderdeel dat afhankelijk is van Azure Attestation om de geldigheid van de enclave te controleren. 
- **Client**: Het onderdeel dat gegevens van een enclave verzamelt en aanvragen naar Azure Attestation verzendt. 
- **Azure Attestation**: Het onderdeel dat enclavebewijs van de client accepteert, valideert en het Attestation-token naar de client retourneert


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>Validatiewerkstroom voor de Intel® SGX-enclave (Software Guard Extensions)

Dit zijn de algemene stappen in een typische attestation-werkstroom voor een SGX-enclave (met Azure Attestation):

1. Client verzamelt bewijs van een enclave. Bewijs is informatie over de enclave-omgeving en de clientbibliotheek die in de enclave wordt uitgevoerd.
1. De client heeft een URL die verwijst naar een exemplaar van Azure Attestation. De client verzendt bewijs naar Azure Attestation. De exacte gegevens die naar de provider worden verzonden, zijn afhankelijk van het type enclave.
1. Azure Attestation valideert de verzonden informatie en evalueert deze op basis van het geconfigureerde beleid. Als de verificatie slaagt, wordt door Azure Attestation een Attestation-token uitgegeven en geretourneerd naar de client. Als deze stap mislukt, meldt Azure Attestation een fout aan de client. 
1. De client verzendt het Attestation-token naar Relying Party. Relying Party roept het eindpunt van de metagegevens van de openbare sleutel aan om handtekeningcertificaten op te halen. Relying Party verifieert vervolgens de handtekening van het Attestation-token en controleert de betrouwbaarheid van de enclave. 

![Validatiestroom voor SGX-enclave](./media/sgx-validation-flow.png)

> [!Note]
> Wanneer u Attestation-aanvragen verzendt in de API-versie [2018-09-01-preview](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview), moet de client bewijzen verzenden naar Azure Attestation samen met het Azure AD-toegangstoken.

## <a name="trusted-platform-module-tpm-enclave-validation-work-flow"></a>Werk stroom voor enclave-validatie van Trusted Platform Module (TPM)

Dit zijn de algemene stappen in een typische TPM enclave-Attestation-werk stroom (met behulp van Azure Attestation):

1.  Bij het opstarten van het apparaat/platform kunnen diverse opstart-en opstart Services gebeurtenissen meten die worden ondersteund door de TPM en veilig worden opgeslagen (TCG-logboek).
2.  De client verzamelt de TCG-logboeken van het apparaat en de TPM-offerte, waarmee de gegevens voor Attestation worden uitgevoerd.
3.  De client heeft een URL die verwijst naar een exemplaar van Azure Attestation. De client verzendt bewijs naar Azure Attestation. De exacte gegevens die naar de provider worden verzonden, zijn afhankelijk van het platform.
4.  Azure Attestation valideert de verzonden informatie en evalueert deze op basis van het geconfigureerde beleid. Als de verificatie slaagt, wordt door Azure Attestation een Attestation-token uitgegeven en geretourneerd naar de client. Als deze stap mislukt, meldt Azure Attestation een fout aan de client. De communicatie tussen de client en de Attestation-service wordt bepaald door het Azure Attestation TPM-protocol.
5.  De client verzendt vervolgens het Attestation-token naar Relying Party. Relying Party roept het eindpunt van de metagegevens van de openbare sleutel aan om handtekeningcertificaten op te halen. De Relying Party controleert vervolgens de hand tekening van het Attestation-token en zorgt ervoor dat de platformen betrouwbaar zijn.

![TPM-validatie stroom](./media/tpm-validation-flow.png)

## <a name="next-steps"></a>Volgende stappen
- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
