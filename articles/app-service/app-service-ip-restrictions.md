---
title: Azure App Service toegangsbeperkingen
description: Meer informatie over het beveiligen van uw app in Azure App Service door toegangsbeperkingen in te stellen.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 541af6d0051d06de5721b22616fbf1e2867b71d6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833360"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Toegangsbeperkingen Azure App Service instellen

Door toegangsbeperkingen in te stellen, kunt u een op prioriteit geordende lijst voor toestaan/weigeren definiëren waarmee de netwerktoegang tot uw app wordt bepaald. De lijst kan IP-adressen of Azure Virtual Network s bevatten. Wanneer er een of meer vermeldingen zijn, bestaat er een *impliciete deny all* aan het einde van de lijst.

De toegangsbeperking werkt met alle Azure App Service gehoste workloads. De workloads kunnen web-apps, API-apps, Linux-apps, Linux-container-apps en Functions omvatten.

Wanneer er een aanvraag wordt ingediend bij uw app, wordt het FROM-adres geëvalueerd op basis van de regels in uw toegangsbeperkingslijst. Als het FROM-adres zich in een subnet dat is geconfigureerd met service-eindpunten voor Microsoft.Web, wordt het bronsubnet vergeleken met de regels voor virtuele netwerken in uw lijst met toegangsbeperkingen. Als het adres geen toegang krijgt op basis van de regels in de lijst, antwoordt de service met een [HTTP 403-statuscode.](https://en.wikipedia.org/wiki/HTTP_403)

De toegangsbeperkingsmogelijkheid wordt geïmplementeerd in de App Service front-endrollen, die upstream zijn van de werkrolhosts waarop uw code wordt uitgevoerd. Daarom zijn toegangsbeperkingen effectief netwerktoegangsbeheerlijsten (ACL's).

De mogelijkheid om de toegang tot uw web-app vanuit een virtueel Azure-netwerk te beperken, wordt ingeschakeld door [service-eindpunten][serviceendpoints]. Met service-eindpunten kunt u de toegang tot een service met meerdere tenants vanuit geselecteerde subnetten beperken. Het werkt niet om verkeer te beperken tot apps die worden gehost in een App Service Environment. Als u zich in een App Service Environment, kunt u de toegang tot uw app controleren door IP-adresregels toe te passen.

> [!NOTE]
> De service-eindpunten moeten zowel aan de netwerkzijde als voor de Azure-service waarmee ze worden ingeschakeld, zijn ingeschakeld. Voor een lijst met Azure-services die service-eindpunten ondersteunen, Virtual Network [service-eindpunten.](../virtual-network/virtual-network-service-endpoints-overview.md)
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagram van de stroom van toegangsbeperkingen.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Toegangsbeperkingsregels beheren in de portal

Ga als volgt te werk om een toegangsbeperkingsregel toe te voegen aan uw app:

1. Meld u aan bij Azure Portal.

1. Selecteer Netwerken in het **linkerdeelvenster.**

1. Selecteer in **het deelvenster** Netwerken onder Toegangsbeperkingen de optie **Toegangsbeperkingen** **configureren.**

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Schermopname van App Service deelvenster Netwerkopties in de Azure Portal.":::

1. Bekijk op **de pagina Toegangsbeperkingen** de lijst met toegangsbeperkingsregels die zijn gedefinieerd voor uw app.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Schermopname van de pagina Toegangsbeperkingen in de Azure Portal met de lijst met toegangsbeperkingsregels die zijn gedefinieerd voor de geselecteerde app.":::

   In de lijst worden alle huidige beperkingen weergegeven die op de app worden toegepast. Als u een beperking voor het virtuele netwerk voor uw app hebt, wordt in de tabel laten zien of de service-eindpunten zijn ingeschakeld voor Microsoft.Web. Als er geen beperkingen zijn gedefinieerd voor uw app, is de app overal toegankelijk.

### <a name="add-an-access-restriction-rule"></a>Een toegangsbeperkingsregel toevoegen

Als u een toegangsbeperkingsregel aan uw app wilt toevoegen, selecteert u in het deelvenster **Toegangsbeperkingen** de optie **Regel toevoegen.** Nadat u een regel toevoegt, wordt deze onmiddellijk van kracht. 

Regels worden afgedwongen in volgorde van prioriteit, beginnend bij het laagste getal in de **kolom Prioriteit.** Een *impliciete deny all* is van kracht nadat u zelfs maar één regel toevoegt.

Ga als **volgt te werk** in het deelvenster Toegangsbeperking toevoegen wanneer u een regel maakt:

1. Selecteer **onder** Actie de optie **Toestaan** of **Weigeren.**  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Schermopname van het deelvenster Toegangsbeperking toevoegen.":::

1. Voer desgewenst een naam en beschrijving van de regel in.
1. Voer in **het** vak Prioriteit een prioriteitswaarde in.
1. Selecteer in **de** vervolgkeuzelijst Type het type regel.

De verschillende typen regels worden beschreven in de volgende secties.

> [!NOTE]
> - Er geldt een limiet van 512 toegangsbeperkingsregels. Als u meer dan 512 toegangsbeperkingsregels nodig hebt, raden we u aan om een zelfstandig beveiligingsproduct te installeren, zoals Azure Front Door, Azure-app Gateway of een alternatieve WAF.
>
#### <a name="set-an-ip-address-based-rule"></a>Een regel op basis van IP-adressen instellen

Volg de procedure zoals beschreven in de vorige sectie, maar met de volgende toevoeging:
* Selecteer voor stap 4 in **de vervolgkeuzelijst Type** de optie **IPv4** of **IPv6.** 

Geef het **IP-adresblok** op in Inter-Domain CIDR-notatie (Classless Inter-Domain Routing) voor zowel de IPv4- als IPv6-adressen. Als u een adres wilt opgeven, kunt u bijvoorbeeld *1.2.3.4/32* gebruiken, waarbij de eerste vier octets uw IP-adres vertegenwoordigen en */32* het masker is. De IPv4 CIDR-notatie voor alle adressen is 0.0.0.0/0. Zie Classless Inter-Domain Routing voor meer informatie over [CIDR-notatie.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 

#### <a name="set-a-service-endpoint-based-rule"></a>Een regel op basis van een service-eindpunt instellen

* Selecteer voor stap 4 in **de vervolgkeuzelijst Type** **de optie Virtual Network.**

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Schermopname van het deelvenster Beperking toevoegen met het Virtual Network geselecteerd.":::

Geef de **vervolgkeuzelijsten** **Abonnement, Virtual Network** en **Subnet** op, die overeenkomen met de lijsten waarvoor u de toegang wilt beperken.

Met behulp van service-eindpunten kunt u de toegang tot geselecteerde subnetten van virtuele Azure-netwerken beperken. Als service-eindpunten nog niet zijn ingeschakeld met Microsoft.Web voor het subnet dat u hebt geselecteerd, worden deze automatisch ingeschakeld, tenzij u het selectievakje Ontbrekende **Microsoft.Web-service-eindpunten** negeren inschakelen. Het scenario waarin u mogelijk service-eindpunten wilt inschakelen voor de app, maar niet het subnet, is voornamelijk afhankelijk van of u over de machtigingen voor het inschakelen ervan op het subnet bent. 

Als u iemand anders nodig hebt om service-eindpunten in te kunnenschakelen in het subnet, selecteert u het selectievakje Ontbrekende **Microsoft.Web-service-eindpunten** negeren. Uw app wordt geconfigureerd voor service-eindpunten, in afwachting dat deze later in het subnet worden ingeschakeld. 

U kunt service-eindpunten niet gebruiken om de toegang te beperken tot apps die worden uitgevoerd in een App Service Environment. Wanneer uw app zich in een App Service Environment, kunt u de toegang tot de app controleren door IP-toegangsregels toe te passen. 

Met service-eindpunten kunt u uw app configureren met toepassingsgateways of andere WAF-apparaten (Web Application Firewall). U kunt ook toepassingen met meerdere lagen configureren met beveiligde back-ends. Zie Netwerkfuncties en integratie [App Service en Application Gateway](networking-features.md) met [service-eindpunten](networking/app-gateway-with-service-endpoints.md)voor meer informatie.

> [!NOTE]
> - Service-eindpunten worden momenteel niet ondersteund voor web-apps die ip-Secure Sockets Layer (SSL) virtueel IP-adres (VIP) gebruiken.
>
#### <a name="set-a-service-tag-based-rule"></a>Een regel op basis van servicetags instellen

* Selecteer servicetag in de **vervolgkeuzelijst Type** **voor stap** 4.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Schermopname van het deelvenster Beperking toevoegen met het servicetagtype geselecteerd.":::

Elke servicetag vertegenwoordigt een lijst met IP-adresbereiken van Azure-services. Een lijst met deze services en koppelingen naar de specifieke reeksen vindt u in de [servicetagdocumentatie][servicetags].

Alle beschikbare servicetags worden ondersteund in toegangsbeperkingsregels. Voor het gemak is alleen een lijst met de meest voorkomende tags beschikbaar via de Azure Portal. Gebruik Azure Resource Manager of scripts om geavanceerdere regels te configureren, zoals regels met een regionaal bereik. Dit zijn de tags die beschikbaar zijn via Azure Portal:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Een regel bewerken

1. Als u een bestaande regel voor toegangsbeperkingen wilt bewerken, selecteert u op de pagina **Toegangsbeperkingen** de regel die u wilt bewerken.

1. Maak in **het deelvenster Toegangsbeperking** bewerken uw wijzigingen en selecteer **vervolgens Regel bijwerken.** Bewerkingen zijn onmiddellijk van kracht, met inbegrip van wijzigingen in volgorde van prioriteit.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Schermopname van het deelvenster Toegangsbeperking bewerken in Azure Portal, met de velden voor een bestaande toegangsbeperkingsregel.":::

   > [!NOTE]
   > Wanneer u een regel bewerkt, kunt u niet schakelen tussen regeltypen. 

### <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel wilt verwijderen, selecteert u op de pagina **Toegangsbeperkingen** het beletselteken (**...**) naast de regel die u wilt verwijderen en selecteert u **vervolgens Verwijderen.**

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Schermopname van de pagina Toegangsbeperkingen, met het beletselteken Verwijderen naast de regel voor toegangsbeperking die moet worden verwijderd.":::

## <a name="access-restriction-advanced-scenarios"></a>Geavanceerde scenario's voor toegangsbeperking
In de volgende secties worden enkele geavanceerde scenario's met toegangsbeperkingen beschreven.

### <a name="filter-by-http-header"></a>Filteren op HTTP-header

Als onderdeel van een regel kunt u extra http-headerfilters toevoegen. De volgende http-headernamen worden ondersteund:
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Voor elke headernaam kunt u maximaal 8 waarden toevoegen, gescheiden door komma's. De HTTP-headerfilters worden geëvalueerd na de regel zelf en beide voorwaarden moeten waar zijn om de regel toe te passen.

### <a name="multi-source-rules"></a>Regels voor meerdere bronnen

Met regels voor meerdere bronnen kunt u maximaal 8 IP-adresbereiken of 8 servicetags combineren in één regel. U kunt dit gebruiken als u meer dan 512 IP-adresbereiken hebt of als u logische regels wilt maken waarbij meerdere IP-adresbereiken worden gecombineerd met één http-headerfilter.

Regels voor meerdere bronnen worden op dezelfde manier gedefinieerd als u regels met één bron definieert, maar met elk bereik gescheiden door komma's.

PowerShell-voorbeeld:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Eén IP-adres blokkeren

Wanneer u uw eerste regel voor toegangsbeperking toevoegt, voegt de service een expliciete regel alle weigeren toe met een prioriteit van 2147483647.  In de praktijk is de *expliciete* regel Alles weigeren de laatste regel die moet worden uitgevoerd en blokkeert deze de toegang tot alle IP-adressen die niet expliciet zijn toegestaan door een *regel* toestaan.

Voor een scenario waarin u expliciet één IP-adres of een blok MET IP-adressen wilt blokkeren, maar toegang tot alle andere adressen wilt toestaan, voegt u een expliciete regel Alles *toestaan* toe.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Schermopname van de pagina Toegangsbeperkingen in de Azure Portal, met één geblokkeerd IP-adres.":::

### <a name="restrict-access-to-an-scm-site"></a>Toegang tot een SCM-site beperken 

U kunt niet alleen de toegang tot uw app bepalen, maar ook de toegang beperken tot de SCM-site die door uw app wordt gebruikt. De SCM-site is zowel het web-implementatie-eindpunt als de Kudu-console. U kunt toegangsbeperkingen aan de SCM-site afzonderlijk toewijzen vanuit de app of dezelfde set beperkingen gebruiken voor zowel de app als de SCM-site. Wanneer u het **selectievakje \<app name> Dezelfde beperkingen als** incheckt, wordt alles leeg gelaten. Als u het selectievakje uitcheckt, worden uw SCM-site-instellingen opnieuw toegepast. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Schermopname van de pagina Toegangsbeperkingen in de Azure Portal, waarin wordt weergegeven dat er geen toegangsbeperkingen zijn ingesteld voor de SCM-site of de app.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Toegang tot een specifiek Azure Front Door beperken
Verkeer van Azure Front Door naar uw toepassing is afkomstig van een bekende set IP-adresbereiken die zijn gedefinieerd in de servicetag AzureFrontDoor.Backend. Met behulp van een beperkingsregel voor servicetags kunt u verkeer beperken tot alleen afkomstig van Azure Front Door. Om ervoor te zorgen dat verkeer alleen afkomstig is van uw specifieke exemplaar, moet u de binnenkomende aanvragen verder filteren op basis van de unieke HTTP-header die Azure Front Door verzendt.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Schermopname van de pagina Toegangsbeperkingen in de Azure Portal, waarin wordt weergegeven hoe u een Azure Front Door toevoegt.":::

PowerShell-voorbeeld:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Toegangsbeperkingsregels programmatisch beheren

U kunt toegangsbeperkingen via een van de volgende programma's toevoegen: 

* Gebruik [de Azure CLI](/cli/azure/webapp/config/access-restriction). Bijvoorbeeld:
   
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
   > Voor het werken met servicetags, HTTP-headers of regels voor meerdere bronnen is ten minste versie 5.7.0 vereist. U kunt de versie van de geïnstalleerde module controleren met: **Get-InstalledModule -Name Az**

U kunt waarden ook handmatig instellen door het volgende te doen:

* Gebruik een [Azure REST API](/rest/api/azure/) PUT-bewerking op de app-configuratie in Azure Resource Manager. De locatie voor deze informatie in Azure Resource Manager is:

  management.azure.com/subscriptions/**abonnements-id**/resourceGroups/**resourcegroepen**/providers/Microsoft.Web/sites/**web-app name**/config/web?api-version=2020-06-01

* Gebruik een Resource Manager sjabloon. U kunt bijvoorbeeld een resources.azure.com en het blok ipSecurityRestrictions bewerken om de vereiste JSON toe te voegen.

  De JSON-syntaxis voor het eerdere voorbeeld is:

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
  De JSON-syntaxis voor een geavanceerd voorbeeld met servicetag en http-headerbeperking is:
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
## <a name="set-up-azure-functions-access-restrictions"></a>Toegangsbeperkingen Azure Functions instellen

Toegangsbeperkingen zijn ook beschikbaar voor functie-apps met dezelfde functionaliteit als App Service abonnementen. Wanneer u toegangsbeperkingen inschakelen, schakelt u ook de Azure Portal code-editor voor alle niet-toegestaan IP's.

## <a name="next-steps"></a>Volgende stappen
[Toegangsbeperkingen voor Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integratie met service-eindpunten](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
