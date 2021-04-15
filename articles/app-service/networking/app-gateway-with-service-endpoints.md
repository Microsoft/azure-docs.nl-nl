---
title: Application Gateway integratie met service-eindpunten - Azure App Service | Microsoft Docs
description: Beschrijft hoe Application Gateway integreert met Azure App Service beveiligd met service-eindpunten.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f1d517ba37bbef95d1863485c8c3b6313f196c11
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374910"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway integratie met service-eindpunten
Er zijn drie variaties van App Service waarvoor een iets andere configuratie van de integratie met Azure Application Gateway. De variaties omvatten reguliere App Service, ook wel bekend als multiten tenant, Interne Load Balancer (ILB) App Service Environment (ASE) en Externe AS-omgeving. In dit artikel wordt beschreven hoe u deze configureert met App Service (meerdere tenants) en worden overwegingen besproken over ILB en externe AS-omgeving.

## <a name="integration-with-app-service-multi-tenant"></a>Integratie met App Service (multi-tenant)
App Service (meerdere tenants) heeft een openbaar eindpunt op internet. Met [behulp van service-eindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md) kunt u verkeer van een specifiek subnet binnen een Azure-Virtual Network en de rest blokkeren. In het volgende scenario gebruiken we deze functionaliteit om ervoor te zorgen dat een App Service alleen verkeer van een specifiek Application Gateway ontvangen.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Diagram toont het internet dat naar een Application Gateway in een Azure-Virtual Network stroomt en van daaruit via een firewallpictogram naar exemplaren van apps in App Service.":::

Deze configuratie bestaat uit twee delen naast het maken van de App Service en de Application Gateway. Het eerste deel bestaat uit het inschakelen van service-eindpunten in het subnet van de Virtual Network waar Application Gateway is geïmplementeerd. Service-eindpunten zorgen ervoor dat al het netwerkverkeer dat het subnet verlaat naar App Service wordt getagd met de specifieke subnet-id. Het tweede deel bestaat uit het instellen van een toegangsbeperking van de specifieke web-app om ervoor te zorgen dat alleen verkeer met deze specifieke subnet-id is toegestaan. U kunt deze configureren met behulp van verschillende hulpprogramma's, afhankelijk van de voorkeur.

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Met Azure Portal volgt u vier stappen voor het inrichten en configureren van de installatie. Als u bestaande resources hebt, kunt u de eerste stappen overslaan.
1. Maak een App Service met behulp van een van de quickstarts in de App Service documentatie, bijvoorbeeld [.NET Core Quickstart](../quickstart-dotnetcore.md)
2. Maak een Application Gateway met behulp van de [quickstart van](../../application-gateway/quick-create-portal.md)de portal, maar sla de sectie Back-eindedoelen toevoegen over.
3. Configureer [App Service als een back-Application Gateway in](../../application-gateway/configure-web-app-portal.md), maar sla de sectie Toegang beperken over.
4. Maak ten slotte de [toegangsbeperking met behulp van service-eindpunten](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

U hebt nu toegang tot de App Service via Application Gateway, maar als u rechtstreeks toegang probeert te krijgen tot de App Service, ontvangt u een 403 HTTP-fout die aangeeft dat de website is gestopt.

![Schermopname van de tekst van fout 403 - Verboden.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
Met [Resource Manager implementatiesjabloon][template-app-gateway-app-service-complete] wordt een volledig scenario ingericht. Het scenario bestaat uit een App Service-exemplaar dat is vergrendeld met service-eindpunten en toegangsbeperking om alleen verkeer van Application Gateway. De sjabloon bevat veel Smart Defaults en unieke postfixes die zijn toegevoegd aan de resourcenamen om eenvoudig te zijn. Als u deze wilt overschrijven, moet u de repo klonen of de sjabloon downloaden en bewerken. 

Als u de sjabloon wilt toepassen, kunt u de knop Implementeren in Azure gebruiken in de beschrijving van de sjabloon, of u kunt de juiste PowerShell/CLI gebruiken.

## <a name="using-azure-command-line-interface"></a>Azure-opdrachtregelinterface gebruiken
Met [het Azure CLI-voorbeeld](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) wordt een App Service vergrendeld met service-eindpunten en toegangsbeperking om alleen verkeer van Application Gateway. Als u alleen verkeer naar een bestaande App Service van een bestaande Application Gateway wilt isoleren, is de volgende opdracht voldoende.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

In de standaardconfiguratie zorgt de opdracht ervoor dat zowel de configuratie van het service-eindpunt in het subnet als de toegangsbeperking in de App Service.

## <a name="considerations-for-ilb-ase"></a>Overwegingen voor ILB ASE
ILB ASE is niet blootgesteld aan internet en verkeer tussen het exemplaar en een Application Gateway is daarom al geïsoleerd voor de Virtual Network. In de [volgende handleiding wordt een](../environment/integrate-with-application-gateway.md) ILB ASE geconfigureerd en geïntegreerd met een Application Gateway met behulp van Azure Portal. 

Als u ervoor wilt zorgen dat alleen verkeer van het Application Gateway-subnet de ASE bereikt, kunt u een netwerkbeveiligingsgroep (NSG) configureren die van invloed is op alle web-apps in de AS-omgeving. Voor de NSG kunt u het IP-bereik van het subnet en eventueel de poorten (80/443) opgeven. Zorg ervoor dat u de vereiste [NSG-regels](../environment/network-info.md#network-security-groups) voor ASE niet overschrijven om correct te werken.

Als u verkeer wilt isoleren naar een afzonderlijke web-app, moet u op IP gebaseerde toegangsbeperkingen gebruiken, omdat service-eindpunten niet werken voor ASE. Het IP-adres moet het privé-IP-adres van het Application Gateway zijn.

## <a name="considerations-for-external-ase"></a>Overwegingen voor externe ASE
Externe ASE heeft een openbaar load balancer zoals multi-tenant App Service. Service-eindpunten werken niet voor ASE en daarom moet u ip-toegangsbeperkingen gebruiken met behulp van het openbare IP-adres van het Application Gateway-exemplaar. Volg deze [quickstart](../environment/create-external-ase.md) om een externe AS-Azure Portal maken met behulp van de Azure Portal

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager sjabloon voor het volledige scenario"

## <a name="considerations-for-kuduscm-site"></a>Overwegingen voor kudu/scm-site
De scm-site, ook wel kudu genoemd, is een beheersite die voor elke web-app bestaat. Het is niet mogelijk om de proxy van de SCM-site om te draaien en u wilt deze waarschijnlijk ook vergrendelen voor afzonderlijke IP-adressen of een specifiek subnet.

Als u dezelfde toegangsbeperkingen wilt gebruiken als de hoofdsite, kunt u de instellingen overnemen met behulp van de volgende opdracht.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Als u afzonderlijke toegangsbeperkingen wilt instellen voor de SCM-site, kunt u toegangsbeperkingen toevoegen met behulp van de vlag --scm-site, zoals hieronder wordt weergegeven.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Volgende stappen
Zie de App Service Environment voor [App Service Environment informatie.](/azure/app-service/environment)

Als u uw web-app verder wilt beveiligen, vindt u Web Application Firewall informatie over Application Gateway web-app in [Azure Web Application Firewall documentatie.](../../web-application-firewall/ag/ag-overview.md)
