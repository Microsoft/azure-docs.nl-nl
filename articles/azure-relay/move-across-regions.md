---
title: Een Azure Relay naam ruimte naar een andere regio verplaatsen
description: In dit artikel wordt beschreven hoe u een Azure Relay naam ruimte van de huidige regio naar een andere regio kunt verplaatsen.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463607"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Een Azure Relay naam ruimte naar een andere regio verplaatsen
In dit artikel wordt beschreven hoe u een Azure Relay naam ruimte van de ene regio naar een andere regio kunt verplaatsen. Dit zijn de stappen op hoog niveau:

1. De relay-naam ruimte **exporteren** naar een Azure Resource Manager sjabloon.
1. **Update locatie (regio)** voor resources in de sjabloon. Verwijder ook alle **dynamische** WCF-relays uit de sjabloon. 

    WCF-relays hebben twee modi. In de eerste modus wordt de WCF-relay expliciet gemaakt met behulp van de Azure Portal-of Azure Resource Manager sjabloon. Op de pagina **WCF-Relais** van het Azure Portal ziet u de eigenschap **isDynamic** ingesteld op **False** voor een relay in deze modus. 

    In de tweede modus wordt de WCF-Relay automatisch gegenereerd wanneer een listener (Server) verbinding maakt voor een bepaald eindpunt adres. Zolang de listener is verbonden met de relay, ziet u de relay in de lijst met WCF-relays in de Azure Portal. Voor een relay in deze modus wordt de eigenschap **isDynamic** ingesteld op **True** , omdat deze dynamisch wordt gegenereerd. De dynamische WCF-relay verdwijnt wanneer de verbinding met de listener wordt verbroken. 
1. Resources **implementeren** met behulp van de sjabloon naar de doel regio.

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat de Azure Relay-service beschikbaar is in de doel regio. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Voorbereiden
Exporteer een resource manager-sjabloon om aan de slag te gaan. Deze sjabloon bevat instellingen die uw Azure Relay naam ruimte beschrijven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle resources** en selecteer vervolgens uw Azure relay naam ruimte.
3. Selecteer **sjabloon exporteren** onder **instellingen** in het menu links.
4. Kies **downloaden** op de pagina **sjabloon exporteren** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager-sjabloon downloaden":::
5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze. Dit zip-bestand bevat de JSON-bestanden van de sjabloon en de para meters. 
1. Open de **template.jsin** het bestand uit de uitgepakte map in een editor van uw keuze.
1. Zoek de `location` waarde voor de eigenschap en vervang deze door de nieuwe naam voor de regio. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .
1. Definities van **dynamische WCF-doorstuur** bronnen verwijderen (type: `Microsoft.Relay/namespaces/WcfRelays` ). Dynamische WCF-relays zijn de ' **isDynamic** -eigenschap ingesteld op **True '** op de pagina **relays** . In het volgende voor beeld is **echo service** een dynamische WCF-relay en de definitie ervan moet worden verwijderd uit de sjabloon. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Dynamische relays":::

## <a name="move"></a>Verplaatsen
Implementeer de sjabloon voor het maken van een relay-naam ruimte in de doel regio. 

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
1. Selecteer op de pagina **resource groep** de Azure relay naam ruimte. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Azure Relay naam ruimte selecteren":::    
1. Selecteer op de pagina **Azure relay naam ruimte** **hybride verbindingen** of **WCF-relays** in het menu links om te controleren of er hybride verbindingen en WCF-relays zijn gemaakt. Als u bent verg eten definities voor dynamische WCF-relays te verwijderen voordat u de sjabloon importeert, verwijdert u deze op de pagina **WCF-relays** . De dynamische WCF-relays worden automatisch gemaakt wanneer clients verbinding maken met de relay-naam ruimte. 

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u na de implementatie wilt beginnen, kunt u de naam ruimte van het **doel Azure relay** verwijderen en herhaalt u de stappen die worden beschreven in de secties [voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Als u de wijzigingen wilt door voeren en de verplaatsing van een naam ruimte wilt volt ooien, verwijdert u de **Azure relay naam ruimte** in de bron regio. 

Een Azure Relay naam ruimte (bron of doel) verwijderen met behulp van de Azure Portal:

1. Typ in het zoek venster aan de bovenkant van Azure Portal **relays** en selecteer **door sturen** van **Services** in de zoek resultaten. U ziet alle Azure Relay naam ruimten in een lijst.
2. Selecteer de doel naam ruimte die u wilt verwijderen om de **Relay** -pagina te openen. 
1. Selecteer op de pagina **Relais** de optie **verwijderen** op de werk balk. 

    ![Naam ruimte verwijderen-knop](./media/move-across-regions/delete-namespace-button.png)
3. Typ op de pagina **naam ruimte verwijderen** de naam van de Azure relay naam ruimte om het verwijderen te bevestigen en selecteer vervolgens **verwijderen**. 

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een Azure Relay naam ruimte verplaatst van de ene regio naar een andere regio. Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:

- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
