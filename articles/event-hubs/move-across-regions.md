---
title: Een Azure Event Hubs-naam ruimte naar een andere regio verplaatsen | Microsoft Docs
description: In dit artikel leest u hoe u een Azure Event Hubs-naam ruimte van de huidige regio naar een andere regio kunt verplaatsen.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: b177c3916919e3d97325f9d8c6b6027c00cb476f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019919"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Een Azure Event Hubs-naam ruimte verplaatsen naar een andere regio
In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon exporteert voor een bestaande Event Hubs naam ruimte en vervolgens de sjabloon gebruikt om een naam ruimte te maken met dezelfde configuratie-instellingen in een andere regio. Met dit proces worden echter geen gebeurtenissen verplaatst die nog niet zijn verwerkt. U moet de gebeurtenissen van de oorspronkelijke naam ruimte verwerken voordat u deze verwijdert.
 
Als u andere resources hebt in de Azure-resource groep met de naam ruimte Event Hubs, wilt u mogelijk de sjabloon op het niveau van de resource groep exporteren zodat alle gerelateerde resources in één stap naar de nieuwe regio kunnen worden verplaatst. In de stappen in dit artikel ziet u hoe u een **naam ruimte** naar de sjabloon exporteert. De stappen voor het exporteren van een **resource groep** naar de sjabloon zijn vergelijkbaar. 

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de services en functies die uw account gebruikt, worden ondersteund in de doelregio.
- Als u de **functie vastleggen** hebt ingeschakeld voor Event hubs in de naam ruimte, verplaatst u [Azure Storage of Azure data Lake Store gen 2](../storage/common/storage-account-move.md) -of [Azure data Lake Store gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) -accounts voordat u de Event hubs naam ruimte verplaatst. U kunt ook de resource groep met zowel opslag-als Event Hubs-naam ruimten verplaatsen naar de andere regio door de stappen te volgen die vergelijkbaar zijn met die in dit artikel worden beschreven. 
- Als de Event Hubs naam ruimte zich in een **Event hubs cluster** bevindt, [verplaatst u het toegewezen cluster](move-cluster-across-regions.md) naar de **doel regio** voordat u de stappen in dit artikel doorloopt. U kunt ook de Quick Start- [sjabloon op github](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) gebruiken om een event hubs cluster te maken. Verwijder in de sjabloon het naam ruimte gedeelte van de JSON om alleen het cluster te maken. 

## <a name="prepare"></a>Voorbereiden
Exporteer een resource manager-sjabloon om aan de slag te gaan. Deze sjabloon bevat instellingen die uw Event Hubs naam ruimte beschrijven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **alle resources** en selecteer vervolgens uw event hubs naam ruimte.
3. Selecteer > **instellingen**  >  **sjabloon exporteren**.
4. Kies **downloaden** op de pagina **sjabloon exporteren** .

    ![Resource Manager-sjabloon downloaden](./media/move-across-regions/download-template.png)
5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze.

   Dit zip-bestand bevat de. json-bestanden die de sjabloon en scripts bevatten voor het implementeren van de sjabloon.


## <a name="move"></a>Verplaatsen

Implementeer de sjabloon om een Event Hubs naam ruimte te maken in de doel regio. 


1. Selecteer in de Azure Portal **een resource maken**.
2. In **de Marketplace zoeken**, type **sjabloon implementatie** en Sjabloonimlementatie selecteren **(implementeren met aangepaste sjablonen)**.
5. Selecteer **Bouw uw eigen sjabloon in de editor**.
6. Selecteer **bestand laden** en volg de instructies voor het laden van de **template.jsin** het bestand dat u in de laatste sectie hebt gedownload.
1. Werk de waarde van de `location` eigenschap bij zodat deze naar de nieuwe regio verwijst. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .
1. Selecteer **Opslaan** om de sjabloon op te slaan. 
1. Voer op de pagina **aangepaste implementatie** de volgende stappen uit: 
    1. Selecteer een Azure- **abonnement**. 
    2. Selecteer een bestaande **resource groep** of maak een. Als de bron naam ruimte zich in een Event Hubs cluster bevindt, selecteert u de resource groep die het cluster in de doel regio bevat. 
    3. Selecteer de doel **locatie** of-regio. Als u een bestaande resource groep hebt geselecteerd, is deze instelling alleen-lezen. 
    4. Voer de volgende stappen uit in de sectie **instellingen** :    
        1. Voer de naam van de nieuwe **naam ruimte** in. 

            ![Resource Manager-sjabloon implementeren](./media/move-across-regions/deploy-template.png)
        2. Als uw bron naam ruimte zich in een **Event hubs cluster** bevindt, voert u de namen van de **resource groep** en het **Event hubs cluster** in als onderdeel van de **externe ID**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Als Event Hub in uw naam ruimte een opslag account gebruikt voor het vastleggen van gebeurtenissen, geeft u de naam van de resource groep en het veld opslag account voor op `StorageAccounts_<original storage account name>_external` . 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selecteer **Controleren en maken** onderaan de pagina. 
    1. Controleer op de pagina **controleren en maken** de instellingen en selecteer vervolgens **maken**.   

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u na de implementatie wilt beginnen, kunt u de naam ruimte van het **doel event hubs** verwijderen en herhaalt u de stappen die worden beschreven in de secties [voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Als u de wijzigingen wilt door voeren en het verplaatsen van een Event Hubs naam ruimte wilt volt ooien, verwijdert u de **Event hubs naam ruimte** in de oorspronkelijke regio. Zorg ervoor dat u alle gebeurtenissen in de naam ruimte hebt verwerkt voordat u de naam ruimte verwijdert. 

Een Event Hubs naam ruimte (bron of doel) verwijderen met behulp van de Azure Portal:

1. Typ **Event hubs** in het venster zoeken aan de bovenkant van Azure Portal en selecteer **Event hubs** in de zoek resultaten. U ziet de Event Hubs naam ruimten in een lijst.
2. Selecteer de doel naam ruimte die u wilt verwijderen en selecteer **verwijderen** op de werk balk. 

    ![Naam ruimte verwijderen-knop](./media/move-across-regions/delete-namespace-button.png)
3. Bevestig de verwijdering op de pagina **naam ruimte verwijderen** door de naam van de naam **ruimte** te typen en vervolgens **verwijderen** te selecteren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure Event Hubs-naam ruimte verplaatst van de ene regio naar een andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)
