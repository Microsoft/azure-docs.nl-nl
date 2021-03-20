---
title: Over back-ups van Azure-bestands share
description: Meer informatie over het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: e955df7662bd18ed1d1d4ec1f0aa6c9474c5386f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89378080"
---
# <a name="about-azure-file-share-backup"></a>Over back-ups van Azure-bestands share

Azure file share Backup is een systeem eigen, op de cloud gebaseerde back-upoplossing die uw gegevens in de Cloud beschermt en extra onderhouds overhead voor on-premises back-upoplossingen verwijdert. De Azure Backup-service kan probleemloos worden geïntegreerd met Azure File Sync en Hiermee kunt u uw bestands share gegevens en uw back-ups centraliseren. Met deze eenvoudige, betrouw bare en veilige oplossing kunt u de beveiliging van uw bestands shares in een paar eenvoudige stappen configureren met een garantie dat u uw gegevens in elk nood geval kunt herstellen.

## <a name="key-benefits-of-azure-file-share-backup"></a>Belangrijkste voor delen van back-ups van Azure-bestands share

* Geen **infra structuur**: er is geen implementatie nodig om de beveiliging voor uw bestands shares te configureren.
* **Aangepaste Bewaar periode**: u kunt back-ups configureren met een dagelijks/wekelijks/maandelijks/jaarlijks Bewaar periode op basis van uw vereisten.
* **Ingebouwde beheer mogelijkheden**: u kunt back-ups plannen en de gewenste Bewaar periode opgeven zonder de extra overhead voor het weghalen van gegevens.
* **Direct terugzetten**: back-up van Azure-bestands share maakt gebruik van moment opnamen van bestands shares, zodat u alleen de bestanden kunt selecteren die u onmiddellijk wilt herstellen.
* **Waarschuwingen en rapportage**: u kunt waarschuwingen configureren voor back-up-en herstel fouten en de rapportage oplossing van Azure Backup gebruiken om inzicht te krijgen in de back-ups van uw bestands shares.
* **Bescherming tegen onbedoeld verwijderen van bestands shares**: Azure backup maakt de [functie voor voorlopig verwijderen](../storage/files/storage-files-prevent-file-share-deletion.md) op een opslag account niveau mogelijk met een Bewaar periode van 14 dagen. Zelfs als een schadelijke actor de bestands share verwijdert, worden de inhoud en herstel punten (moment opnamen) van de bestands share bewaard voor een Configureer bare Bewaar periode, waardoor het herstel van de bron inhoud en moment opnamen zonder gegevens verlies kan worden voltooid.

## <a name="architecture"></a>Architectuur

![Azure file share-back-uparchitectuur](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Hoe het back-upproces werkt

1. De eerste stap bij het configureren van back-ups voor Azure-bestands shares is het maken van een Recovery Services kluis. De kluis biedt een geconsolideerde weer gave van de back-ups die zijn geconfigureerd voor verschillende werk belastingen.

2. Wanneer u een kluis hebt gemaakt, detecteert de Azure Backup-service de opslag accounts die kunnen worden geregistreerd bij de kluis. U kunt het opslag account selecteren dat als host fungeert voor de bestands shares die u wilt beveiligen.

3. Nadat u het opslag account hebt geselecteerd, wordt in de Azure Backup-service de set bestands shares weer gegeven die aanwezig zijn in het opslag account en worden de namen opgeslagen in de catalogus met beheer lagen.

4. Vervolgens configureert u het back-upbeleid (planning en retentie) op basis van uw vereisten en selecteert u de bestands shares waarvan u een back-up wilt maken. De Azure Backup-service registreert de planningen in het besturings vlak voor geplande back-ups.

5. Op basis van het opgegeven beleid activeert de Azure Backup Scheduler op het geplande tijdstip back-ups. Als onderdeel van deze taak wordt de moment opname van de bestands share gemaakt met behulp van de bestands share-API. Alleen de URL van de moment opname wordt opgeslagen in het meta gegevens archief.

    >[!NOTE]
    >De bestands share gegevens worden niet overgebracht naar de back-upservice omdat de back-upservice moment opnamen maakt en beheert die deel uitmaken van uw opslag account. back-ups worden niet overgedragen naar de kluis.

6. U kunt de inhoud van de Azure-bestands share (afzonderlijke bestanden of de volledige share) herstellen op basis van moment opnamen die beschikbaar zijn op de bron bestands share. Zodra de bewerking is geactiveerd, wordt de URL van de moment opname opgehaald uit het meta gegevens archief en worden de gegevens weer gegeven en overgebracht van de bron momentopname naar de doel bestands share van uw keuze.

7. Als u Azure File Sync gebruikt, geeft de back-upservice aan de Azure File Sync-Service de paden van de bestanden die worden teruggezet, waardoor een detectie proces voor de achtergrond wijzigingen op deze bestanden wordt geactiveerd. Alle bestanden die zijn gewijzigd, worden naar het eind punt van de server gesynchroniseerd. Dit proces gebeurt parallel met de oorspronkelijke terugzet bewerking naar de Azure-bestands share.

8. De bewakings gegevens van de taak back-up en herstel worden gepusht naar de Azure Backup monitoring-service. Hierdoor kunt u Cloud back-ups voor uw bestands shares in één dash board bewaken. Daarnaast kunt u ook waarschuwingen of e-mail meldingen configureren wanneer de status van de back-up wordt beïnvloed. E-mails worden verzonden via de e-mail service van Azure.

## <a name="backup-costs"></a>Back-upkosten

Er zijn twee kosten verbonden aan de back-upoplossing voor Azure-bestands share:

1. **Opslag kosten voor moment opname**: de opslag kosten die worden gemaakt voor moment opnamen worden gefactureerd samen met Azure files gebruik volgens de [hier](https://azure.microsoft.com/pricing/details/storage/files/) genoemde prijs informatie

2. **Kosten van beveiligde instanties**: vanaf 1 september 2020 worden de kosten van een beveiligd exemplaar in rekening gebracht op basis van de [hier](https://azure.microsoft.com/pricing/details/backup/)genoemde prijs gegevens. De kosten voor het beveiligde exemplaar zijn afhankelijk van de totale grootte van beveiligde bestands shares in een opslag account.

Als u gedetailleerde schattingen wilt ontvangen voor het maken van back-ups van Azure-bestands shares, kunt u de gedetailleerde [Azure backup prijzen Estimator](https://aka.ms/AzureBackupCostEstimates).  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van back-ups van Azure-bestands shares](backup-afs.md)
* Vind antwoorden op [vragen over het maken van back-ups van Azure files](backup-azure-files-faq.md)
