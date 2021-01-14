---
title: Bron logboek registratie inschakelen in azure Traffic Manager
description: Meer informatie over het inschakelen van bron logboek registratie voor uw Traffic Manager-profiel en het openen van de logboek bestanden die als gevolg hiervan worden gemaakt.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: f24a4a0d982ff78ca4d6726e950825ed2c784e67
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184538"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Bron logboek registratie inschakelen in azure Traffic Manager

In dit artikel wordt beschreven hoe u het verzamelen van diagnostische resource Logboeken kunt inschakelen voor een Traffic Manager profiel.

Azure Traffic Manager-resource logboeken kunnen inzicht geven in het gedrag van de Traffic Manager-profiel bron. U kunt bijvoorbeeld de logboek gegevens van het profiel gebruiken om te bepalen waarom er een time-out optreedt voor individuele tests op een eind punt.

## <a name="enable-resource-logging"></a>Resourcelogboekregistratie inschakelen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/powershell), of door Power shell uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u Power shell vanaf uw computer uitvoert, hebt u de Azure PowerShell module 1.0.0 of hoger nodig. U kunt uitvoeren `Get-Module -ListAvailable Az` om de geïnstalleerde versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u Power shell lokaal uitvoert, moet u ook uitvoeren `Login-AzAccount` om u aan te melden bij Azure.

1. **Het Traffic Manager profiel ophalen:**

    Als u de bron logboek registratie wilt inschakelen, hebt u de ID van een Traffic Manager profiel nodig. Haal het Traffic Manager profiel op waarvoor u bron logboek registratie wilt inschakelen met [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). De uitvoer bevat de ID-gegevens van het Traffic Manager profiel.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Bron logboek registratie inschakelen voor het Traffic Manager profiel:**

    Schakel de bron logboek registratie voor het Traffic Manager profiel in met behulp van de ID die in de vorige stap is verkregen met [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Met de volgende opdracht worden uitgebreide logboeken voor het Traffic Manager profiel opgeslagen in een opgegeven Azure Storage-account. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Controleer de diagnostische instellingen:**

      Controleer de diagnostische instellingen voor het Traffic Manager profiel met behulp van [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Met de volgende opdracht worden de categorieën weer gegeven die zijn geregistreerd voor een resource.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Zorg ervoor dat alle logboek categorieën die zijn gekoppeld aan de bron van het Traffic Manager-profiel, worden weer gegeven als ingeschakeld. Controleer ook of het opslag account correct is ingesteld.

## <a name="access-log-files"></a>Toegang tot logboek bestanden
1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Navigeer naar uw Azure Storage-account in de portal.
2. Klik op de pagina **overzicht** van uw Azure Storage-account onder **Services** op **blobs**.
3. Voor **containers** selecteert u **Insights-logs-probehealthstatusevents** en navigeert u naar de PT1H.jsin het bestand en klikt u op **downloaden** om een kopie van dit logboek bestand te downloaden en op te slaan.

    ![Toegang tot logboek bestanden van uw Traffic Manager profiel vanuit een Blob-opslag](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager-logboek schema

Alle bron logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden. Zie [ondersteunde services, schema's en categorieën voor Azure-resource logboeken](../azure-monitor/platform/resource-logs-schema.md)voor het schema voor bron logboeken op het hoogste niveau.

De volgende tabel bevat logboeken die specifiek zijn voor de Azure Traffic Manager-profiel bron.

|Veldnaam|Veldtype|Definitie|Voorbeeld|
|----|----|---|---|
|EndpointName|Tekenreeks|De naam van het Traffic Manager-eind punt waarvan de integriteits status wordt vastgelegd.|*myPrimaryEndpoint*|
|Status|Tekenreeks|De integriteits status van het Traffic Manager-eind punt dat is gecontroleerd. De status **kan een of meer** zijn **.**|**Omhoog**|
|||||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Traffic Manager bewaking](traffic-manager-monitoring.md)