---
title: Surface hubs bewaken met Azure Monitor | Microsoft Docs
description: Gebruik de Surface Hub oplossing om de status van uw Surface hubs bij te houden en inzicht te krijgen in de manier waarop ze worden gebruikt.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 9d81af064b18ca8113599b460f2b9524c7288439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711073"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Surface hubs bewaken met Azure Monitor om hun status bij te houden

![Surface Hub-symbool](./media/surface-hubs/surface-hub-symbol.png)

In dit artikel wordt beschreven hoe u de Surface Hub oplossing in Azure Monitor kunt gebruiken om micro soft Surface Hub-apparaten te bewaken. Met deze oplossing kunt u de status van uw Surface hubs bijhouden en begrijpen hoe ze worden gebruikt.

Voor elke Surface Hub is micro soft monitoring agent geïnstalleerd. Via de agent kunt u gegevens van uw Surface Hub naar een Log Analytics werkruimte in Azure Monitor verzenden. Er worden logboek bestanden van uw Surface hubs gelezen en vervolgens naar Azure Monitor verzonden. Problemen als servers die offline zijn, de agenda die niet wordt gesynchroniseerd of als het apparaataccount niet kan worden aangemeld bij Skype, worden weer gegeven in het Surface Hub dash board in Azure Monitor. Met behulp van de gegevens in het dash board kunt u apparaten identificeren die niet worden uitgevoerd, of die andere problemen ondervinden, en mogelijk oplossingen voor de gedetecteerde problemen Toep assen.

## <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende informatie om de oplossing te installeren en configureren. Als u uw Surface hubs in Azure Monitor wilt beheren, hebt u het volgende nodig:

* Een [log Analytics abonnements](https://azure.microsoft.com/pricing/details/log-analytics/) niveau waarmee het aantal apparaten dat u wilt bewaken, wordt ondersteund. Log Analytics prijzen variëren afhankelijk van het aantal apparaten dat is inge schreven en hoeveel gegevens er worden verwerkt. Als u uw Surface Hub-implementatie wilt plannen, moet u rekening houden.

Vervolgens voegt u een bestaande Log Analytics-werk ruimte toe, of maakt u een nieuwe. Gedetailleerde instructies voor het gebruik van beide methoden zijn bij [het maken van een log Analytics-werk ruimte in de Azure Portal](../logs/quick-create-workspace.md). Nadat de Log Analytics-werk ruimte is geconfigureerd, zijn er twee manieren om uw Surface Hub apparaten in te schrijven:

* Automatisch via intune
* Hand matig door **instellingen** op uw Surface hub apparaat.

## <a name="set-up-monitoring"></a>Controle instellen
U kunt de status en activiteit van uw Surface Hub bewaken met behulp van Azure Monitor. U kunt de Surface Hub inschrijven met behulp van intune of lokaal met behulp van **instellingen** op de Surface hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Surface hubs aansluiten op Azure Monitor via intune
U hebt de werk ruimte-ID en de werkruimte sleutel nodig voor de Log Analytics-werk ruimte waarmee u uw Surface hubs kunt beheren. U kunt deze ophalen uit de werk ruimte-instellingen in het Azure Portal.

InTune is een micro soft-product waarmee u de configuratie-instellingen voor Log Analytics werk ruimte centraal kunt beheren die worden toegepast op een of meer van uw apparaten. Volg deze stappen om uw apparaten te configureren via intune:

1. Meld u aan bij intune.
2. Navigeer naar **instellingen**  >  **verbonden bronnen**.
3. Een beleid maken of bewerken op basis van de Surface Hub sjabloon.
4. Ga naar de sectie Azure Operational Insights van het beleid en voeg de Log Analytics *werk ruimte-id* en de *werkruimte sleutel* toe aan het beleid.
5. U kunt nu het beleid opslaan.
6. Koppel het beleid aan de juiste groep apparaten.

   ![Intune-beleid](./media/surface-hubs/intune.png)

InTune synchroniseert vervolgens de Log Analytics-instellingen met de apparaten in de doel groep en registreert deze in uw Log Analytics-werk ruimte.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Surface hubs aansluiten op Azure Monitor met behulp van de instellingen-app
U hebt de werk ruimte-ID en de werkruimte sleutel nodig voor de Log Analytics-werk ruimte waarmee u uw Surface hubs kunt beheren. U kunt deze ophalen uit de instellingen voor de werk ruimte Log Analytics in de Azure Portal.

Als u intune niet gebruikt om uw omgeving te beheren, kunt u apparaten hand matig registreren via de **instellingen** op elke Surface hub:

1. Open **instellingen** vanuit uw Surface hub.
2. Voer de beheerders referenties voor het apparaat in wanneer u hierom wordt gevraagd.
3. Klik op **Dit apparaat** en klik onder **bewaking** op **log Analytics instellingen configureren**.
4. Selecteer **bewaking inschakelen**.
5. Typ in het dialoog venster Log Analytics instellingen de Log Analytics **werk ruimte-id** en typ de **werkruimte sleutel**.  
   ![Scherm afbeelding toont de instellingen van het Microsoft Operations Manager-pakket met behulp van het selectie vakje controle inschakelen en tekst vakken voor werk ruimte-ID en werkruimte sleutel.](./media/surface-hubs/settings.png)
6. Klik op **OK** om de configuratie te volt ooien.

Er wordt een bevestiging weer gegeven met de melding of de configuratie is toegepast op het apparaat. Als dat het geval is, wordt er een bericht weer gegeven met de mede deling dat de agent is verbonden met Azure Monitor. Het apparaat begint vervolgens met het verzenden van gegevens naar Azure Monitor waar u deze kunt bekijken en er actie op moet ondernemen.

## <a name="monitor-surface-hubs"></a>Surface hubs bewaken
Het bewaken van uw Surface hubs met Azure Monitor is vergelijkbaar met het controleren van andere geregistreerde apparaten.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wanneer u op de tegel Surface Hub klikt, wordt de status van uw apparaat weer gegeven.

   ![Surface Hub dash board](./media/surface-hubs/surface-hub-dashboard.png)

U kunt [waarschuwingen](../alerts/alerts-overview.md) maken op basis van bestaande of aangepaste zoek opdrachten in Logboeken. Met behulp van de gegevens Azure Monitor verzameld van uw Surface hubs, kunt u zoeken naar problemen en waarschuwen op basis van de voor waarden die u voor uw apparaten definieert.

## <a name="next-steps"></a>Volgende stappen
* Gebruik [logboek query's in azure monitor](../logs/log-query-overview.md) om gedetailleerde Surface hub gegevens weer te geven.
* Maak [waarschuwingen](../alerts/alerts-overview.md) om u te waarschuwen wanneer er problemen optreden met uw Surface hubs.
