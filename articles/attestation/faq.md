---
title: Veelgestelde vragen
description: Antwoorden op veelgestelde vragen over Microsoft Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89236645"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Veelgestelde vragen over Microsoft Azure-Attestation

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over [Azure Attestation](overview.md).

Als uw Azure-probleem niet in dit artikel wordt behandeld, kunt u ook een Azure-ondersteunings aanvraag indienen op de [ondersteunings pagina van Azure](https://azure.microsoft.com/support/options/).

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Wat is Azure PCK cache service en de bijbehorende rol in enclave-Attestation

De Azure PCK-cache service definieert de Azure-beveiligings basislijn voor de knoop punten [Azure vertrouwelijk Computing (ACC)](../confidential-computing/overview.md) van Intel en slaat de gegevens op in het cache geheugen. De gegevens in de cache worden verder gebruikt door Azure-Attestation in het valideren van vertrouwde uitvoerings omgevingen (TEEs).  

Azure PCK-cache service:
   - Biedt hoge Beschik baarheid 
   - Vermindert afhankelijkheden op extern gehoste services en Internet connectiviteit.
   - Haalt de nieuwste versies van Intel-certificaten, Crl's, informatie over vertrouwde Computing Base (TCB) en de enclave-identiteit van de ACCe knoop punten van Intel op. De service bevestigt daarom dat de Azure-beveiligings basislijn wordt verwezen door Azure Attestation tijdens het valideren van de TEEs, waardoor Attestation-fouten aanzienlijk worden verkleind als gevolg van invalidatie of het intrekken van Intel-certificaten  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Wordt SGX-Attestation ondersteund door Azure Attestation in niet-Azure-omgevingen

Azure Attestation is afhankelijk van de beveiligings basislijn die wordt vermeld door de Azure PCK-cache service om de TEEs te valideren. De Azure PCK-cache service is momenteel alleen bedoeld voor het ondersteunen van alleen Azure-knoop punten die vertrouwelijk worden computing. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Welke validaties worden uitgevoerd met Azure Attestation voor de attesting van SGX enclaves

Azure Attestation is een uniform Framework voor het extern afstellen van verschillende soorten TEEs. Azure-Attestation:

   - Valideert of de vertrouwde basis van een ondertekende enclave-offerte tot Intel behoort.
   - Valideert of de enclave-offerte voldoet aan de Azure-beveiligings basislijn zoals gedefinieerd door de Azure PCK-cache service.
   - Valideert of de SHA256-hash van enclave-bewaarde gegevens (hetzij) in het object Attestation-aanvraag overeenkomt met het eerste 32 bytes van het veld reportData in de enclave-offerte.
   - Hiermee kunnen klanten een Attestation-provider maken en een aangepast beleid configureren. Naast de bovenstaande validaties evalueert Azure attestation de enclave quote op basis van het beleid. Beleid definieert autorisatie regels voor de enclave en dicteer ook uitgifte regels voor het genereren van het Attestation-token. Om te bevestigen of de beoogde software in een enclave wordt uitgevoerd, kunnen klanten autorisatie regels toevoegen om te controleren of de velden **mrsigner** en **mrenclave** in de enclave-offerte overeenkomen met de waarden van de binaire bestanden van de klant.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Hoe kan een verificateur het onderpand verkrijgen voor SGX-Attestation dat wordt ondersteund door Azure Attestation

Over het algemeen, voor de Attestation-modellen met Intel als basis van de vertrouwens relatie, besprekingen de Attestation-client naar enclave-Api's om de enclave-bewijzen op te halen. Enclave Api's roepen intern de Intel PCK cache-service aan om Intel-certificaten op te halen van het knoop punt dat moet worden verklaard. De certificaten worden gebruikt voor het ondertekenen van de enclave-bewijzen, waardoor er een extern te verklaren onderpand wordt gegenereerd.  

Hetzelfde proces kan worden geïmplementeerd voor Azure Attestation. Als u echter de voor delen van de Azure PCK cache-service wilt gebruiken, is het raadzaam om de [Azure DCAP-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.DCAP)te installeren nadat u de virtuele machine hebt geïnstalleerd. Op basis van de overeenkomst met Intel, wanneer de Azure DCAP-bibliotheek is geïnstalleerd, worden de aanvragen voor het genereren van enclave-gegevens omgeleid van de Intel PCK caching-service naar de Azure PCK-cache service. De Azure DCAP-bibliotheek wordt ondersteund in omgevingen die zijn gebaseerd op Windows en Linux.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Naar Azure Attestation van andere Attestation-modellen verplaatsen

- Installeer de Azure DCAP-bibliotheek ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) na de installatie van de virtuele machine van Azure vertrouwelijk computing om gebruik te maken van de voor delen van de Azure PCK-cache service.
- Er moet een externe Attestation-client worden gemaakt waarmee het enclave-bewijs materiaal kan worden opgehaald en aanvragen kunnen worden verzonden naar Azure-Attestation. Zie [code voorbeelden](/samples/browse/?expanded=azure&terms=attestation) voor referentie 
- Attestation-aanvragen kunnen worden verzonden naar het REST API-eind punt van standaard providers of aangepaste Attestation-providers 
- Azure Attestation-Api's worden beveiligd door Azure AD-verificatie. De client die Attestation-Api's aanroept, moet daarom een geldig Azure AD-toegangs token kunnen verkrijgen en door geven in de Attestation-aanvraag 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Hoe kan de Relying Party de integriteit van het Attestation-token verifiëren

Attestation-token dat door Azure Attestation is gegenereerd, is ondertekend met een zelfondertekend certificaat. De certificaten worden weer gegeven via een [OpenID Connect-eind punt voor meta gegevens](/rest/api/attestation/metadataconfiguration/get). Relying Party kan de handtekening certificaten ophalen van dit eind punt en handtekening verificatie uitvoeren voor het Attestation-token. De geldigheids duur van het Attestation-token is 8 uur. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Het certificaat identificeren dat moet worden gebruikt voor handtekening verificatie vanuit het OpenID Connect-eind punt voor meta gegevens

Meerdere certificaten die in het OpenID Connect-eind punt voor meta gegevens worden weer gegeven, komen overeen met verschillende use cases (bijvoorbeeld SGX Attestation) die worden ondersteund door Azure Attestation. Volgens de standaarden die zijn opgegeven door [RFC 7515](https://tools.ietf.org/html/rfc7515), moet het certificaat met de sleutel-id (Kid) die overeenkomt met de *Kid* -para meter in de header van het Attestation-token worden gebruikt voor handtekening verificatie. Als er geen overeenkomende **Kid** wordt gevonden, moet u alle certificaten proberen die worden weer gegeven door het OpenID Connect-eind punt voor meta gegevens.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>Is het mogelijk om met de Relying Party geheimen te delen met de gevalideerde TEEs (Trusted Execution Environment)

De open bare sleutel die in een enclave wordt gegenereerd, kan worden weer gegeven in de eigenschap Enclaveing data (hetzij) van het object Attestation-aanvraag dat door de client naar Azure Attestation wordt verzonden. Nadat u hebt gecontroleerd of de SHA256-hash van hetzij wordt weer gegeven in het veld reportData van de offerte, bevat Azure Attestation hetzij in het Attestation-token. Relying Party kan de hetzij van het geverifieerde Attestation-antwoord gebruiken om de geheimen te versleutelen en te delen met de enclave. Zie [basis beginselen van Azure Attestation](basic-concepts.md) (Engelstalig) voor meer informatie.
