---
title: Toegangs beperkingen Azure App Service
description: Meer informatie over het beveiligen van uw app in Azure App Service door toegangs beperkingen in te stellen.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fea189952b1452c680255ceb99e38609775a8bd6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502685"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Toegangs beperkingen voor Azure App Service instellen

Door toegangs beperkingen in te stellen, kunt u een lijst met voor rang gegeven toestaan/weigeren definiëren die de netwerk toegang tot uw app beheert. De lijst kan IP-adressen of Azure Virtual Network subnetten bevatten. Als er sprake is van een of meer vermeldingen, bestaat er al een impliciete *weigering* aan het einde van de lijst.

De functie voor toegangs beperking werkt met alle door Azure App Service gehoste workloads. De werk belastingen kunnen Web apps, API apps, Linux-apps, Linux-container-apps en-functies bevatten.

Wanneer er een aanvraag wordt ingediend bij uw app, wordt het van-adres geëvalueerd op basis van de regels in de lijst met toegangs restricties. Als het van-adres zich in een subnet bevindt dat is geconfigureerd met Service-eind punten naar micro soft. Web, wordt het bron-subnet vergeleken met de regels voor het virtuele netwerk in de lijst met toegangs restricties. Als het adres niet is toegestaan op basis van de regels in de lijst, reageert de service met een [HTTP 403-](https://en.wikipedia.org/wiki/HTTP_403) status code.

De functie voor toegangs beperking wordt geïmplementeerd in de front-end rollen van App Service, die een upstream zijn van de werk nemers waar de code wordt uitgevoerd. Toegangs beperkingen zijn daarom effectief op het netwerk toegangscontrole lijsten (Acl's).

De mogelijkheid om de toegang tot uw web-app vanuit een virtueel Azure-netwerk te beperken, is ingeschakeld door [service-eind punten][serviceendpoints]. Met Service-eind punten kunt u de toegang beperken tot een multi tenant-service van geselecteerde subnetten. Het is niet mogelijk om het verkeer te beperken tot apps die worden gehost in een App Service Environment. Als u een App Service Environment hebt, kunt u de toegang tot uw app beheren door IP-adres regels toe te passen.

> [!NOTE]
> De service-eind punten moeten beide zijn ingeschakeld op de netwerk zijde en voor de Azure-service waarmee ze worden ingeschakeld. Zie [Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)voor een lijst met Azure-Services die service-eind punten ondersteunen.
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagram van de stroom van toegangs beperkingen.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Toegangs beperkings regels beheren in de portal

Ga als volgt te werk om een toegangs beperkings regel toe te voegen aan uw app:

1. Meld u aan bij de Azure-portal.

1. Selecteer **netwerken** in het linkerdeel venster.

1. Selecteer in het deel venster **netwerken** onder **toegangs beperkingen** de optie **toegangs beperkingen configureren**.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Scherm opname van het deel venster met netwerk opties App Service in de Azure Portal.":::

1. Bekijk op de pagina **toegangs beperkingen** de lijst met toegangs beperkings regels die voor uw app zijn gedefinieerd.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Scherm afbeelding van de pagina toegangs beperkingen in de Azure Portal, waarin de lijst met toegangs beperkings regels wordt weer gegeven die zijn gedefinieerd voor de geselecteerde app.":::

   In de lijst worden alle huidige beperkingen weer gegeven die worden toegepast op de app. Als u een beperking voor het virtuele netwerk hebt voor uw app, wordt in de tabel aangegeven of de service-eind punten zijn ingeschakeld voor micro soft. Web. Als er geen beperkingen zijn gedefinieerd voor uw app, is de app vanaf elke locatie toegankelijk.

### <a name="add-an-access-restriction-rule"></a>Een toegangs beperkings regel toevoegen

Als u een toegangs beperkings regel wilt toevoegen aan uw app, selecteert u **regel toevoegen** in het deel venster **toegangs beperkingen** . Nadat u een regel hebt toegevoegd, wordt deze onmiddellijk van kracht. 

Regels worden in volg orde van prioriteit afgedwongen, te beginnen met het laagste getal in de kolom **prioriteit** . Een impliciete *weigering* is van kracht nadat u zelfs één regel hebt toegevoegd.

Ga als volgt te werk in het deel venster **toegangs beperking toevoegen** wanneer u een regel maakt:

1. Selecteer onder **actie** de optie **toestaan** of **weigeren**.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Scherm opname van het deel venster ' toegangs beperking toevoegen '.":::

1. Voer eventueel een naam en beschrijving van de regel in.
1. Voer in het vak **prioriteit** een prioriteits waarde in.
1. Selecteer in de vervolg keuzelijst **type** het type regel.

De verschillende typen regels worden beschreven in de volgende secties.

> [!NOTE]
> - Er geldt een limiet van 512 toegangs beperkings regels. Als u meer dan 512 toegangs beperkings regels nodig hebt, raden we u aan om een zelfstandig beveiligings product te installeren, zoals Azure front deur, Azure-app gateway of een alternatieve WAF.
>
#### <a name="set-an-ip-address-based-rule"></a>Een op IP-adres gebaseerde regel instellen

Volg de procedure zoals beschreven in de vorige sectie, maar met de volgende toevoeging:
* Selecteer voor stap 4 in de vervolg keuzelijst **type** de optie **IPv4** of **IPv6**. 

Geef het **IP-adres blok** op in de CIDR-notatie (classable Inter-Domain Routing) voor zowel IPv4-als IPv6-adressen. Als u een adres wilt opgeven, kunt u iets zoals *1.2.3.4/32* gebruiken, waarbij de eerste vier octetten uw IP-adres vertegenwoordigen en */32* het masker is. De IPv4 CIDR-notatie voor alle adressen is 0.0.0.0/0. Zie [klasseloze Inter-Domain route ring](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)voor meer informatie over CIDR-notatie. 

#### <a name="set-a-service-endpoint-based-rule"></a>Een regel op basis van een service-eind punt instellen

* Selecteer voor stap 4 in de vervolg keuzelijst **type** de optie **Virtual Network**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Scherm afbeelding van het deel venster beperking toevoegen met het Virtual Network type geselecteerd.":::

Geef de vervolg keuzelijst **abonnement**, **Virtual Network** en **subnet** op die overeenkomen met wat u de toegang wilt beperken.

Door service-eind punten te gebruiken, kunt u de toegang beperken tot geselecteerde subnetten van het virtuele netwerk van Azure. Als service-eind punten niet al zijn ingeschakeld met micro soft. web voor het geselecteerde subnet, worden ze automatisch ingeschakeld, tenzij u het selectie vakje **ontbrekende micro soft. webservice-eind punten negeren** inschakelt. Het scenario waarin u mogelijk service-eind punten in de app wilt inschakelen, maar niet het subnet is afhankelijk van het feit of u gemachtigd bent om deze in te scha kelen op het subnet. 

Als iemand anders de service-eind punten in het subnet moet inschakelen, schakelt u het selectie vakje **ontbrekende micro soft. web service-eind punten negeren** in. Uw app wordt geconfigureerd voor service-eind punten in de verwachting dat ze later in het subnet worden ingeschakeld. 

U kunt Service-eind punten niet gebruiken om de toegang te beperken tot apps die worden uitgevoerd in een App Service Environment. Als uw app zich in een App Service Environment bevindt, kunt u de toegang hiertoe beheren door IP-toegangs regels toe te passen. 

Met Service-eind punten kunt u uw app configureren met toepassings gateways of andere Web Application Firewall (WAF)-apparaten. U kunt ook toepassingen met meerdere lagen configureren met beveiligde back-ends. Zie [netwerk functies en app service](networking-features.md) en [Application Gateway integratie met Service-eind punten](networking/app-gateway-with-service-endpoints.md)voor meer informatie.

> [!NOTE]
> - Service-eind punten worden momenteel niet ondersteund voor web-apps die gebruikmaken van IP-Secure Sockets Layer (VIP) voor virtueel IP-verkeer.
>
#### <a name="set-a-service-tag-based-rule-preview"></a>Een op code gebaseerde regel voor een service instellen (preview-versie)

* Voor stap 4 selecteert u in de vervolg keuzelijst **type** de optie **service label (voor beeld)**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png" alt-text="Scherm afbeelding van het deel venster beperking toevoegen met het servicetag type geselecteerd.":::

Elke servicetag vertegenwoordigt een lijst met IP-adresbereiken van Azure-Services. Een lijst met deze services en koppelingen naar de specifieke bereiken vindt u in de documentatie voor de [service label][servicetags].

De volgende lijst met Service tags wordt ondersteund in de toegangs beperkings regels tijdens de preview-fase:
* ActionGroup
* AzureCloud
* AzureCognitiveSearch
* AzureConnectors
* AzureEventGrid
* AzureFrontDoor. back-end
* AzureMachineLearning
* AzureSignalR
* AzureTrafficManager
* LogicApps
* ServiceFabric

### <a name="edit-a-rule"></a>Een regel bewerken

1. Als u wilt beginnen met het bewerken van een bestaande beperkings regel voor toegang, selecteert u op de pagina **toegangs beperkingen** de regel die u wilt bewerken.

1. Breng in het deel venster **toegangs beperking bewerken** de gewenste wijzigingen aan en selecteer **regel bijwerken**. Bewerkingen zijn direct van kracht, met inbegrip van wijzigingen in de volg orde van prioriteit.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Scherm afbeelding van het deel venster ' toegangs beperking bewerken ' in de Azure Portal, waarin de velden voor een bestaande toegangs beperkings regel worden weer gegeven.":::

   > [!NOTE]
   > Wanneer u een regel bewerkt, kunt u niet scha kelen tussen regel typen. 

### <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel wilt verwijderen, selecteert u op de pagina **toegangs beperkingen** het beletsel teken (**...**) naast de regel die u wilt verwijderen en selecteert u vervolgens **verwijderen**.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Scherm afbeelding van de pagina toegangs beperkingen, met het weglatings teken naast de regel voor toegangs beperking die moet worden verwijderd.":::

## <a name="access-restriction-advanced-scenarios"></a>Geavanceerde scenario's voor toegangs beperking
In de volgende secties worden een aantal geavanceerde scenario's beschreven die gebruikmaken van toegangs beperkingen.
### <a name="block-a-single-ip-address"></a>Eén IP-adres blok keren

Wanneer u uw eerste toegangs beperkings regel toevoegt, voegt de service een expliciete regel voor het *weigeren van alle* regels toe met de prioriteit 2147483647. In de praktijk is de regel expliciet *Alles weigeren* de laatste regel die moet worden uitgevoerd en blokkeert deze de toegang tot een IP-adres dat niet expliciet is toegestaan door een regel voor *toestaan* .

Voor een scenario waarbij u een enkel IP-adres of een blok met IP-adressen expliciet wilt blok keren, maar toegang tot alle andere wilt toestaan, voegt u een expliciete regel voor *toestaan* toe.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Scherm afbeelding van de pagina toegangs beperkingen in de Azure Portal, waarbij één geblokkeerd IP-adres wordt weer gegeven.":::

### <a name="restrict-access-to-an-scm-site"></a>Toegang tot een SCM-site beperken 

Naast het beheren van de toegang tot uw app, kunt u de toegang beperken tot de SCM-site die wordt gebruikt door uw app. De SCM-site is zowel het Web Deploy-eind punt als de kudu-console. U kunt toegangs beperkingen voor de SCM-site afzonderlijk toewijzen vanuit de app of dezelfde set beperkingen voor zowel de app als de SCM-site gebruiken. Wanneer u **dezelfde beperkingen als \<app name>** selectie vakje inschakelt, wordt alles leeg gelaten. Als u het selectie vakje uitschakelt, worden de instellingen van uw SCM-site opnieuw toegepast. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Scherm afbeelding van de pagina toegangs beperkingen in de Azure Portal, waarin wordt weer gegeven dat er geen toegangs beperkingen zijn ingesteld voor de SCM-site of de app.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance-preview"></a>Toegang tot een specifiek exemplaar van de Azure-front-deur beperken (preview-versie)
Verkeer van de voor deur van Azure naar uw toepassing is afkomstig uit een bekende set IP-bereiken die zijn gedefinieerd in het label AzureFrontDoor. back-service. Met een beperkings regel voor service tags kunt u het verkeer beperken tot alleen de herkomst van de voor deur van Azure. Om ervoor te zorgen dat verkeer alleen afkomstig is uit uw specifieke exemplaar, moet u de binnenkomende aanvragen verder filteren op basis van de unieke http-header die door Azure front-deur wordt verzonden. Tijdens de preview kunt u dit doen met Power shell of REST/ARM. 

* Power shell-voor beeld (de voor deur-ID vindt u in de Azure Portal):

   ```azurepowershell-interactive
    $frontdoorId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
      -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
      -HttpHeader @{'x-azure-fdid' = $frontdoorId}
    ```
## <a name="manage-access-restriction-rules-programmatically"></a>Toegangs beperkings regels programmatisch beheren

U kunt toegangs beperkingen programmatisch toevoegen door een van de volgende handelingen uit te voeren: 

* Gebruik [de Azure cli](/cli/azure/webapp/config/access-restriction). Bijvoorbeeld:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Gebruik [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule). Bijvoorbeeld:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > Voor het werken met Service Tags, HTTP-headers of regels met meerdere bronnen is ten minste versie 5.1.0 vereist. U kunt de versie van de geïnstalleerde module controleren met: **Get-InstalledModule-name AZ**

U kunt waarden ook hand matig instellen door een van de volgende handelingen uit te voeren:

* Gebruik een [Azure rest API](/rest/api/azure/) put-bewerking in de app-configuratie in azure Resource Manager. De locatie voor deze informatie in Azure Resource Manager is:

  management.azure.com/subscriptions/**-abonnements-id**/resourceGroups/**resource groepen**/providers/Microsoft.web/sites/**Web-app-naam**/config/Web? API-Version = 2020-06-01

* Gebruik een resource manager-sjabloon. U kunt bijvoorbeeld resources.azure.com gebruiken en het ipSecurityRestrictions-blok bewerken om de vereiste JSON toe te voegen.

  De JSON-syntaxis voor het vorige voor beeld is:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  De JSON-syntaxis voor een geavanceerd voor beeld met de beperking service label en http-header is:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Toegangs beperkingen voor Azure Functions instellen

Er zijn ook toegangs beperkingen beschikbaar voor functie-apps met dezelfde functionaliteit als App Service-abonnementen. Wanneer u toegangs beperkingen inschakelt, schakelt u ook de Azure Portal code-editor uit voor niet-toegestane IP-adressen.

## <a name="next-steps"></a>Volgende stappen
[Toegangs beperkingen voor Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integratie met Service-eind punten Application Gateway](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md