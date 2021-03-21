---
title: Azure Log Analytics-werk ruimte verwijderen en herstellen | Microsoft Docs
description: Meer informatie over het verwijderen van uw Log Analytics-werk ruimte als u deze in een persoonlijk abonnement hebt gemaakt of uw werkruimte model opnieuw hebt gestructureerd.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/20/2020
ms.openlocfilehash: 83a64e3348d4af768c56609df3df5c9194ec5af1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030865"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Azure Log Analytics-werk ruimte verwijderen en herstellen

In dit artikel wordt uitgelegd hoe u met de procedure voor het uitvoeren van Azure Log Analytics Workspace kunt verwijderen en hoe u de verwijderde werk ruimte herstelt.

## <a name="considerations-when-deleting-a-workspace"></a>Overwegingen bij het verwijderen van een werk ruimte

Wanneer u een Log Analytics-werk ruimte verwijdert, wordt er een tijdelijke Verwijder bewerking uitgevoerd om het herstel van de werk ruimte met inbegrip van gegevens en verbonden agents binnen 14 dagen toe te staan, of het verwijderen per ongeluk of opzettelijk is geslaagd. Na de periode voor het voorlopig verwijderen zijn de resource en de gegevens van de werk ruimte niet-herstelbaar en in de wachtrij geplaatst om binnen 30 dagen volledig te worden verwijderd. De naam van de werk ruimte is vrijgegeven en u kunt deze gebruiken om een nieuwe werk ruimte te maken.

> [!NOTE]
> Als u het gedrag van zacht verwijderen wilt negeren en uw werk ruimte permanent wilt verwijderen, volgt u de stappen in [permanente werk ruimte verwijderen](#permanent-workspace-delete).

U wilt voorzichtig zijn wanneer u een werk ruimte verwijdert, omdat er mogelijk belang rijke gegevens en configuratie zijn die uw service bewerking negatief kunnen beïnvloeden. Bekijk welke agents, oplossingen en andere Azure-Services hun gegevens in Log Analytics opslaan, zoals:

* Beheeroplossingen
* Azure Automation
* Agents die worden uitgevoerd op virtuele Windows-en Linux-machines
* Agents die worden uitgevoerd op Windows-en Linux-computers in uw omgeving
* System Center Operations Manager

De bewerking voor zacht verwijderen verwijdert de werkruimte resource en de machtigingen van de bijbehorende gebruikers worden verbroken. Als gebruikers zijn gekoppeld aan andere werk ruimten, kunnen ze Log Analytics blijven gebruiken met die andere werk ruimten.

## <a name="soft-delete-behavior"></a>Gedrag bij voorlopig verwijderen

Met de bewerking voor het verwijderen van de werk ruimte wordt de resource manager-bron van de werk ruimte verwijderd, maar de configuratie en gegevens worden gedurende 14 dagen bewaard, terwijl het uiterlijk van de werk ruimte wordt verwijderd. Alle agents en System Center Operations Manager-beheer groepen die zijn geconfigureerd om te rapporteren aan de werk ruimte, blijven behouden in een zwevende status tijdens de tijdelijke periode voor het verwijderen. De service biedt verder een mechanisme voor het herstellen van de verwijderde werk ruimte, inclusief de bijbehorende gegevens en verbonden resources, waardoor het verwijderen in feite ongedaan wordt gemaakt.

> [!NOTE] 
> Geïnstalleerde oplossingen en gekoppelde services, zoals uw Azure Automation account, worden permanent verwijderd uit de werk ruimte tijdens het verwijderen en kunnen niet worden hersteld. Deze moeten na de herstel bewerking opnieuw worden geconfigureerd om de werk ruimte naar de eerder geconfigureerde status te brengen.

U kunt een werk ruimte verwijderen met behulp van [Power shell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace), [rest API](/rest/api/loganalytics/workspaces/delete)of in de [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.
3. Selecteer een werk ruimte in de lijst met Log Analytics-werk ruimten en klik vervolgens op **verwijderen**  boven in het middelste deel venster.
4. Er wordt een bevestigings pagina weer gegeven waarin de gegevens opname in de afgelopen week wordt weer gegeven in de werk ruimte. 
5. Als u de werk ruimte permanent wilt verwijderen om de optie later te herstellen, selecteert u het selectie vakje **de werk ruimte permanent verwijderen** .
6. Typ de naam van de werk ruimte die u wilt bevestigen en klik vervolgens op **verwijderen**.

   ![Verwijderen van werk ruimte bevestigen](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Permanente werk ruimte verwijderen
De methode voor het zacht verwijderen past mogelijk niet in sommige scenario's zoals ontwikkelen en testen, waarbij u een implementatie met dezelfde instellingen en werkruimte naam moet herhalen. In dergelijke gevallen kunt u uw werk ruimte permanent verwijderen en de periode voor voorlopig verwijderen negeren. Met de bewerking permanent verwijderen van werk ruimte wordt de naam van de werk ruimte vrijgegeven en kunt u een nieuwe werk ruimte maken met dezelfde naam.

> [!IMPORTANT]
> Gebruik de permanente bewerking voor het verwijderen van werk ruimten met een waarschuwing omdat het onomkeerbaar is en u de werk ruimte en de gegevens niet kunt herstellen.

Als u een werk ruimte permanent wilt verwijderen met de Azure Portal, selecteert u het selectie vakje **de werk ruimte permanent verwijderen** voordat u op de knop **verwijderen** klikt.

Als u een werk ruimte permanent wilt verwijderen met behulp van Power shell, voegt u het label '-ForceDelete ' toe om uw werk ruimte permanent te verwijderen. De optie-ForceDelete is momenteel beschikbaar met AZ. OperationalInsights 2.3.0 of hoger. 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>Werk ruimte herstellen
Wanneer u per ongeluk een Log Analytics-werk ruimte verwijdert, wordt de werk ruimte door de service in een tijdelijke verwijderings status geplaatst, waardoor deze niet toegankelijk is voor een wille keurige bewerking. De naam van de verwijderde werk ruimte blijft behouden tijdens de tijdelijke verwijderings periode en kan niet worden gebruikt voor het maken van een nieuwe werk ruimte. Na de periode voor het voorlopig verwijderen is de werk ruimte niet-herstelbaar, wordt deze gepland voor permanent verwijderen en wordt de naam ervan vrijgegeven en kan deze worden gebruikt voor het maken van een nieuwe werk ruimte.

U kunt uw werk ruimte herstellen tijdens de tijdelijke periode, inclusief gegevens, configuratie en verbonden agents. U moet over Inzender machtigingen beschikken voor het abonnement en de resource groep waar de werk ruimte zich bevond vóór de bewerking voor zacht verwijderen. Het herstel van de werk ruimte wordt uitgevoerd door de Log Analytics-werk ruimte opnieuw te maken met de details van de verwijderde werk ruimte, met inbegrip van:

- Abonnements-id
- Naam resourcegroep
- Werkruimtenaam
- Region

> [!IMPORTANT]
> Als uw werk ruimte is verwijderd als onderdeel van het verwijderen van een resource groep, moet u eerst de resource groep opnieuw maken.

### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**. U ziet de lijst met werk ruimten die u in het geselecteerde bereik hebt.
3. Klik in het menu linksboven op **herstellen** om een pagina met werk ruimten in de status zacht verwijderen te openen die kan worden hersteld.

   ![Scherm opname van het Log Analytics werk ruimten in Azure Portal met de optie herstellen gemarkeerd op de menu balk.](media/delete-workspace/recover-menu.png)

4. Selecteer de werk ruimte en klik op **herstellen** om de werk ruimte te herstellen.

   ![Scherm opname van het dialoog venster Verwijderde Log Analytics werk ruimten herstellen in Azure Portal waarbij een werk ruimte is gemarkeerd en de knop herstellen is geselecteerd.](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

De werk ruimte en alle bijbehorende gegevens worden teruggezet na de herstel bewerking. Oplossingen en gekoppelde services zijn permanent verwijderd uit de werk ruimte toen ze werd verwijderd en moeten opnieuw worden geconfigureerd om de werk ruimte naar de eerder geconfigureerde status te brengen. Sommige gegevens zijn mogelijk niet beschikbaar voor de query nadat de werk ruimte is hersteld en de bijbehorende schema's worden toegevoegd aan de werk ruimte.

## <a name="troubleshooting"></a>Problemen oplossen

U moet ten minste *log Analytics Inzender* machtigingen hebben om een werk ruimte te verwijderen.

* Als u niet zeker weet of de verwijderde werk ruimte zich in de modus voor voorlopig verwijderen bevindt en kan worden hersteld, klikt u op [Prullenbak openen](#recover-workspace) op *log Analytics pagina werk ruimten* om een lijst met voorlopig verwijderde werk ruimten per abonnement weer te geven. Permanent verwijderde werk ruimten worden niet opgenomen in de lijst.
* Als er een fout bericht wordt weer gegeven, *is de naam van de werk ruimte al in gebruik* of *conflict* bij het maken van een werk ruimte, kan dit sinds:
  * De naam van de werk ruimte is niet beschikbaar en wordt gebruikt door iemand in uw organisatie of door een andere klant.
  * De werk ruimte is in de afgelopen 14 dagen verwijderd en de naam is gereserveerd voor de tijdelijke periode voor het verwijderen. Volg deze stappen om de werk ruimte eerst te herstellen en vervolgens permanent verwijderen uit te voeren om de werk ruimte te vervangen door de voorlopig te verwijderen en permanent te verwijderen om een nieuwe werk ruimte met dezelfde naam te maken.<br>
    1. [Herstel](#recover-workspace) uw werk ruimte.
    2. Uw werk ruimte [permanent verwijderen](#permanent-workspace-delete) .
    3. Maak een nieuwe werk ruimte met dezelfde naam voor de werk ruimte.
 
      Nadat de aanroep voor het verwijderen is voltooid, kunt u de werk ruimte herstellen en de permanente Verwijder bewerking volt ooien in een van de methoden die u eerder hebt voorgesteld.

* Als u 204-respons code krijgt met een *resource die niet is gevonden* bij het verwijderen van een werk ruimte, zijn er mogelijk een opeenvolgende nieuwe pogingen ondervonden. 204 is een leeg antwoord. Dit betekent meestal dat de resource niet bestaat, zodat de verwijdering is voltooid zonder dat u iets hoeft te doen.
* Als u de resource groep en de werk ruimte hebt verwijderd, ziet u de verwijderde werk ruimte in de [Prullenbak](#recover-workspace) van de pagina. de herstel bewerking mislukt echter met fout code 404 omdat de resource groep niet bestaat. Maak de resource groep opnieuw en voer het herstel opnieuw uit.
