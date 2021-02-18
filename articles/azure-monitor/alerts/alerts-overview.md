---
title: Overzicht van waarschuwings-en meldings bewaking in azure
description: Overzicht van waarschuwingen in Azure. Waarschuwingen, klassieke waarschuwingen en de interface van waarschuwingen.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: 96e15c1e07d437855b6553757295800406a4cf4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609621"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Overzicht van waarschuwingen in Microsoft Azure 

In dit artikel wordt beschreven welke waarschuwingen, hun voor delen en hoe u ze kunt gaan gebruiken.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Wat zijn waarschuwingen in Microsoft Azure?

Waarschuwingen geven u proactief op de hoogte wanneer er problemen met uw infra structuur of toepassing worden gevonden met behulp van uw bewakings gegevens in Azure Monitor. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. 

## <a name="overview"></a>Overzicht

In het onderstaande diagram wordt de stroom van waarschuwingen aangegeven. 

![Diagram van waarschuwings stroom](media/alerts-overview/Azure-Monitor-Alerts.svg)

Waarschuwings regels worden gescheiden van waarschuwingen en de acties die worden uitgevoerd wanneer een waarschuwing wordt geactiveerd. De waarschuwings regel legt het doel en de criteria voor waarschuwingen vast. De waarschuwings regel kan een ingeschakelde of uitgeschakelde status hebben. Waarschuwingen worden alleen geactiveerd wanneer deze functie is ingeschakeld. 

Hieronder vindt u belang rijke kenmerken van een waarschuwings regel:

**Doel resource** : Hiermee definieert u het bereik en de signalen die beschikbaar zijn voor waarschuwingen. Een doel kan elke Azure-resource zijn. Voor beelden van doelen:

- Virtuele machines.
- Opslag accounts.
- Log Analytics-werkruimte.
- Application Insights. 

Voor bepaalde resources, zoals virtuele machines, kunt u meerdere resources opgeven als het doel van de waarschuwings regel.

**Signaal** gegenereerd door de doel resource. Signalen kunnen van de volgende typen zijn: metrisch, activiteiten logboek, Application Insights en logboek.

**Criteria** : een combi natie van signaal en logica die wordt toegepast op een doel bron. Voorbeelden: 

- Percentage CPU > 70%
- Reactie tijd van server > 4 MS 
- Resultaat aantal van een logboek query > 100

**Naam van waarschuwing** : een specifieke naam voor de waarschuwings regel die door de gebruiker is geconfigureerd.

**Beschrijving van waarschuwing** : een beschrijving voor de waarschuwings regel die door de gebruiker is geconfigureerd.

**Ernst** : de ernst van de waarschuwing na de criteria die zijn opgegeven in de waarschuwings regel wordt voldaan. Ernst kan variëren van 0 tot 4.

- Ernst 0 = kritiek
- Ernst 1 = fout
- Ernst 2 = waarschuwing
- Ernst 3 = informatief
- Ernst 4 = uitgebreid 

**Actie** -een specifieke actie die wordt uitgevoerd wanneer de waarschuwing wordt geactiveerd. Zie [actie groepen](../alerts/action-groups.md)voor meer informatie.

## <a name="what-you-can-alert-on"></a>Wat u kunt waarschuwen voor

U kunt een waarschuwing ontvangen over metrische gegevens en Logboeken, zoals beschreven bij het [bewaken van data bronnen](./../agents/data-sources.md). De signalen zijn onder andere, maar zijn niet beperkt tot:

- Metrische waarden
- Query's voor zoeken in logboeken
- Activiteitenlogboekgebeurtenissen
- Status van het onderliggende Azure-platform
- Tests voor de beschikbaarheid van een website

## <a name="manage-alerts"></a>Waarschuwingen beheren

U kunt de status van een waarschuwing instellen om op te geven waar deze zich in het oplossings proces bevindt. Wanneer aan de criteria die zijn opgegeven in de waarschuwings regel wordt voldaan, wordt er een waarschuwing gemaakt of geactiveerd en is de status *Nieuw*. U kunt de status wijzigen wanneer u een waarschuwing bevestigt en wanneer u deze sluit. Alle status wijzigingen worden opgeslagen in de geschiedenis van de waarschuwing.

De volgende waarschuwings statussen worden ondersteund.

| Staat | Beschrijving |
|:---|:---|
| Nieuw | Het probleem is gedetecteerd en nog niet gecontroleerd. |
| Bevestigd | Een beheerder heeft de waarschuwing gecontroleerd en aan het werk gegaan. |
| Gesloten | Het probleem is opgelost. Nadat een waarschuwing is gesloten, kunt u deze opnieuw openen door deze te wijzigen in een andere status. |

De *waarschuwings status* is verschillend en onafhankelijk van de *monitor voorwaarde*. De waarschuwings status wordt ingesteld door de gebruiker. De bewakings voorwaarde is ingesteld door het systeem. Wanneer een waarschuwing wordt geactiveerd, wordt de controle voorwaarde van de waarschuwing ingesteld op *' geactiveerd '* en wanneer de onderliggende voor waarde die de waarschuwing heeft veroorzaakt, is gewist, wordt de monitor voorwaarde ingesteld op *' opgelost '*. 

De status van de waarschuwing wordt niet gewijzigd totdat de gebruiker deze wijzigt. Meer informatie [over het wijzigen van de status van uw waarschuwingen en slimme groepen](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

## <a name="alerts-experience"></a>Waarschuwings ervaring 
De pagina standaard waarschuwingen bevat een samen vatting van waarschuwingen die binnen een bepaald tijds bereik zijn gemaakt. Hier worden de totale waarschuwingen voor elke ernst weer gegeven, met kolommen die het totale aantal waarschuwingen in elke status voor elke Ernst identificeren. Selecteer een van de mogelijke ernst om de pagina [alle waarschuwingen](#all-alerts-page) te openen, gefilterd op die ernst.

In plaats daarvan kunt u [de waarschuwings instanties die op uw abonnementen worden gegenereerd programmatisch opsommen met behulp van rest api's](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  U hebt alleen toegang tot waarschuwingen die in de afgelopen 30 dagen zijn gegenereerd.

Klassieke waarschuwingen worden niet weer gegeven of bijgehouden. U kunt de abonnementen of filter parameters wijzigen om de pagina bij te werken. 

![Scherm afbeelding van de pagina waarschuwingen](media/alerts-overview/alerts-page.png)

U kunt deze weer gave filteren door waarden te selecteren in de vervolg keuzelijsten boven aan de pagina.

| Kolom | Beschrijving |
|:---|:---|
| Abonnement | Selecteer de Azure-abonnementen waarvoor u de waarschuwingen wilt weer geven. U kunt ervoor kiezen om al uw abonnementen te selecteren. Alleen waarschuwingen waarmee u toegang hebt tot de geselecteerde abonnementen, worden opgenomen in de weer gave. |
| Resourcegroep | Selecteer één resource groep. In de weer gave zijn alleen waarschuwingen met doelen in de geselecteerde resource groep opgenomen. |
| Tijdsbereik | Alleen waarschuwingen die binnen het geselecteerde tijds bereik worden geactiveerd, worden opgenomen in de weer gave. Ondersteunde waarden zijn het afgelopen uur, de afgelopen 24 uur, de afgelopen 7 dagen en de afgelopen 30 dagen. |

Selecteer de volgende waarden boven aan de pagina waarschuwingen om een andere pagina te openen:

| Waarde | Beschrijving |
|:---|:---|
| Totaal aantal waarschuwingen | Het totale aantal waarschuwingen dat overeenkomt met de geselecteerde criteria. Selecteer deze waarde om de weer gave alle waarschuwingen zonder filter te openen. |
| Slimme groepen | Het totale aantal slimme groepen dat is gemaakt op basis van de waarschuwingen die overeenkomen met de geselecteerde criteria. Selecteer deze waarde om de lijst met Smart groepen te openen in de weer gave alle waarschuwingen.
| Totale waarschuwings regels | Het totale aantal waarschuwings regels in het geselecteerde abonnement en in de resource groep. Selecteer deze waarde om de regel weergave te openen die is gefilterd op het geselecteerde abonnement en de resource groep.


## <a name="manage-alert-rules"></a>Waarschuwingsregels beheren
Als u de pagina **regels** wilt weer geven, selecteert u **waarschuwings regels beheren**. De pagina regels is één plaats voor het beheren van alle waarschuwings regels in uw Azure-abonnementen. De lijst bevat alle waarschuwings regels en kan worden gesorteerd op basis van doel resources, resource groepen, regel naam of status. U kunt waarschuwings regels ook op deze pagina bewerken, inschakelen of uitschakelen.  

 ![Scherm afbeelding van de pagina met regels](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken
U kunt waarschuwings regels op een consistente manier schrijven, ongeacht de bewakings service of het signaal type.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
U kunt als volgt een nieuwe waarschuwings regel maken:
1. Kies het _doel_ voor de waarschuwing.
1. Selecteer het _signaal_ van de beschik bare signalen voor het doel.
1. Geef de _logica_ op die moet worden toegepast op gegevens uit het signaal.

Voor dit vereenvoudigde ontwerp proces hoeft u niet langer de bewakings bron of signalen te weten die worden ondersteund voordat u een Azure-resource selecteert. De lijst met beschik bare signalen wordt automatisch gefilterd op basis van de doel resource die u selecteert. U kunt ook de logica van de waarschuwings regel automatisch definiëren, op basis van dat doel.  

Meer informatie over het maken van waarschuwings regels vindt u in [waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../alerts/alerts-metric.md).

Er zijn waarschuwingen beschikbaar in verschillende Azure-bewakings Services. Zie [Azure-toepassingen en-resources bewaken](../overview.md)voor meer informatie over hoe en wanneer elk van deze services moet worden gebruikt. 


## <a name="all-alerts-page"></a>Pagina alle waarschuwingen 
Selecteer **Totaal aantal waarschuwingen** om de pagina **alle waarschuwingen** weer te geven. Hier kunt u een lijst weer geven met waarschuwingen die zijn gemaakt in de geselecteerde tijd. U kunt een lijst weer geven van de afzonderlijke waarschuwingen of een lijst van de Smart-groepen die de waarschuwingen bevatten. Selecteer de banner aan de bovenkant van de pagina om tussen de weer gaven te scha kelen.

![Scherm afbeelding van alle pagina waarschuwingen](media/alerts-overview/all-alerts-page.png)

U kunt de weer gave filteren door de volgende waarden te selecteren in de vervolg keuzelijsten boven aan de pagina:

| Kolom | Beschrijving |
|:---|:---|
| Abonnement | Selecteer de Azure-abonnementen waarvoor u de waarschuwingen wilt weer geven. U kunt ervoor kiezen om al uw abonnementen te selecteren. Alleen waarschuwingen waarmee u toegang hebt tot de geselecteerde abonnementen, worden opgenomen in de weer gave. |
| Resourcegroep | Selecteer één resource groep. In de weer gave zijn alleen waarschuwingen met doelen in de geselecteerde resource groep opgenomen. |
| Resourcetype | Selecteer een of meer resource typen. Alleen waarschuwingen met doelen van het geselecteerde type worden opgenomen in de weer gave. Deze kolom is alleen beschikbaar nadat een resource groep is opgegeven. |
| Resource | Selecteer een resource. De weer gave bevat alleen waarschuwingen met die resource als doel. Deze kolom is alleen beschikbaar nadat een resource type is opgegeven. |
| Severity | Selecteer een ernst van de waarschuwing of selecteer **Alles** om waarschuwingen van alle ernst op te neemt. |
| Bewakings voorwaarde | Selecteer een Bewaak voorwaarde of selecteer **Alles** om waarschuwingen van alle voor waarden op te stellen. |
| Waarschuwingsstatus | Selecteer een waarschuwings status of selecteer **Alles** om waarschuwingen van alle statussen op te neemt. |
| Service bewaken | Selecteer een service of selecteer **Alles** om alle services op te laten staan. Er worden alleen waarschuwingen opgenomen die zijn gemaakt door regels die gebruikmaken van de service als doel. |
| Tijdsbereik | Alleen waarschuwingen die binnen het geselecteerde tijds bereik worden geactiveerd, worden opgenomen in de weer gave. Ondersteunde waarden zijn het afgelopen uur, de afgelopen 24 uur, de afgelopen 7 dagen en de afgelopen 30 dagen. |

Selecteer **kolommen** boven aan de pagina om te selecteren welke kolommen u wilt weer geven. 

## <a name="alert-details-page"></a>Pagina waarschuwings Details
Wanneer u een waarschuwing selecteert, geeft deze pagina Details van de waarschuwing en kunt u de status ervan wijzigen.

![Scherm afbeelding van de pagina met waarschuwings Details](media/alerts-overview/alert-detail2.png)

De pagina waarschuwings Details bevat de volgende secties:

| Sectie | Beschrijving |
|:---|:---|
| Samenvatting | Hiermee worden de eigenschappen en andere belang rijke informatie over de waarschuwing weer gegeven. |
| Geschiedenis | Een lijst met alle acties die worden uitgevoerd door de waarschuwing en eventuele wijzigingen aan de waarschuwing. Momenteel beperkt tot status wijzigingen. |
| Diagnostiek | Informatie over de Smart-groep waarin de waarschuwing is opgenomen. Het *aantal meldingen verwijst naar* het aantal waarschuwingen dat is opgenomen in de slimme groep. Bevat ook andere waarschuwingen in dezelfde slimme groep die zijn gemaakt in de afgelopen 30 dagen, ongeacht het tijd filter op de lijst pagina met waarschuwingen. Selecteer een waarschuwing om de details ervan weer te geven. |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>Op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) voor uw waarschuwings instanties

Voor het verbruik en Beheer van waarschuwingsexemplaren moet de gebruiker beschikken over de ingebouwde Azure-rollen [Bewakingsinzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) of [Bewakingslezer](../../role-based-access-control/built-in-roles.md#monitoring-reader). Deze rollen worden met elk Azure Resource Manager-bereik ondersteund, van het abonnementsniveau tot gedetailleerde toewijzingen op het niveau van een resource. Als een gebruiker bijvoorbeeld alleen toegang voor de bewaking van inzenders voor de virtuele machine heeft `ContosoVM1` , kan die gebruiker alleen waarschuwingen gebruiken en beheren die zijn gegenereerd op `ContosoVM1` .

## <a name="manage-your-alert-instances-programmatically"></a>Uw waarschuwings instanties programmatisch beheren

Mogelijk wilt u programmatisch een query uitvoeren op waarschuwingen die zijn gegenereerd op basis van uw abonnement. Query's kunnen bestaan uit het maken van aangepaste weer gaven buiten de Azure Portal, of om uw waarschuwingen te analyseren om patronen en trends te identificeren.

U kunt een query uitvoeren voor waarschuwingen die zijn gegenereerd op basis van uw abonnementen door gebruik te maken van de [Waarschuwingenbeheer rest API](/rest/api/monitor/alertsmanagement/alerts) of door gebruik te maken van de [Azure resource Graph](../../governance/resource-graph/overview.md) en de [rest API voor resources](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources).

Met de resource grafiek REST API voor resources kunt u op schaal een query uitvoeren op waarschuwings exemplaren. Resource grafiek wordt aanbevolen wanneer u waarschuwingen moet beheren die worden gegenereerd over veel abonnementen. 

De volgende voorbeeld aanvraag voor de resource grafiek REST API retourneert het aantal waarschuwingen binnen één abonnement:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

U kunt ook het resultaat van deze resource grafiek query weer geven in de portal met Azure resource Graph Explorer: [Portal.Azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

U kunt een query uitvoeren op de waarschuwingen voor hun [essentiële](../alerts/alerts-common-schema-definitions.md#essentials) velden.

Gebruik de [Waarschuwingenbeheer rest API](/rest/api/monitor/alertsmanagement/alerts) om meer informatie te krijgen over specifieke waarschuwingen, met inbegrip van de context velden van de [waarschuwing](../alerts/alerts-common-schema-definitions.md#alert-context) .

## <a name="smart-groups"></a>Slimme groepen

Slimme groepen zijn aggregaties van waarschuwingen op basis van machine learning-algoritmen, wat kan bijdragen aan waarschuwings lawaai en hulp bij het oplossen van problemen. Meer [informatie over slimme groepen](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) en [het beheren van uw slimme groepen](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over slimme groepen](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Meer informatie over actie groepen](../alerts/action-groups.md)
- [Uw waarschuwings instanties in azure beheren](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Slimme groepen beheren](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Meer informatie over prijzen voor Azure-abonnementen](https://azure.microsoft.com/pricing/details/monitor/)