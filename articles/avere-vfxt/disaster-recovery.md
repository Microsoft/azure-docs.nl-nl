---
title: Richt lijnen voor herstel na nood gevallen voor avere vFXT voor Azure
description: Gegevens in avere vFXT voor Azure beveiligen tegen onbedoeld verwijderen of uitval
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342243"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Richt lijnen voor herstel na nood gevallen voor avere vFXT voor Azure

In dit artikel vindt u een overzicht van strategieën voor het beveiligen van uw avere vFXT voor Azure-werk stroom en biedt richt lijnen voor het maken van back-ups van gegevens, zodat u ongel ukken of uitval kunt herstellen.

AVERE vFXT voor Azure slaat tijdelijk gegevens op in de cache. Gegevens worden op lange termijn opgeslagen in back-end-opslag systemen: on-premises hardwareapparaten, Azure Blob-opslag containers of beide.

Denk aan deze vier gebieden om te beschermen tegen storingen en mogelijke gegevens verlies:

* Bescherming tegen uitval tijd als een avere vFXT voor Azure-systeem niet beschikbaar is
* Gegevens in de cluster cache beveiligen
* Gegevens beveiligen in back-end NAS-hardware-opslag
* Gegevens beveiligen in back-end Azure Blob-Cloud opslag

Elke avere vFXT voor Azure-klant moet een eigen uitgebreid plan voor herstel na nood gevallen maken dat abonnementen bevat voor deze items. U kunt ook tolerantie bouwen in toepassingen die u gebruikt met het vFXT-cluster. Lees de koppelingen in de [volgende stappen](#next-steps) voor hulp.

## <a name="protect-against-downtime"></a>Beveiligen tegen downtime

Redundantie is ingebouwd in het avere vFXT voor Azure-product:

* Het cluster is Maxi maal beschikbaar en er kan met een minimale onderbreking een failover worden uitgevoerd voor afzonderlijke cluster knooppunten.
* Gegevens die in de cache worden gewijzigd, worden regel matig geschreven naar back-end-kern bestanden (hardware NAS of Azure Blob) voor lange termijn opslag.

Elk avere vFXT voor Azure-cluster moet zich in één beschikbaarheids zone bevinden, maar u kunt redundante clusters in verschillende zones of verschillende regio's gebruiken om snel toegang te bieden in het geval van een regionale storing.

U kunt ook opslag containers in meerdere regio's plaatsen als u zich zorgen maakt over het verlies van toegang tot gegevens. Houd er echter rekening mee dat trans acties tussen regio's een hogere latentie en hogere kosten hebben dan trans acties die binnen een regio blijven.

## <a name="protect-data-in-the-cluster-cache"></a>Gegevens in de cluster cache beveiligen

Gegevens in de cache worden altijd naar de kern bestanden geschreven voordat een regel matig wordt afgesloten, maar in een niet-beheerd afsluiten kunnen de gewijzigde gegevens in de cache verloren gaan.

Als u het cluster gebruikt voor het optimaliseren van alleen lees bewerkingen van bestanden, gaan er geen wijzigingen verloren. Als u het cluster ook gebruikt voor het opslaan van bestands wijzigingen (schrijf bewerkingen), moet u overwegen of u het [cache beleid](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) van de kern bestanden wilt aanpassen<!-- link to legacy doc --> aanpassen hoe vaak gegevens worden geschreven naar lange termijn opslag.

In het algemeen is het herstel plan gericht op het maken van back-ups van de back-end-opslag systemen, die meer gegevens bevatten en normaal gesp roken belang rijker zijn voor het opnieuw instellen van uw werk stroom na een storing.

## <a name="protect-data-in-nas-core-filers"></a>Gegevens in NAS-kern bestanden beveiligen

Gebruik geaccepteerde methoden voor het beveiligen van gegevens die zijn opgeslagen in een on-premises NAS-hardware kern bestand, inclusief moment opnamen en volledige back-ups zoals aanbevolen door de NAS-provider. Herstel na nood gevallen voor deze kern bestandsers valt buiten het bereik van dit artikel.

## <a name="protect-data-in-azure-blob-storage"></a>Gegevens in Azure Blob-opslag beveiligen

AVERE vFXT voor Azure gebruikt lokaal redundante opslag (LRS) voor kern bestanden van Azure Blob. Dit betekent dat de gegevens in de BLOB-containers automatisch worden gekopieerd voor beveiliging tegen tijdelijke hardwarefouten binnen een Data Center.

In deze sectie vindt u tips voor het verder beschermen van uw gegevens in Blob Storage van zeldzame regionale onderbrekingen of onopzettelijke verwijderingen.

Best practices voor het beveiligen van gegevens in Azure Blob Storage zijn onder andere:

* Kopieer uw kritieke gegevens regel matig naar een ander opslag account in een andere regio (zo vaak als bepaald door uw plan voor herstel na nood gevallen).
* De toegang tot gegevens op alle doel systemen beheren om onbedoeld verwijderen of beschadiging te voor komen. Overweeg het gebruik van [resource vergrendelingen](../azure-resource-manager/management/lock-resources.md) voor gegevens opslag.
* Schakel de functie avere vFXT voor Azure- [Cloud momentopname](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) in voor uw BLOB-kern bestanden.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>AVERE vFXT-kern gegevensbestand kopiëren naar een back-upaccount

Volg deze stappen om een back-up van gegevens te maken in een ander account.

1. Als dat nodig is, kunt u een nieuwe versleutelings sleutel genereren en deze veilig opslaan buiten de betrokken systemen.

   Als uw gegevens zijn versleuteld door de avere vFXT voor Azure-cluster, moet u een nieuwe versleutelings sleutel genereren voordat u de gegevens naar een ander opslag account kopieert. Bewaar deze sleutel en het wacht woord veilig in een faciliteit die veilig zijn en die niet wordt beïnvloed door een regionale fout.

   U moet deze sleutel opgeven bij het toevoegen van de container aan een cluster, zelfs als u deze opnieuw toevoegt aan het oorspronkelijke cluster.

   [Instellingen voor Cloud versleuteling](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>) lezen<!-- link to legacy doc site --> voor gedetailleerde informatie.

   Als uw container alleen gebruikmaakt van de ingebouwde versleuteling van Azure, kunt u deze stap overs Laan.

1. Verwijder de kern bestand van het systeem. Hiermee wordt het cluster gedwongen om alle gewijzigde gegevens te schrijven naar de back-end-opslag.

   Hoewel u de kern bestand opnieuw moet toevoegen nadat de back-up is gemaakt, is het verwijderen de beste manier om te garanderen dat alle gegevens volledig naar de back-end zijn geschreven. (De optie ' Suspend ' kan soms gewijzigde gegevens in de cache achterlaten.) <!-- xxx true? or just metadata? -->

   Noteer de naam en koppelings gegevens van de kern bestand (vermeld op de pagina **naam ruimte** in het configuratie scherm), zodat u deze kunt repliceren wanneer u de container opnieuw toevoegt na de back-up.

   Gebruik het configuratie scherm van het cluster om de kern bestand te verwijderen. [Open het configuratie scherm van het cluster](avere-vfxt-cluster-gui.md) en kies **core filer**  >  **kern bestanden beheren**. Zoek het opslag systeem waarvan u een back-up wilt maken en gebruik de knop **verwijderen** om het te verwijderen uit het cluster.

1. Maak een nieuwe, lege Blob Storage-container in een ander opslag account in een andere regio.

1. Gebruik een handig Kopieer programma om de gegevens op de kern bestand te kopiëren naar de nieuwe container. De kopie moet de gegevens zonder wijzigingen repliceren en zonder de oorspronkelijke indeling van het Cloud-bestands systeem die wordt gebruikt door avere vFXT voor Azure te onderbreken. Azure-hulpprogram ma's zijn onder andere [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)en [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Nadat u de gegevens naar de back-upcontainer hebt gekopieerd, voegt u de oorspronkelijke container weer toe aan het cluster zoals beschreven in [opslag ruimte configureren](avere-vfxt-add-storage.md).

   * Gebruik dezelfde naam voor de kern bestandsnaam en koppelings informatie zodat client-werk stromen niet hoeven te worden gewijzigd.
   * Stel de waarde van de **Bucket inhoud** in op de optie bestaande gegevens.
   * Als de container is versleuteld door het cluster, moet u de huidige versleutelings sleutel voor de inhoud opgeven. (Dit is de sleutel die u in stap één hebt bijgewerkt.)

Voor back-ups na de eerste moet u geen nieuwe opslag container maken. U kunt echter wel een nieuwe versleutelings sleutel genereren telkens wanneer u een back-up uitvoert, zodat u zeker weet dat de huidige sleutel is opgeslagen op een plek waar u zich bevindt.

### <a name="access-a-backup-data-source-during-an-outage"></a>Toegang tot een gegevens bron voor back-ups tijdens een storing

Ga als volgt te werk om toegang te krijgen tot de back-upcontainer van een avere vFXT voor Azure-cluster:

1. Maak, indien nodig, een nieuwe avere-vFXT voor Azure-cluster in een regio waarin het probleem niet optreedt.

   > [!TIP]
   > Wanneer u een avere vFXT voor Azure-cluster maakt, kunt u een kopie van de aanmaak-sjabloon en-para meters opslaan. Als u deze informatie opslaat tijdens het maken van uw primaire cluster, kunt u deze gebruiken om een vervangend cluster met dezelfde eigenschappen te maken. Klik op de pagina [samen vatting](avere-vfxt-deploy.md#validation-and-purchase) op de koppeling **sjabloon en para meters downloaden** . Sla de informatie op in een bestand voordat u het cluster maakt.

1. Voeg een nieuwe Cloud core-bestands extensie toe die naar de dubbele BLOB-container verwijst.

   Zorg ervoor dat de doel container al gegevens bevat in de instelling van de **Bucket inhoud** van de wizard kern bestand maken. (Het systeem moet u waarschuwen als u per ongeluk deze set **leeg** laat.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Als dat nodig is, werkt u de clients bij zodat ze het nieuwe cluster of de nieuwe kern bestand koppelen in plaats van het origineel. (Als u de vervangende kern bestand met dezelfde naam en hetzelfde pad als de oorspronkelijke container toevoegt, hoeft u de client processen alleen bij te werken als u het nieuwe cluster moet koppelen aan een nieuw IP-adres.)

## <a name="next-steps"></a>Volgende stappen

* Lees [cluster tuning](avere-vfxt-tuning.md)voor meer informatie over het aanpassen van instellingen voor avere VFXT voor Azure.
* Meer informatie over herstel na nood gevallen en het maken van flexibele toepassingen in Azure:

  * [Technische richtlijnen voor flexibiliteit van Azure](/azure/architecture/framework/resiliency/overview)
  * [Herstel na een serviceonderbreking in de gehele regio](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->