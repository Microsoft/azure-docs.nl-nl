---
title: Integratie van de VM Insights-kaart met Operations Manager | Microsoft Docs
description: VM Insights detecteert automatisch toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. In dit artikel wordt beschreven hoe u met de kaart functie automatisch gedistribueerde toepassings diagrammen maakt in Operations Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 3a7d0d49313cb524a5bf39add5c9a55862dcad47
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046887"
---
# <a name="integrate-system-center-operations-manager-with-vm-insights-map-feature"></a>System Center Operations Manager integreren met de functie voor het toewijzen van vm's met VM

In VM Insights kunt u gedetecteerde toepassings onderdelen weer geven op virtuele Windows-en Linux-machines (Vm's) die worden uitgevoerd in azure of in uw omgeving. Als deze integratie tussen de kaart functie en System Center Operations Manager, kunt u automatisch gedistribueerde toepassings diagrammen maken in Operations Manager die zijn gebaseerd op de dynamische afhankelijkheids toewijzingen in VM Insights. In dit artikel wordt beschreven hoe u uw System Center Operations Manager-beheer groep configureert ter ondersteuning van deze functie.

>[!NOTE]
>Als u Servicetoewijzing al hebt geïmplementeerd, kunt u uw kaarten weer geven in VM Insights, waaronder extra functies voor het controleren van de status en prestaties van de virtuele machine. De kaart functie van VM Insights is bedoeld om de zelfstandige Servicetoewijzing oplossing te vervangen. Zie [overzicht van VM Insights](../vm/vminsights-overview.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een System Center Operations Manager-beheer groep (2012 R2 of hoger).
* Een Log Analytics-werk ruimte die is geconfigureerd om VM Insights te ondersteunen.
* Een of meer virtuele Windows-en Linux-machines of fysieke computers die worden bewaakt door Operations Manager en het verzenden van gegevens naar uw Log Analytics-werk ruimte. Linux-servers die rapporteren aan een Operations Manager-beheer groep moeten worden geconfigureerd om rechtstreeks verbinding te maken met Azure Monitor. Raadpleeg het overzicht in [logboek gegevens verzamelen met de log Analytics-agent](../agents/log-analytics-agent.md)voor meer informatie.
* Een service-principal met toegang tot het Azure-abonnement dat is gekoppeld aan de Log Analytics-werk ruimte. Ga voor meer informatie naar [een service-principal maken](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installeer de Servicetoewijzing management pack

U schakelt de integratie tussen Operations Manager en de kaart functie in door de Microsoft.SystemCenter. ServiceMap management pack bundel te importeren (Microsoft.SystemCenter. ServiceMap. MPB). U kunt de management pack bundel downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=55763). De bundel bevat de volgende Management Packs:

* Micro soft Servicetoewijzing-toepassings weergaven
* Micro soft System Center Servicetoewijzing intern
* Micro soft System Center Servicetoewijzing onderdrukkingen
* Micro soft System Center Servicetoewijzing

## <a name="configure-integration"></a>Integratie configureren

Nadat u de Servicetoewijzing management pack hebt geïnstalleerd, wordt er een nieuw knoop punt **servicetoewijzing** weer gegeven onder **Operations Management Suite** in het deel venster **beheer** van uw Operations Manager Operations-console.

>[!NOTE]
>[Operations Management Suite is een verzameling van services](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) die zijn opgenomen log Analytics, maakt nu deel uit van [Azure monitor](../overview.md).

Ga als volgt te werk om de integratie van de VM Insights-map te configureren:

1. Als u de configuratie wizard wilt openen, klikt u in het deel venster **servicetoewijzing overzicht** op **werk ruimte toevoegen**.  

    ![Servicetoewijzing deel venster Overzicht](media/service-map-scom/scom-configuration.png)

2. Voer in het venster configuratie van de **verbinding** de naam van de TENANT of id, toepassings-id (ook wel bekend als de gebruikers naam of clientID) en het wacht woord van de Service-Principal in en klik vervolgens op **volgende**. Ga voor meer informatie naar een service-principal maken.

    ![Het venster verbindings configuratie](media/service-map-scom/scom-config-spn.png)

3. Selecteer in het venster voor het selecteren van het **abonnement** het Azure-abonnement, de Azure-resource groep (de naam die de log Analytics-werk ruimte bevat) en log Analytics werk ruimte en klik vervolgens op **volgende**.

    ![De werk ruimte Operations Manager configuratie](media/service-map-scom/scom-config-workspace.png)

4. In het venster **computer groep selecteren** kiest u de servicetoewijzing machine groepen die u wilt synchroniseren met Operations Manager. Klik op **computer groepen toevoegen/verwijderen**, kies groepen in de lijst met **beschik bare computer groepen** en klik op **toevoegen**.  Wanneer u klaar bent met het selecteren van groepen, klikt u op **OK** om te volt ooien.

    ![De Operations Manager configuratie machine groepen](media/service-map-scom/scom-config-machine-groups.png)

5. In het venster **server selectie** configureert u de servicetoewijzing servers groep met de servers die u wilt synchroniseren tussen Operations Manager en de kaart functie. Klik op **servers toevoegen/verwijderen**.

    Voor de integratie van het bouwen van een gedistribueerd toepassings diagram voor een-server moet de-server:

   * Bewaakt door Operations Manager
   * Geconfigureerd om te rapporteren aan de Log Analytics werk ruimte die is geconfigureerd met VM Insights
   * Vermeld in de groep Servicetoewijzing servers

     ![De configuratie groep Operations Manager](media/service-map-scom/scom-config-group.png)

6. Optioneel: Selecteer de resource groep alle beheerser vers om te communiceren met Log Analytics en klik vervolgens op **werk ruimte toevoegen**.

    ![Screen shot van het scherm Server groep in Microsoft Operations Management Suite werk ruimte toevoegen waarvoor alle beheerser vers-resource groep is geselecteerd.](media/service-map-scom/scom-config-pool.png)

    Het kan een minuut duren voordat de Log Analytics-werk ruimte is geconfigureerd en geregistreerd. Nadat de configuratie is geconfigureerd, start Operations Manager de eerste synchronisatie van de kaart.

    ![Scherm opname van het venster voltooiing in Microsoft Operations Management Suite werk ruimte toevoegen bevestigen dat de werk ruimte is toegevoegd.](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Integratie controleren

Nadat de Log Analytics werk ruimte is verbonden, wordt een nieuwe map, Servicetoewijzing, weer gegeven in het deel venster **bewaking** van de Operations Manager Operations-console.

![Het deel venster Operations Manager bewaking](media/service-map-scom/scom-monitoring.png)

De map Servicetoewijzing heeft vier knoop punten:

* **Actieve waarschuwingen**: een lijst met alle actieve waarschuwingen over de communicatie tussen Operations Manager en Azure monitor.  

  >[!NOTE]
  >Deze waarschuwingen worden niet Log Analytics waarschuwingen die zijn gesynchroniseerd met Operations Manager en ze worden gegenereerd in de beheer groep op basis van werk stromen die zijn gedefinieerd in de Servicetoewijzing management pack.

* **Servers**: geeft een lijst van de bewaakte servers die zijn geconfigureerd voor synchronisatie vanuit de functie van de VM Insights-kaart.

    ![Het deel venster Operations Manager monitoring servers](media/service-map-scom/scom-monitoring-servers.png)

* **Afhankelijkheids weergaven van computer groepen**: een lijst met alle computer groepen die zijn gesynchroniseerd vanuit de kaart functie. U kunt op een wille keurige groep klikken om het gedistribueerde toepassings diagram weer te geven.

    ![Scherm opname van Servicetoewijzing een diagram met afbeeldingen weer geven voor elke computer groep en lijnen die de afhankelijkheden ertussen aangeven.](media/service-map-scom/scom-group-dad.png)

* **Server afhankelijkheids weergaven**: een lijst met alle servers die zijn gesynchroniseerd vanuit de kaart functie. U kunt klikken op een wille keurige server om het gedistribueerde toepassings diagram weer te geven.

    ![Scherm opname van Servicetoewijzing een diagram met afbeeldingen weer geven voor elke server en lijnen die de afhankelijkheden ertussen aangeven.](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>De werk ruimte bewerken of verwijderen

U kunt de geconfigureerde werk ruimte bewerken of verwijderen via het servicetoewijzing deel venster **overzicht** (**beheer** venster > **Operations Management Suite**  >  **servicetoewijzing**).

> [!NOTE]
> [Operations Management Suite is een verzameling services](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) die log Analytics bevat, die nu deel uitmaakt van [Azure monitor](../overview.md).

U kunt slechts één Log Analytics werkruimte configureren in deze huidige versie.

![Het deel venster Operations Manager werk ruimte bewerken](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Regels en onderdrukkingen configureren

Met een regel, *Microsoft.SystemCenter. ServiceMapImport. rule*, wordt regel matig gegevens opgehaald uit de functie van de VM Insights-kaart. Als u het synchronisatie-interval wilt wijzigen, kunt u de regel overschrijven en de waarde voor de para meter **IntervalMinutes** wijzigen.

![Het venster Eigenschappen van Operations Manager onderdrukkingen](media/service-map-scom/scom-overrides.png)

* **Ingeschakeld**: automatische updates in-of uitschakelen.
* **IntervalMinutes**: Hiermee geeft u de tijd tussen updates op. Het standaard interval is een uur. Als u toewijzingen vaker wilt synchroniseren, kunt u de waarde wijzigen.
* **TimeoutSeconds**: Hiermee geeft u de tijds duur vóór de time-out van de aanvraag op.
* **TimeWindowMinutes**: Hiermee geeft u het tijd venster op voor het opvragen van gegevens. De standaard waarde is 60 minuten, het Maxi maal toegestane interval.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Het huidige ontwerp bevat de volgende problemen en beperkingen:

* U kunt alleen verbinding maken met een enkele Log Analytics-werk ruimte.
* Hoewel u servers hand matig aan de groep Servicetoewijzing servers kunt toevoegen via het deel venster **ontwerpen** , worden de kaarten voor die servers niet direct gesynchroniseerd. Ze worden tijdens de volgende synchronisatie cyclus gesynchroniseerd vanuit de functie van de VM Insights-kaart.
* Als u wijzigingen aanbrengt in de diagrammen voor gedistribueerde toepassingen die zijn gemaakt door de management pack, worden deze wijzigingen waarschijnlijk overschreven bij de volgende synchronisatie met VM Insights.

## <a name="create-a-service-principal"></a>Een service-principal maken

Zie voor officiële Azure-documentatie over het maken van een Service-Principal:

* [Een service-principal maken met behulp van Power shell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Een service-principal maken met behulp van Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Een service-principal maken met behulp van de Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>Suggesties

Hebt u feedback voor ons over de integratie met de functie voor het toewijzen van vm's met de VM of deze documentatie? Ga naar onze [pagina met gebruikers spraak](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), waar u functies kunt suggereren of stem op bestaande suggesties.

