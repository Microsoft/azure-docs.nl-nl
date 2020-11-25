---
title: Toegangs beperkingen Azure App Service
description: Meer informatie over het beveiligen van uw app in Azure App Service door toegangs beperkingen in te stellen.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010177"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Toegangs beperkingen voor Azure App Service instellen

Door toegangs beperkingen in te stellen, kunt u een lijst met voor rang gegeven toestaan/weigeren definiëren die de netwerk toegang tot uw app beheert. De lijst kan IP-adressen of Azure Virtual Network subnetten bevatten. Als er sprake is van een of meer vermeldingen, bestaat er al een impliciete *weigering* aan het einde van de lijst.

De mogelijkheid tot toegang beperken werkt met alle door Azure App Service gehoste workloads. De werk belastingen kunnen Web apps, API apps, Linux-apps, Linux-container-apps en-functies bevatten.

Wanneer een aanvraag wordt gedaan voor uw app, wordt het van-adres geëvalueerd op basis van de IP-adres regels in uw lijst met toegangs beperkingen. Als het van-adres zich in een subnet bevindt dat is geconfigureerd met Service-eind punten naar micro soft. Web, wordt het bron-subnet vergeleken met de regels voor virtuele netwerken in uw lijst met toegangs beperkingen. Als het adres niet is toegestaan op basis van de regels in de lijst, reageert de service met een [HTTP 403-](https://en.wikipedia.org/wiki/HTTP_403) status code.

De functie voor toegangs beperking wordt geïmplementeerd in de front-end rollen van App Service, die een upstream zijn van de werk nemers waar de code wordt uitgevoerd. Toegangs beperkingen zijn daarom effectief op het netwerk toegangscontrole lijsten (Acl's).

De mogelijkheid om de toegang tot uw web-app vanuit een virtueel Azure-netwerk te beperken, is ingeschakeld door [service-eind punten][serviceendpoints]. Met Service-eind punten kunt u de toegang beperken tot een multi tenant-service van geselecteerde subnetten. Het is niet mogelijk om het verkeer te beperken tot apps die worden gehost in een App Service Environment. Als u een App Service Environment hebt, kunt u de toegang tot uw app beheren door IP-adres regels toe te passen.

> [!NOTE]
> De service-eind punten moeten beide zijn ingeschakeld op de netwerk zijde en voor de Azure-service waarmee ze worden ingeschakeld. Zie [Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)voor een lijst met Azure-Services die service-eind punten ondersteunen.
>

![Diagram van de stroom van toegangs beperkingen.](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>Toegangs beperkings regels toevoegen of bewerken in de portal

Ga als volgt te werk om een regel voor toegangs beperking toe te voegen aan uw app:

1. Meld u aan bij de Azure-portal.

1. Selecteer **netwerken** in het linkerdeel venster.

1. Selecteer in het deel venster **netwerken** onder **toegangs beperkingen** de optie **toegangs beperkingen configureren**.

   ![Scherm opname van het deel venster met netwerk opties App Service in de Azure Portal.](media/app-service-ip-restrictions/access-restrictions.png)  

1. Bekijk op de pagina **toegangs beperkingen** de lijst met toegangs beperkings regels die voor uw app zijn gedefinieerd.

   ![Scherm afbeelding van de pagina toegangs beperkingen in de Azure Portal, waarin de lijst met toegangs beperkings regels wordt weer gegeven die zijn gedefinieerd voor de geselecteerde app.](media/app-service-ip-restrictions/access-restrictions-browse.png)

   In de lijst worden alle huidige beperkingen weer gegeven die worden toegepast op de app. Als u een virtuele-netwerk beperking hebt voor uw app, wordt in de tabel aangegeven of de service-eind punten zijn ingeschakeld voor micro soft. Web. Als er geen beperkingen zijn gedefinieerd voor uw app, is de app vanaf elke locatie toegankelijk.  

### <a name="add-an-access-restriction-rule"></a>Een toegangs beperkings regel toevoegen

Als u een regel voor toegangs beperking wilt toevoegen aan uw app, selecteert u **regel toevoegen** in het deel venster **toegangs beperkingen** . Nadat u een regel hebt toegevoegd, wordt deze onmiddellijk van kracht. 

Regels worden in volg orde van prioriteit afgedwongen, te beginnen met het laagste getal in de kolom **prioriteit** . Een impliciete *weigering* is van kracht nadat u zelfs één regel hebt toegevoegd.

Ga als volgt te werk in het deel venster **IP-beperking toevoegen** wanneer u een regel maakt:

1. Selecteer onder **actie** de optie **toestaan** of **weigeren**.  

   ![Scherm opname van het deel venster ' IP-beperking toevoegen '.](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. Voer eventueel een naam en beschrijving van de regel in.  
1. Selecteer in de vervolg keuzelijst **type** het type regel.  
1. Voer in het vak **prioriteit** een prioriteits waarde in.  
1. Selecteer in de vervolg keuzelijst **abonnement**, **Virtual Network** en **subnet** de items waarvoor u de toegang wilt beperken.  

### <a name="set-an-ip-address-based-rule"></a>Een op IP-adres gebaseerde regel instellen

Volg de procedure zoals beschreven in de vorige sectie, maar met de volgende variatie:
* Selecteer voor stap 3 in de vervolg keuzelijst **type** de optie **IPv4** of **IPv6**. 

Geef het IP-adres op in de CIDR-notatie (Classable Inter-Domain Routing) voor zowel IPv4-als IPv6-adressen. Als u een adres wilt opgeven, kunt u iets zoals *1.2.3.4/32* gebruiken, waarbij de eerste vier octetten uw IP-adres vertegenwoordigen en */32* het masker is. De IPv4 CIDR-notatie voor alle adressen is 0.0.0.0/0. Zie [klasseloze Inter-Domain route ring](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)voor meer informatie over CIDR-notatie. 

## <a name="use-service-endpoints"></a>Service-eindpunten gebruiken

Door service-eind punten te gebruiken, kunt u de toegang beperken tot geselecteerde subnetten van het virtuele netwerk van Azure. Als u de toegang tot een specifiek subnet wilt beperken, maakt u een beperkings regel met een **Virtual Network** type. U kunt vervolgens het abonnement, het virtuele netwerk en het subnet selecteren dat u wilt toestaan of weigeren van toegang tot. 

Als service-eind punten niet al zijn ingeschakeld met micro soft. web voor het geselecteerde subnet, worden ze automatisch ingeschakeld, tenzij u het selectie vakje **ontbrekende micro soft. webservice-eind punten negeren** inschakelt. Het scenario waarin u mogelijk service-eind punten in de app wilt inschakelen, maar niet het subnet is afhankelijk van het feit of u gemachtigd bent om deze in te scha kelen op het subnet. 

Als iemand anders de service-eind punten in het subnet moet inschakelen, schakelt u het selectie vakje **ontbrekende micro soft. web service-eind punten negeren** in. Uw app wordt geconfigureerd voor service-eind punten in de verwachting dat ze later in het subnet worden ingeschakeld. 

![Scherm opname van het deel venster ' IP-beperking toevoegen ' met het Virtual Network type geselecteerd.](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

U kunt Service-eind punten niet gebruiken om de toegang te beperken tot apps die worden uitgevoerd in een App Service Environment. Als uw app zich in een App Service Environment bevindt, kunt u de toegang hiertoe beheren door IP-toegangs regels toe te passen. 

Met Service-eind punten kunt u uw app configureren met toepassings gateways of andere Web Application Firewall (WAF)-apparaten. U kunt ook toepassingen met meerdere lagen configureren met beveiligde back-ends. Zie [netwerk functies en app service](networking-features.md) en [Application Gateway integratie met Service-eind punten](networking/app-gateway-with-service-endpoints.md)voor meer informatie.

> [!NOTE]
> - Service-eind punten worden momenteel niet ondersteund voor web-apps die gebruikmaken van IP-Secure Sockets Layer (VIP) voor virtueel IP-verkeer.
> - Er is een limiet van 512 rijen met IP-of service-eindpunt beperkingen. Als u meer dan 512 rijen met beperkingen nodig hebt, raden we u aan om een zelfstandig beveiligings product te installeren, zoals Azure front deur, Azure-app gateway of een WAF.
>

## <a name="manage-access-restriction-rules"></a>Toegangs beperkings regels beheren

U kunt een bestaande toegangs beperkings regel bewerken of verwijderen.

### <a name="edit-a-rule"></a>Een regel bewerken

1. Als u een bestaande toegangs beperkings regel wilt bewerken, dubbelklikt u op de pagina **toegangs beperkingen** op de regel die u wilt bewerken.

1. Breng in het deel venster **IP-beperking bewerken** de gewenste wijzigingen aan en selecteer **regel bijwerken**. Bewerkingen zijn direct van kracht, met inbegrip van wijzigingen in de volg orde van prioriteit.

   ![Scherm opname van het deel venster ' IP-beperking bewerken ' in de Azure Portal, waarin de velden voor een bestaande toegangs beperkings regel worden weer gegeven.](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > Wanneer u een regel bewerkt, kunt u niet scha kelen tussen een IP-adres regel en een regel voor een virtueel netwerk. 

   ![Scherm opname van het deel venster ' IP-beperking bewerken ' in Azure Portal, waarin de instellingen voor een virtuele netwerk regel worden weer gegeven.](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel wilt verwijderen, selecteert u op de pagina **toegangs beperkingen** het beletsel teken (**...**) naast de regel die u wilt verwijderen en selecteert u vervolgens **verwijderen**.

![Scherm afbeelding van de pagina toegangs beperkingen, met het beletsel teken ' verwijderen ' naast de toegangs beperkings regel die moet worden verwijderd.](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>Eén IP-adres blok keren

Wanneer u uw eerste IP-beperkings regel toevoegt, voegt de service een expliciete regel voor het *weigeren van alle* regels toe met de prioriteit 2147483647. In de praktijk is de regel expliciet *Alles weigeren* de laatste regel die moet worden uitgevoerd en blokkeert deze de toegang tot een IP-adres dat niet expliciet is toegestaan door een regel voor *toestaan* .

Voor een scenario waarbij u een enkel IP-adres of een blok met IP-adressen expliciet wilt blok keren, maar toegang tot alle andere wilt toestaan, voegt u een expliciete regel voor *toestaan* toe.

![Scherm afbeelding van de pagina toegangs beperkingen in de Azure Portal, waarbij één geblokkeerd IP-adres wordt weer gegeven.](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>Toegang tot een SCM-site beperken 

Naast het beheren van de toegang tot uw app, kunt u de toegang beperken tot de SCM-site die wordt gebruikt door uw app. De SCM-site is zowel het Web Deploy-eind punt als de kudu-console. U kunt toegangs beperkingen voor de SCM-site afzonderlijk toewijzen vanuit de app of dezelfde set beperkingen voor zowel de app als de SCM-site gebruiken. Wanneer u **dezelfde beperkingen als \<app name>** selectie vakje inschakelt, wordt alles leeg gelaten. Als u het selectie vakje uitschakelt, worden de instellingen van uw SCM-site opnieuw toegepast. 

![Scherm afbeelding van de pagina toegangs beperkingen in de Azure Portal, waarin wordt weer gegeven dat er geen toegangs beperkingen zijn ingesteld voor de SCM-site of de app.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>Toegangs beperkings regels beheren met programma code

U kunt op een van de volgende manieren toegangs beperkingen toevoegen: 

* Gebruik [de Azure cli](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true). Bijvoorbeeld:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Gebruik [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true). Bijvoorbeeld:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

U kunt waarden ook hand matig instellen door een van de volgende handelingen uit te voeren:

* Gebruik een [Azure rest API](/rest/api/azure/) put-bewerking in de app-configuratie in azure Resource Manager. De locatie voor deze informatie in Azure Resource Manager is:

  management.azure.com/subscriptions/-**abonnements-id**/resourceGroups/**resource groepen**/providers/Microsoft.web/sites/**Web-app-naam**/config/Web? API-Version = 2018-02-01

* Een ARM-sjabloon gebruiken. U kunt bijvoorbeeld resources.azure.com gebruiken en het ipSecurityRestrictions-blok bewerken om de vereiste JSON toe te voegen.

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

## <a name="set-up-azure-functions-access-restrictions"></a>Toegangs beperkingen voor Azure Functions instellen

Er zijn ook toegangs beperkingen beschikbaar voor functie-apps met dezelfde functionaliteit als App Service-abonnementen. Wanneer u toegangs beperkingen inschakelt, schakelt u ook de Azure Portal code-editor uit voor niet-toegestane IP-adressen.

## <a name="next-steps"></a>Volgende stappen
[Toegangs beperkingen voor Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integratie met Service-eind punten Application Gateway](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
