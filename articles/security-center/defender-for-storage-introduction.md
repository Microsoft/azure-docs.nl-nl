---
title: Azure Defender for Storage - de voordelen en functies
description: Kom meer te weten over de voordelen en functies van Azure Defender for Storage.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eb1635cec2b0bcf7f2c13101b2aeab25a869dc66
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558528"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Inleiding tot Azure Defender for Storage

**Azure Defender voor opslag** is een beveiligingsintelligentielaag van Azure voor de detectie van ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of misbruik te maken van uw opslagaccounts. Er wordt gebruikgemaakt van de geavanceerde mogelijkheden van beveiligings-AI en [Microsoft-bedreigingsinformatie](https://go.microsoft.com/fwlink/?linkid=2128684) om contextuele beveiligingswaarschuwingen en aanbevelingen te bieden.

Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. Deze waarschuwingen zijn geïntegreerd met Azure Security Center en worden ook via e-mail verzonden naar abonnementsbeheerders. Ze bevatten informatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|Voor **Azure Defender for Storage** gelden de prijzen op de [pagina Prijzen](security-center-pricing.md)|
|Beveiligde opslagtypen:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nee](./media/icons/no-icon.png) China Gov, Other Gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Wat zijn de voordelen van Azure Defender for Storage?

Azure Defender for Storage biedt:

- **Systeemeigen Azure-beveiliging**: in Defender for Storage kunt u gegevens die zijn opgeslagen in Azure Blob, Azure Files, en Data Lake, met slechts één muisklik beveiligen. Als systeemeigen Azure-service biedt Defender for Storage een centrale beveiliging voor alle gegevensactiva die worden beheerd in Azure, en is het geïntegreerd met andere beveiligingsservices in Azure, zoals Azure Sentinel.
- **Suite voor geavanceerd detecteren**: detecties in Defender for Storage worden mogelijk gemaakt met Bedreigingsinformatie van Microsoft, en bieden bescherming tegen de belangrijkste opslagbedreigingen, zoals anonieme toegang, gecompromitteerde referenties, social engineering, misbruik van machtigingen, en schadelijke inhoud.
- **Antwoorden op schaal**: met de geautomatiseerde hulpprogramma's van Security Center kunt u gemakkelijker reageren op geïdentificeerde bedreigingen en deze voorkomen. Meer informatie over [Geautomatiseerde antwoorden op Security Center-triggers](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Overzicht op hoog niveau van de functies van Azure Defender for Storage":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Wat voor soort waarschuwingen biedt Azure Defender for Storage?

Beveiligingswaarschuwingen worden geactiveerd in het geval van:

- **Patronen in verdachte toegang**, zoals toegang vanaf een Tor-afsluitknooppunt of een IP-adres dat door Microsoft-bedreigingsinformatie als verdacht wordt beschouwd
- **Verdachte activiteiten**, zoals afwijkende gegevensextractie of een ongebruikelijke wijziging van toegangsmachtigingen
- **Uploaden van schadelijke inhoud**, zoals mogelijke malware-bestanden (op basis van hashreputatieanalyse) of het hosten van phishing-inhoud

Waarschuwingen bevatten details over het incident waardoor ze zijn geactiveerd evenals aanbevelingen voor het onderzoeken en oplossen van bedreigingen. Waarschuwingen kunnen worden geëxporteerd naar Azure Sentinel, een SIEM-hulpprogramma van derden of een ander extern hulpprogramma.

> [!TIP]
> Het is een best practice om [Azure Defender voor opslag te configureren](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) op abonnementsniveau, maar u kunt het ook [configureren voor afzonderlijke opslagaccounts](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Wat is hashreputatieanalyse voor malware?

Om te bepalen of een geüpload bestand verdacht is, maakt Azure Defender for Storage gebruik van hashreputatieanalyse, die door [bedreigingsinformatie van Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) wordt ondersteund. De bedreigingsbeschermingshulpprogramma's scannen de geüploade bestanden niet, maar onderzoeken de opslaglogboeken en vergelijken de hashes van recent geüploade bestanden met die van bekende virussen, Trojaanse paarden, spyware en ransomware. 

Wanneer wordt vermoed dat een bestand malware bevat, wordt in Security Center een waarschuwing weergegeven en kan Security Center de opslageigenaar e-mailen om goedkeuring te vragen om het verdachte bestand te verwijderen. Als u de automatische verwijdering wilt instellen van bestanden die volgens hashreputatieanalyse malware bevatten, implementeert u een [werkstroomautomatisering om waarschuwingen te activeren voor 'malware die mogelijk naar een opslagaccount is geüpload'](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Als u de bedreigingsbeschermingsmogelijkheden van Security Center wilt inschakelen, moet u Azure Defender inschakelen voor het abonnement dat de van toepassing zijnde werkbelastingen bevat.
>
> U kunt **Azure Defender for Storage** inschakelen op abonnementsniveau of op resourceniveau.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Een test waarschuwing activeren voor Azure Defender voor opslag

Als u de beveiligings waarschuwingen van Azure Defender wilt testen voor opslag in uw omgeving, genereert u de waarschuwing ' toegang vanaf een Tor-eind knooppunt naar een opslag account ' door de volgende stappen uit te voeren:

1. Open een opslag account met Azure Defender voor opslag ingeschakeld.
1. Selecteer in de zijbalk ' containers ' en open een bestaande container of maak een nieuwe.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Een BLOB-container openen vanuit een Azure Storage-account" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Upload een bestand naar die container.

    > [!CAUTION]
    > Upload een bestand met gevoelige gegevens niet.

1. Selecteer in het context menu op het geüploade bestand de optie SAS genereren.

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="De optie SAS genereren voor een bestand in een BLOB-container":::

1. Wijzig de standaard opties en selecteer **SAS-token en URL genereren**.

1. Kopieer de gegenereerde SAS-URL.

1. Open de Tor-browser op de lokale computer.

    > [!TIP]
    > U kunt Tor downloaden van de Tor-project site [https://www.torproject.org/download/](https://www.torproject.org/download/) .

1. Navigeer in de Tor-browser naar de SAS-URL.

1. Down load het bestand dat u in stap 3 hebt geüpload.

    Binnen twee uur krijgt u de volgende beveiligings waarschuwing van Security Center:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Beveiligings waarschuwing met betrekking tot toegang vanaf een Tor-eind knooppunt":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender for Storage.

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- Een waarschuwing kunt u altijd exporteren, ongeacht of deze door Security Center is gegenereerd of door Security Center is ontvangen vanuit een ander beveiligingsproduct. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, een extern SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen naar een SIEM exporteren](continuous-export.md).
- [Advanced Defender for Storage inschakelen](../storage/common/azure-defender-storage-configure.md)
- [De lijst met waarschuwingen voor Azure Defender for Storage](alerts-reference.md#alerts-azurestorage)
- [De bedreigingsinformatiemogelijkheden van Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)