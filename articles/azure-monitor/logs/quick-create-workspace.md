---
title: Een Log Analytics-werk ruimte maken in de Azure Portal | Microsoft Docs
description: Meer informatie over het maken van een Log Analytics-werk ruimte om beheer oplossingen en gegevens verzameling in te scha kelen vanuit uw Cloud-en on-premises omgevingen in de Azure Portal.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 27eac9cefe645087cae43c34cb6503b562fb7c07
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656296"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Een Log Analytics-werkruimte in Azure Portal maken
Gebruik het menu **log Analytics werk ruimten** om een log Analytics-werk ruimte te maken met behulp van de Azure Portal. Een Log Analytics-werk ruimte is een unieke omgeving voor Azure Monitor logboek gegevens. Elke werk ruimte heeft een eigen gegevens opslagplaats en-configuratie, en gegevens bronnen en-oplossingen zijn geconfigureerd om hun gegevens op te slaan in een bepaalde werk ruimte. U hebt een Log Analytics-werk ruimte nodig als u van plan bent om gegevens te verzamelen uit de volgende bronnen:

* Azure-resources in uw abonnement
* On-premises computers die worden bewaakt door System Center Operations Manager
* Verzamelingen van apparaten uit Configuration Manager 
* Diagnose-of logboek gegevens vanuit Azure Storage

Zie de volgende onderwerpen voor andere bronnen, zoals virtuele Azure-machines en Windows-of Linux-Vm's in uw omgeving:

*  [Gegevens verzamelen van virtuele machines van Azure](../vm/quick-collect-azurevm.md) 
*  [Gegevens verzamelen van een hybride Linux-computer](../vm/quick-collect-linux-computer.md)
*  [Gegevens verzamelen van de hybride Windows-computer](../vm/quick-collect-windows-computer.md)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Een werkruimte maken
Klik in Azure Portal op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.

![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
Klik op **toevoegen** en geef waarden op voor de volgende opties:

   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Kies voor **resource groep** een bestaande resource groep die al is ingesteld, of maak een nieuwe.  
   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*. Deze naam moet uniek zijn binnen alle Azure Monitor-abonnementen.
   * Selecteer een beschik bare **regio**.  Zie voor meer informatie de [regio's log Analytics beschikbaar in](https://azure.microsoft.com/regions/services/) en zoek naar Azure monitor in het veld **zoeken naar een product** .  


        ![Log Analytics-resourceblade maken](media/quick-create-workspace/create-workspace.png)  


Klik op **Beoordelen en maken** om de instellingen te controleren en klik vervolgens **Maken** om de werkruimte te maken. Hiermee selecteert u een standaardprijscategorie van Betalen per gebruik- die geen wijzigingen doorvoert totdat u voldoende gegevens begint te verzamelen. Zie [log Analytics prijs informatie](https://azure.microsoft.com/pricing/details/log-analytics/)voor meer informatie over andere prijs categorieën.



## <a name="troubleshooting"></a>Problemen oplossen
Wanneer u een werk ruimte maakt die in de afgelopen 14 dagen is verwijderd en de status voor het [voorlopig verwijderen](../logs/delete-workspace.md#soft-delete-behavior)heeft, kan de bewerking afwijken, afhankelijk van de configuratie van uw werk ruimte:
1. Als u dezelfde naam voor de werk ruimte, de resource groep, het abonnement en de regio opgeeft als in de verwijderde werk ruimte, wordt uw werk ruimte hersteld, met inbegrip van de bijbehorende gegevens, configuratie en verbonden agents.
2. Als u dezelfde naam voor de werk ruimte gebruikt, maar een andere resource groep, abonnement of regio, krijgt u een fout melding *deze werkruimte naam wordt al gebruikt. Probeer een ander account*. Volg deze stappen om de werk ruimte eerst te herstellen en permanent verwijderen uit te voeren om de tijdelijke verwijdering te onderdrukken en uw werk ruimte permanent te verwijderen en een nieuwe werk ruimte met dezelfde naam te maken:
   - Uw werkruimte [herstellen](../logs/delete-workspace.md#recover-workspace)
   - Uw werkruimte [permanent verwijderen](../logs/delete-workspace.md#permanent-workspace-delete)
   - Een nieuwe werk ruimte maken met dezelfde werkruimte naam

## <a name="next-steps"></a>Volgende stappen
Nu u een werk ruimte beschikbaar hebt, kunt u een verzameling van de telemetrie-bewaking configureren, Zoek opdrachten in Logboeken uitvoeren om die gegevens te analyseren en een beheer oplossing toevoegen om extra gegevens en analytische inzichten te leveren. 

* Zie [status van log Analytics werk ruimte controleren in azure monitor](../logs/monitor-workspace.md) waarschuwings regels maken om de status van uw werk ruimte te controleren. 
* Zie [Azure service-logboeken en-metrische gegevens verzamelen voor gebruik in log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) voor het inschakelen van de gegevensverzameling van Azure-resources met Azure Diagnostics of Azure Storage.
