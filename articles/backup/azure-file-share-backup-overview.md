---
title: Over back-ups van Azure-bestands delen
description: Meer informatie over het maken van een back-up van Azure-bestands shares in de Recovery Services-kluis
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516812"
---
# <a name="about-azure-file-share-backup"></a>Over back-ups van Azure-bestands delen

Azure File Share Backup is een systeemeigen back-upoplossing in de cloud die uw gegevens in de cloud beschermt en extra onderhoudsoverhead voor on-premises back-upoplossingen elimineert. De Azure Backup-service kan probleemloos worden geïntegreerd met Azure File Sync en stelt u in staat om uw bestandsgegevens en back-ups te centraliseren. Met deze eenvoudige, betrouwbare en veilige oplossing kunt u de beveiliging voor uw bedrijfsbestands shares configureren in een paar eenvoudige stappen, met de zekerheid dat u uw gegevens in elk noodscenario kunt herstellen.

## <a name="key-benefits-of-azure-file-share-backup"></a>Belangrijke voordelen van back-ups van Azure-bestands share

* **Geen infrastructuur:** er is geen implementatie nodig om de beveiliging voor uw bestands shares te configureren.
* **Aangepaste retentie:** u kunt back-ups configureren met dagelijkse/wekelijkse/maandelijkse/jaarlijkse retentie op basis van uw vereisten.
* **Ingebouwde beheermogelijkheden:** u kunt back-ups plannen en de gewenste retentieperiode opgeven zonder de extra overhead van het verwijderen van gegevens.
* **Direct herstellen: back-ups** van Azure-bestands delen maken gebruik van momentopnamen van bestands delen, zodat u alleen de bestanden kunt selecteren die u direct wilt herstellen.
* **Waarschuwingen en rapportage:** u kunt waarschuwingen configureren voor back-up- en herstelfouten en de rapportageoplossing van Azure Backup gebruiken om inzicht te krijgen in back-ups in uw bestands shares.
* **Beveiliging tegen onbedoeld verwijderen van** bestands shares: Azure Backup functie voor het verwijderen van gegevens in op opslagaccountniveau met een bewaarperiode van 14 dagen. [](../storage/files/storage-files-prevent-file-share-deletion.md) Zelfs als een kwaadwillende actor de bestands share verwijdert, worden de inhoud en herstelpunten (momentopnamen) van de bestands share bewaard voor een configureerbare retentieperiode, waardoor de broninhoud en momentopnamen succesvol en volledig kunnen worden hersteld zonder gegevensverlies.

## <a name="architecture"></a>Architectuur

![Back-uparchitectuur voor Azure-bestands delen](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Hoe het back-upproces werkt

1. De eerste stap bij het configureren van back-ups voor Azure-bestands shares is het maken van een Recovery Services-kluis. De kluis biedt een geconsolideerde weergave van de back-ups die zijn geconfigureerd voor verschillende workloads.

2. Nadat u een kluis hebt Azure Backup, detecteert de service de opslagaccounts die kunnen worden geregistreerd bij de kluis. U kunt het opslagaccount selecteren dat als host voor de bestands shares wordt gebruikt die u wilt beveiligen.

3. Nadat u het opslagaccount hebt geselecteerd, geeft Azure Backup-service de set bestands shares weer die aanwezig zijn in het opslagaccount en worden de namen opgeslagen in de beheerlaagcatalogus.

4. Vervolgens configureert u het back-upbeleid (planning en retentie) op basis van uw vereisten en selecteert u de bestands shares waar u een back-up van wilt maken. De Azure Backup registreert de planningen in het besturingsvlak voor geplande back-ups.

5. Op basis van het opgegeven beleid activeert Azure Backup back-ups op het geplande tijdstip. Als onderdeel van deze taak wordt de momentopname van de bestands share gemaakt met behulp van de API voor bestands delen. Alleen de MOMENTOPNAME-URL wordt opgeslagen in het metagegevensopslag.

    >[!NOTE]
    >De bestandsdeelgegevens worden niet overgedragen naar de Backup-service, omdat de Backup-service momentopnamen maakt en beheert die deel uitmaken van uw opslagaccount en back-ups niet worden overgedragen naar de kluis.

6. U kunt de inhoud van de Azure-bestands share (afzonderlijke bestanden of de volledige share) herstellen vanuit momentopnamen die beschikbaar zijn op de bronbestands share. Zodra de bewerking is geactiveerd, wordt de momentopname-URL opgehaald uit het metagegevensopslag en worden de gegevens weergegeven en overgebracht van de bronmomentopname naar de doelbestands share van uw keuze.

7. Als u Azure File Sync gebruikt, geeft de Backup-service aan de Azure File Sync-service de paden aan van de bestanden die worden hersteld, waarna een detectieproces voor achtergrondwijziging wordt uitgevoerd voor deze bestanden. Bestanden die zijn gewijzigd, worden gesynchroniseerd naar het server-eindpunt. Dit proces vindt parallel plaats met de oorspronkelijke herstel naar de Azure-bestands share.

8. De bewakingsgegevens van de back-up- en herstelklus worden naar de Azure Backup Monitoring-service. Hiermee kunt u cloudback-ups voor uw bestands shares bewaken in één dashboard. Daarnaast kunt u ook waarschuwingen of e-mailmeldingen configureren wanneer de back-uptoestand wordt beïnvloed. E-mailberichten worden verzonden via de e-mailservice van Azure.

## <a name="backup-costs"></a>Back-upkosten

Er zijn twee kosten verbonden aan de back-upoplossing voor Azure-bestands delen:

1. **Opslagkosten voor** momentopnamen: opslagkosten voor momentopnamen worden samen met het Azure Files gefactureerd volgens de prijsgegevens die hier worden [vermeld](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Kosten voor beveiligde exemplaren:** vanaf 1 september 2020 worden er kosten in rekening gebracht voor beveiligde exemplaren volgens de prijsgegevens die hier worden [vermeld.](https://azure.microsoft.com/pricing/details/backup/) De kosten voor beveiligde exemplaren zijn afhankelijk van de totale grootte van beveiligde bestands shares in een opslagaccount.

Als u gedetailleerde schattingen wilt krijgen voor het maken van back-up van Azure-bestands shares, kunt u de gedetailleerde Azure Backup [prijsraming downloaden.](https://aka.ms/AzureBackupCostEstimates)  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken [van een back-up van Azure-bestands shares](backup-afs.md)
* Vind antwoorden op [vragen over het maken van back-Azure Files](backup-azure-files-faq.yml)
