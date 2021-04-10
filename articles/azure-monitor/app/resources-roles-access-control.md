---
title: Resources, rollen en toegangs beheer in Azure-toepassing inzichten | Microsoft Docs
description: Eigen aren, mede werkers en lezers van de inzichten van uw organisatie.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4254170ad100b634439cc846dc381e305ae71d1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589594"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Resources, rollen en toegangs beheer in Application Insights

U kunt bepalen wie toegang heeft tot uw gegevens in azure [Application Insights][start]lezen en bijwerken met behulp van [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Wijs toegang toe aan gebruikers in de **resource groep of het abonnement** waartoe de resource van de toepassing behoort, niet in de resource zelf. Wijs de rol van de **Application Insights onderdeel bijdrager** toe. Dit zorgt voor eenvormige controle van de toegang tot webtests en waarschuwingen samen met uw toepassings bron. [Meer informatie](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Resources, groepen en abonnementen

Eerst worden enkele definities:

* **Resource** : een exemplaar van een Microsoft Azure service. Uw Application Insights resource verzamelt, analyseert en toont de telemetriegegevens die vanuit uw toepassing zijn verzonden.  Andere typen Azure-resources zijn web-apps, data bases en virtuele machines.
  
    Als u uw resources wilt zien, opent u de [Azure Portal][portal], meldt u zich aan en klikt u op alle resources. Als u een resource wilt zoeken, typt u een deel van de naam in het veld Filter.
  
    ![Lijst met Azure-resources](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Resource groep**][group] : elke resource behoort tot één groep. Een groep is een handige manier om gerelateerde resources te beheren, met name voor toegangs beheer. Zo kunt u bijvoorbeeld in één resource groep een web-app, een Application Insights-resource voor het bewaken van de app en een opslag Resource plaatsen om geëxporteerde gegevens te bewaren.

* [**Abonnement**](https://portal.azure.com) : als u Application Insights of andere Azure-resources wilt gebruiken, meldt u zich aan bij een Azure-abonnement. Elke resource groep behoort tot één Azure-abonnement, waar u uw prijs pakket kiest en als het een organisatie-abonnement is, kiest u de leden en hun toegangs machtigingen.
* [**Microsoft-account**][account] : de gebruikers naam en het wacht woord waarmee u zich aanmeldt bij Microsoft Azure abonnementen, Xbox Live, Outlook.com en andere micro soft-Services.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> Toegang beheren in de resource groep

Het is belang rijk om te begrijpen dat naast de resource die u hebt gemaakt voor uw toepassing, ook afzonderlijke verborgen resources voor waarschuwingen en webtests worden weer gegeven. Ze zijn gekoppeld aan dezelfde [resource groep](#resource-group) als uw Application Insights-resource. Mogelijk hebt u ook andere Azure-Services in daar geplaatst, zoals websites of opslag.

## <a name="to-provide-access-to-another-user"></a>Om toegang te verlenen aan een andere gebruiker

U moet eigenaars rechten hebben voor het abonnement of de resource groep.

De gebruiker moet een [micro soft-account][account]hebben of toegang hebben tot het [micro soft-account](../../active-directory/fundamentals/sign-up-organization.md)van hun organisatie. U kunt toegang verlenen aan personen en ook aan gebruikers groepen die zijn gedefinieerd in Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navigeer naar een resource groep of rechtstreeks naar de resource zelf

Kies **toegangs beheer (IAM)** in het menu aan de linkerkant.

![Scherm afbeelding van de knop toegangs beheer in Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Selecteer **Roltoewijzing toevoegen**

![Scherm afbeelding van het toegangs beheer menu met de knop toevoegen gemarkeerd in rood](./media/resources-roles-access-control/0002-add.png)

De onderstaande **machtigingen weergave toevoegen** is voornamelijk specifiek voor Application Insights resources, als u de machtigingen voor toegangs beheer van een hoger niveau als resource groepen hebt bekeken, ziet u aanvullende niet-Application Insights gerichte rollen.

Als u informatie wilt weer geven over alle ingebouwde rollen van op rollen gebaseerd toegangs beheer van Azure, gebruikt u de [officiële referentie-inhoud](../../role-based-access-control/built-in-roles.md).

![Scherm afbeelding van de lijst met gebruikers rollen in toegangs beheer](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Een rol selecteren

Waar van toepassing, gaan we een koppeling naar de bijbehorende officiële referentie documentatie.

| Rol | In de resource groep |
| --- | --- |
| [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) |Kan alles wijzigen, met inbegrip van gebruikers toegang. |
| [Inzender](../../role-based-access-control/built-in-roles.md#contributor) |Kan alles bewerken, inclusief alle resources. |
| [Inzender voor Application Insights onderdelen](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Kan Application Insights resources bewerken. |
| [Lezer](../../role-based-access-control/built-in-roles.md#reader) |Kan weer geven, maar niet wijzigen. |
| [Application Insights Snapshot Debugger](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | Hiermee krijgt de gebruiker toestemming om Application Insights Snapshot Debugger-functies te gebruiken. Houd er rekening mee dat deze rol is opgenomen in de rollen eigenaar en Inzender. |
| Inzender voor Azure service Deploy release management | Rol van Inzender voor services die worden geïmplementeerd via Azure service Deploy. |
| [Gegevens opschoner](../../role-based-access-control/built-in-roles.md#data-purger) | Speciale rol voor het leegmaken van persoonlijke gegevens. Bekijk onze [richt lijnen voor persoonlijke gegevens](../logs/personal-data-mgmt.md) voor meer informatie.   |
| ExpressRoute-beheerder | Kan snelle routes maken en verwijderen.|
| [Inzender van Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources.  |
| [Lezer van Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | Log Analytics Reader kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. |
| masterreader | Hiermee kan een gebruiker alles weer geven, maar geen wijzigingen aanbrengen. |
| [Inzender bewaken](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | Kan alle bewakings gegevens lezen en controle-instellingen bijwerken.|
| [De uitgever van metrische gegevens controleren](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources. |
| [Bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) | Kan alle controle gegevens lezen. |
| Inzender voor resource beleid (preview-versie) | Alsnog gebruikers van EA, met rechten voor het maken/wijzigen van het resource beleid, het maken van een ondersteunings ticket en het lezen van resources/hiërarchie.  |
| [Beheerder van gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) | Hiermee kan een gebruiker de toegang tot Azure-resources beheren voor andere gebruikers.|
| [Website bijdrager](../../role-based-access-control/built-in-roles.md#website-contributor) | Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites.|

' Bewerken ' omvat het maken, verwijderen en bijwerken:

* Resources
* Webtests
* Waarschuwingen
* Continue export

#### <a name="select-the-user"></a>De gebruiker selecteren

Als de gebruiker die u wilt, niet in de adres lijst staat, kunt u iedereen uitnodigen met een Microsoft-account.
(Als ze gebruikmaken van services zoals Outlook.com, OneDrive, Windows Phone of XBox Live, hebben ze een Microsoft-account.)

## <a name="related-content"></a>Gerelateerde inhoud

* [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Power shell-query voor het bepalen van het rollidmaatschap

Omdat bepaalde rollen kunnen worden gekoppeld aan meldingen en e-mail waarschuwingen, kan het handig zijn om een lijst met gebruikers te genereren die tot een bepaalde rol behoren. Voor hulp bij het genereren van deze typen lijsten bieden we de volgende voorbeeld query's die kunnen worden aangepast aan uw specifieke behoeften:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Het hele abonnement voor beheerders rollen + Inzender rollen opvragen

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Query's uitvoeren in de context van een specifieke Application Insights resource voor eigen aren en mede werkers

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Query's uitvoeren in de context van een specifieke resource groep voor eigen aren en mede werkers

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md
