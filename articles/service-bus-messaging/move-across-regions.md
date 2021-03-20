---
title: Een Service Bus naam ruimte naar een andere regio verplaatsen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een Azure Service Bus naam ruimte van de huidige regio naar een andere regio kunt verplaatsen.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88860894"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Een Azure Service Bus naam ruimte naar een andere regio verplaatsen
Er zijn verschillende scenario's waarin u uw bestaande Service Bus naam ruimte wilt verplaatsen van de ene regio naar een andere. Het is bijvoorbeeld mogelijk dat u een naam ruimte met dezelfde configuratie voor testen wilt maken. U kunt ook een secundaire naam ruimte in een andere regio maken als onderdeel van de [planning voor nood herstel](service-bus-geo-dr.md).

Dit zijn de stappen op hoog niveau:

1. De Service Bus naam ruimte in de huidige regio exporteren naar een Azure Resource Manager sjabloon. 
1. Update locatie voor resources in de sjabloon. Verwijder ook het standaard abonnements filter uit de sjabloon omdat u een standaard regel niet kunt maken, omdat deze automatisch voor u wordt gemaakt. 
1. Gebruik de sjabloon om de Service Bus naam ruimte te implementeren in de doel regio. 
1. Controleer de implementatie om ervoor te zorgen dat de naam ruimte, wacht rijen, onderwerpen en abonnementen voor-onderwerpen allemaal worden gemaakt in de doel regio. 
1. Voltooi de verplaatsing door de naam ruimte te verwijderen uit de bron regio na het verwerken van alle berichten. 

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat Azure Service Bus en functies die uw account gebruikt, in de doel regio worden ondersteund.
 
## <a name="prepare"></a>Voorbereiden
Exporteer een resource manager-sjabloon om aan de slag te gaan. Deze sjabloon bevat instellingen die uw Service Bus naam ruimte beschrijven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle resources** en selecteer vervolgens uw service bus naam ruimte.
3. Selecteer > **instellingen**  >  **sjabloon exporteren**.
4. Kies **downloaden** op de pagina **sjabloon exporteren** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager-sjabloon downloaden":::
5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze. Dit zip-bestand bevat de JSON-bestanden van de sjabloon en de para meters. 
1. Open de template.jsin het bestand in de uitgepakte map. 
1. Zoek de `location` waarde voor de eigenschap en vervang deze door de nieuwe naam voor de regio of locatie. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .
1. Definities van resources van het `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` type: verwijderen Vergeet niet om het komma ()-teken te verwijderen dat `,` aan deze sectie is voorafgaan om de JSON geldig te laten blijven.  

    > [!NOTE]
    > U kunt geen standaard regel voor een abonnement maken met behulp van een resource manager-sjabloon. De standaard regel wordt automatisch gemaakt wanneer het abonnement wordt gemaakt in de doel regio. 

## <a name="move"></a>Verplaatsen
Implementeer de sjabloon om een Service Bus naam ruimte te maken in de doel regio. 

1. Selecteer in de Azure Portal **een resource maken**.
2. In **de Marketplace zoeken**, type **sjabloon implementatie** voor de zoek tekst, selecteer **Sjabloonimlementatie (implementeren met aangepaste sjablonen)** en druk vervolgens op **Enter**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Nieuwe sjabloon implementatie":::    
1. Selecteer op de pagina **Sjabloonimlementatie** **maken**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Nieuwe sjabloon implementatie-knop maken":::        
1. Selecteer op de pagina **aangepaste implementatie** de optie **uw eigen sjabloon bouwen in de editor**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Uw eigen sjabloon bouwen in de editor-koppeling":::            
1. Selecteer op de pagina **sjabloon bewerken** de optie **bestand laden** op de werk balk en volg de instructies om detemplate.jste laden **in** het bestand dat u in de laatste sectie hebt gedownload.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Sjabloon selecteren":::                
1. Selecteer **Opslaan** om de sjabloon op te slaan. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Sjabloon opslaan":::                    
1. Voer op de pagina **aangepaste implementatie** de volgende stappen uit: 
    1. Selecteer een Azure-**abonnement**. 
    2. Selecteer een bestaande **resource groep** of maak een. 
    3. Selecteer de doel **locatie** of-regio. Als u een bestaande resource groep hebt geselecteerd, is deze instelling alleen-lezen. 
    4. Voer een nieuwe **naam in voor de naam ruimte**.
    1. Selecteer **Controleren + maken**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Resource Manager-sjabloon implementeren":::
    1. Selecteer op de pagina **controleren en maken** onder aan de pagina **maken** . 
    
## <a name="verify"></a>Verifiëren
1. Nadat de implementatie is voltooid, selecteert **u Ga naar resource groep**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Ga naar de koppeling naar de resource groep":::    
1. Selecteer op de pagina **resource groep** de service bus naam ruimte. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Service Bus naam ruimte selecteren":::    
1. Controleer op de pagina **Service Bus naam ruimte** of u de wacht rijen, onderwerpen en abonnementen van de bron regio ziet. 
    1. U ziet **wacht rijen** in de naam ruimte aan de onderkant van het rechterdeel venster.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="Wacht rijen in de naam ruimte":::
    2. Ga naar het tabblad **onderwerpen** om de onderwerpen in de naam ruimte te bekijken
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="Onderwerpen in de naam ruimte":::
    3. Selecteer het onderwerp om te controleren of abonnementen zijn gemaakt. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Onderwerp abonnementen":::      
    
    

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u na de implementatie wilt beginnen, kunt u de naam ruimte van het **doel service bus** verwijderen en herhaalt u de stappen die worden beschreven in de secties [voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Als u de wijzigingen wilt door voeren en het verplaatsen van een Service Bus naam ruimte wilt volt ooien, verwijdert u de **bron service bus naam ruimte**. Zorg ervoor dat u alle berichten verwerkt voordat u de naam ruimte verwijdert. 

Een Service Bus naam ruimte (bron of doel) verwijderen met behulp van de Azure Portal:

1. Typ **Service Bus** in het venster zoeken aan de bovenkant van Azure Portal en selecteer **Service Bus** in de zoek resultaten. U ziet de Service Bus naam ruimten in een lijst.
2. Selecteer de doel naam ruimte die u wilt verwijderen en selecteer **verwijderen** op de werk balk. 

    ![Naam ruimte verwijderen-knop](./media/move-across-regions/delete-namespace-button.png)
3. Controleer op de pagina **resources verwijderen** de geselecteerde resources en bevestig de verwijdering door **Ja** te typen en vervolgens **verwijderen** te selecteren. 

    U kunt ook de resource groep met de naam ruimte Service Bus verwijderen. Selecteer op de pagina **resource groep** de optie **resource groep verwijderen** op de werk balk en bevestig de verwijdering. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure Service Bus naam ruimte van de ene regio naar de andere verplaatst en worden de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:

- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
