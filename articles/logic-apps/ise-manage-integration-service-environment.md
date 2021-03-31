---
title: Integratie service omgevingen in Azure Logic Apps beheren
description: Controleer de netwerk status en beheer logische apps, verbindingen, aangepaste connectors en integratie accounts in uw integratie service omgeving (ISE) voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 4df9543e5e747de640562b7e5be224e257e0cfd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92676112"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Uw ISE (Integratieserviceomgeving) beheren in Azure Logic Apps

In dit artikel wordt beschreven hoe u beheer taken kunt uitvoeren voor uw [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), bijvoorbeeld:

* Beheer de resources zoals Logic apps, verbindingen, integratie accounts en connectors in uw ISE.

* Controleer de netwerk status van uw ISE.

* Voeg capaciteit toe, start uw ISE opnieuw of verwijder uw ISE. Volg de stappen in dit onderwerp. Zie [artefacten toevoegen aan uw integratie service omgeving](../logic-apps/add-artifacts-integration-service-environment-ise.md)om deze artefacten aan uw ISE toe te voegen.

## <a name="view-your-ise"></a>Uw ISE weer geven

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Voer in het zoekvak van de portal "integratie service omgevingen" in en selecteer vervolgens **integratie service omgevingen**.

   ![Integratie service omgevingen zoeken](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Selecteer uw integratie service omgeving in de lijst met resultaten.

   ![Integratie service omgeving selecteren](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Ga door naar de volgende secties om logische apps, verbindingen, connectors of integratie accounts in uw ISE te vinden.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>De netwerk status controleren

Selecteer in het menu ISE onder **instellingen** de optie **netwerk status**. Dit deel venster toont de integriteits status voor uw subnetten en uitgaande afhankelijkheden op andere services.

![De netwerk status controleren](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> Als het netwerk van uw ISE een slechte status krijgt, kan de interne App Service Environment (ASE) die door uw ISE wordt gebruikt, ook een slechte status krijgen. Als de ASE langer dan zeven dagen niet in orde is, wordt de ASE onderbroken. Controleer de installatie van uw virtuele netwerk om deze status op te lossen. Los eventuele problemen op en start de ISE opnieuw op. 90 anders wordt de onderbroken ASE verwijderd, waarna de ISE onbruikbaar wordt. Zorg er daarom voor dat uw ISE in orde blijft om het benodigde verkeer toe te staan.
> 
> Raadpleeg de volgende onderwerpen voor meer informatie:
>
> * [Overzicht van Azure App Service diagnostische gegevens](../app-service/overview-diagnostics.md)
> * [Bericht logboek registratie voor Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Uw Logic apps beheren

U kunt de Logic apps in uw ISE weer geven en beheren.

1. Selecteer in het menu ISE onder **instellingen** de optie **Logic apps**.

   ![Logic apps weer geven](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Als u logische Apps wilt verwijderen die u niet meer nodig hebt in uw ISE, selecteert u deze Logic apps en selecteert u vervolgens **verwijderen**. Selecteer **Ja** om te bevestigen dat u wilt verwijderen.

> [!NOTE]
> Als u een onderliggende logische app verwijdert en opnieuw maakt, moet u de bovenliggende logische app opnieuw opslaan. De opnieuw gemaakte onderliggende app heeft andere meta gegevens.
> Als u de bovenliggende logische app niet opnieuw opslaat nadat u het onderliggende element opnieuw hebt gemaakt, zullen de aanroepen naar de onderliggende logische app mislukken met de fout ' niet toegestaan '. Dit gedrag is van toepassing op bovenliggende en onderliggende logische apps, bijvoorbeeld die artefacten gebruiken in integratie accounts of Azure functions aanroepen.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API-verbindingen beheren

U kunt de verbindingen weer geven en beheren die zijn gemaakt door de Logic apps die worden uitgevoerd in uw ISE.

1. Selecteer in het menu ISE onder **instellingen** de optie **API-verbindingen**.

   ![API-verbindingen weer geven](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Als u verbindingen wilt verwijderen die u niet meer nodig hebt in uw ISE, selecteert u deze verbindingen en selecteert u vervolgens **verwijderen**. Selecteer **Ja** om te bevestigen dat u wilt verwijderen.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE-connectors beheren

U kunt de API-connectors weer geven en beheren die zijn geïmplementeerd in uw ISE.

1. Selecteer in het menu ISE, onder **instellingen**, **beheerde connectors**.

   ![Beheerde connectors weer geven](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Als u connectors wilt verwijderen die u niet beschikbaar wilt maken in uw ISE, selecteert u deze connectors en selecteert u vervolgens **verwijderen**. Selecteer **Ja** om te bevestigen dat u wilt verwijderen.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Aangepaste connectors beheren

U kunt de aangepaste connectors weer geven en beheren die u hebt geïmplementeerd voor uw ISE.

1. Selecteer in het menu ISE onder **instellingen** de optie **aangepaste connectors**.

   ![Aangepaste connectoren vinden](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Als u aangepaste connectors die u niet meer nodig hebt in uw ISE wilt verwijderen, selecteert u deze connectors en selecteert u vervolgens **verwijderen**. Selecteer **Ja** om te bevestigen dat u wilt verwijderen.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Integratieaccounts beheren

1. Selecteer in uw ISE-menu onder **instellingen** de optie **integratie accounts**.

   ![Integratie accounts zoeken](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Als u integratie accounts uit uw ISE wilt verwijderen wanneer u deze niet meer nodig hebt, selecteert u deze integratie accounts en selecteert u vervolgens **verwijderen**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-capaciteit toevoegen

De Premium ISE-basis eenheid heeft een vaste capaciteit, dus als u meer door voer wilt, kunt u tijdens het maken of later meer schaal eenheden toevoegen. De Developer SKU bevat niet de mogelijkheid om schaal eenheden toe te voegen.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw ISE.

1. Als u de metrische gegevens over gebruik en prestaties voor uw ISE wilt bekijken, selecteert u in het menu ISE **overzicht**.

   ![Gebruik voor ISE weer geven](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. Onder **instellingen** selecteert u **uitschalen**. Selecteer een van deze opties in het deel venster **configureren** :

   * [**Hand matig schalen**](#manual-scale): schalen op basis van het aantal verwerkings eenheden dat u wilt gebruiken.
   * [**Aangepaste automatisch schalen**](#custom-autoscale): schalen op basis van metrische gegevens voor prestaties door te selecteren uit verschillende criteria en de drempel waarden op te geven voor het voldoen aan de criteria.

   ![Scherm afbeelding waarin de pagina uitschalen wordt weer gegeven als hand matig schalen is geselecteerd.](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Hand matig schalen

1. Wanneer u **hand matig schalen** hebt geselecteerd, selecteert u voor **extra capaciteit** het aantal schaal eenheden dat u wilt gebruiken.

   ![Selecteer het gewenste schaal type](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Selecteer **Opslaan** als u klaar bent.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Aangepaste automatische schaalaanpassing

1. Nadat u **aangepaste automatisch schalen** hebt geselecteerd, moet u bij **naam van instelling voor automatisch schalen** een naam opgeven voor de instelling en optioneel de Azure-resource groep selecteren waarvan de instelling deel uitmaakt.

   ![Geef een naam op voor de instelling voor automatisch schalen en selecteer een resource groep](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Voor de **standaard** voorwaarde selecteert u **schaal op basis van een metriek** of **schalen naar een specifiek aantal exemplaren**.

   * Als u op instantie gebaseerd kiest, voert u het nummer in voor de verwerkings eenheden. Dit is een waarde van 0 tot 10.

   * Als u op basis van metrische gegevens kiest, voert u de volgende stappen uit:

     1. Selecteer **een regel toevoegen** in de sectie **regels** .

     1. Stel in het deel venster **schaal regel** uw criteria en actie in die moeten worden uitgevoerd wanneer de regel wordt geactiveerd.

     1. Geef voor **instantie limieten** de volgende waarden op:

        * **Minimum**: het minimum aantal verwerkings eenheden dat moet worden gebruikt
        * **Maximum**: het maximum aantal verwerkings eenheden dat moet worden gebruikt
        * **Standaard**: als er problemen zijn bij het lezen van de metrische gegevens van de resource en de huidige capaciteit lager is dan de standaard capaciteit, wordt automatisch schalen geschaald naar het standaard aantal verwerkings eenheden. Als de huidige capaciteit echter de standaard capaciteit overschrijdt, wordt automatisch schalen niet geschaald in.

1. Selecteer **schaal voorwaarde toevoegen** om een andere voor waarde toe te voegen.

1. Wanneer u klaar bent met uw instellingen voor automatisch schalen, slaat u de wijzigingen op.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>ISE opnieuw starten

Als u de instellingen van de DNS-server of DNS-server wijzigt, moet u uw ISE opnieuw opstarten zodat de ISE deze wijzigingen kan ophalen. Het opnieuw starten van een Premium-SKU ISE resulteert niet in downtime vanwege redundantie en onderdelen die tijdens het recyclen één keer opnieuw worden opgestart. Een ontwikkelaar-SKU ISE echter downtime, omdat er geen redundantie bestaat. Zie [ISE sku's](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)voor meer informatie.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw ISE.

1. Selecteer **overzicht** in het menu ISE. **Start opnieuw** op in de werk balk overzicht.

   ![Integratie service omgeving opnieuw starten](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>ISE verwijderen

Voordat u een ISE verwijdert die u niet meer nodig hebt of een Azure-resource groep die een ISE bevat, moet u controleren of u geen beleids regels of vergren delingen hebt voor de Azure-resource groep die deze resources bevat of op uw virtuele Azure-netwerk, omdat deze items het verwijderen kunnen blok keren.

Nadat u uw ISE hebt verwijderd, moet u mogelijk tot negen uur wachten voordat u probeert uw virtuele Azure-netwerk of subnetten te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
