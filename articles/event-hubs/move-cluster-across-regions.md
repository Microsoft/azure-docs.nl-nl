---
title: Een toegewezen Azure Event Hubs-cluster verplaatsen naar een andere regio | Microsoft Docs
description: Dit artikel laat u zien hoe u een toegewezen Azure Event Hubs-cluster kunt verplaatsen vanuit de huidige regio naar een andere regio.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89380705"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Een toegewezen Azure Event Hubs-cluster verplaatsen naar een andere regio
In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon exporteert voor een bestaand Event Hubs toegewezen cluster en vervolgens de sjabloon gebruikt om een cluster te maken met dezelfde configuratie-instellingen in een andere regio. 

Als u andere resources, zoals naam ruimten en Event hubs, hebt in de Azure-resource groep die het Event Hubs cluster bevat, kunt u de sjabloon op het niveau van de resource groep exporteren zodat alle gerelateerde resources in één stap naar de nieuwe regio kunnen worden verplaatst. In de stappen in dit artikel ziet u hoe u een **Event hubs cluster** naar de sjabloon exporteert. De stappen voor het exporteren van een **resource groep** naar de sjabloon zijn vergelijkbaar. 

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat het toegewezen cluster kan worden gemaakt in de doel regio. De eenvoudigste manier om erachter te komen is met behulp van de Azure Portal te proberen [een event hubs toegewezen cluster te maken](event-hubs-dedicated-cluster-create-portal.md). U ziet de lijst met regio's die op dat moment worden ondersteund voor het maken van het cluster. 

## <a name="prepare"></a>Voorbereiden
Exporteer een resource manager-sjabloon om aan de slag te gaan. Deze sjabloon bevat instellingen die uw Event Hubs toegewezen cluster beschrijven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle resources** en selecteer vervolgens uw event hubs toegewezen cluster.
3. Selecteer > **instellingen**  >  **sjabloon exporteren**.
4. Kies **downloaden** op de pagina **sjabloon exporteren** .

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Resource Manager-sjabloon downloaden" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze.

   Dit zip-bestand bevat de. json-bestanden die de sjabloon en scripts bevatten voor het implementeren van de sjabloon.


## <a name="move"></a>Verplaatsen

Implementeer de sjabloon voor het maken van een Event Hubs toegewezen cluster in de doel regio. 


1. Selecteer in de Azure Portal **een resource maken**.
2. In **de Marketplace zoeken**, type **sjabloon implementatie** en Sjabloonimlementatie selecteren **(implementeren met aangepaste sjablonen)**.
5. Selecteer **Bouw uw eigen sjabloon in de editor**.
6. Selecteer **bestand laden** en volg de instructies voor het laden van de **template.jsin** het bestand dat u in de laatste sectie hebt gedownload.
1. Werk de waarde van de `location` eigenschap bij zodat deze naar de nieuwe regio verwijst. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .
1. Selecteer **Opslaan** om de sjabloon op te slaan. 
1. Voer op de pagina **aangepaste implementatie** de volgende stappen uit: 
    1. Selecteer een Azure-**abonnement**. 
    2. Selecteer een bestaande **resource groep** of maak een. 
    3. Selecteer de doel **locatie** of-regio. Als u een bestaande resource groep hebt geselecteerd, is deze instelling alleen-lezen. 
    4. Voer de volgende stappen uit in de sectie **instellingen** :    
        1. Voer de naam van het nieuwe **cluster** in. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Resource Manager-sjabloon implementeren":::
    5. Selecteer **Controleren en maken** onderaan de pagina. 
    1. Controleer op de pagina **controleren en maken** de instellingen en selecteer vervolgens **maken**.  

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u na de implementatie wilt beginnen, kunt u het **doel event hubs toegewezen cluster** verwijderen en herhaalt u de stappen die worden beschreven in de secties voor het [voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Als u de wijzigingen wilt door voeren en de verplaatsing van een Event Hubs cluster wilt volt ooien, verwijdert u het **Event hubs cluster** in de oorspronkelijke regio. 

Een Event Hubs cluster (bron of doel) verwijderen met behulp van de Azure Portal:

1. In het zoek venster aan de bovenkant van Azure Portal, typt u **Event hubs clusters** en selecteert u **Event hubs clusters** uit de zoek resultaten. U ziet het Event Hubs cluster in een lijst.
2. Selecteer het cluster dat u wilt verwijderen en selecteer **verwijderen** op de werk balk. 
3. Bevestig de verwijdering op de pagina **cluster verwijderen** door de naam van het **cluster** te typen en vervolgens **verwijderen** te selecteren. 

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u een Event Hubs toegewezen cluster kunt verplaatsen van de ene regio naar een andere. 

Zie het artikel [Event hubs naam ruimten verplaatsen in verschillende regio's](move-across-regions.md) voor instructies over het verplaatsen van een naam ruimte van de ene regio naar een andere regio. 

Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:

- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)
