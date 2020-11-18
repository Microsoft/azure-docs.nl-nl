---
title: Een App Service Environment gebruiken en beheren
description: Meer informatie over het maken, publiceren en schalen van apps in een App Service Environment. Alle algemene taken in dit artikel zoeken.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 86d0569d95df18924ed47682b75d7491c71d4483
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833551"
---
# <a name="use-an-app-service-environment"></a>Een App Service-omgeving gebruiken

Een App Service Environment (ASE) is een implementatie van Azure App Service in een subnet in een Azure Virtual Network-exemplaar van de klant. Een ASE bestaat uit:

- **Front-ends**: waar http of https eindigt in een app service Environment
- **Werk nemers**: de resources die uw apps hosten
- **Data Base**: bevat informatie die de omgeving definieert
- **Opslag**: wordt gebruikt voor het hosten van de door de klant gepubliceerde apps

U kunt een ASE implementeren met een extern of intern virtueel IP-adres (VIP) voor toegang tot apps. Een implementatie met een extern VIP wordt meestal een *externe ASE* genoemd. Een implementatie met een intern VIP wordt een *ILB-ASE* genoemd, omdat het een interne load BALANCER (ILB) gebruikt. Zie [een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over de ILB-ASE.

## <a name="create-an-app-in-an-ase"></a>Een app maken in een ASE

Als u een app in een ASE wilt maken, gebruikt u hetzelfde proces als wanneer u normaal gesp roken een app maakt, maar met enkele kleine verschillen. Wanneer u een nieuw App Service plan maakt:

- In plaats van een geografische locatie te kiezen voor het implementeren van uw app, kiest u een ASE als uw locatie.
- Alle App Service plannen die in een ASE zijn gemaakt, kunnen zich alleen in een geïsoleerde prijs categorie bevindt.

Als u geen ASE hebt, kunt u er een maken door de instructies in [Create a app service Environment][MakeExternalASE]te volgen.

Een app maken in een ASE:

1. Selecteer **een resource maken**  >  **Web en mobiel**  >  **Web-app**.

1. voer een naam voor de app in. Als u al een App Service plan in een ASE hebt geselecteerd, komt de domein naam voor de app overeen met de domein naam van de ASE:

    ![App-naam selecteren][1]

1. Selecteer een abonnement.

1. Voer een naam in voor een nieuwe resource groep of selecteer **bestaande gebruiken** en selecteer er een in de vervolg keuzelijst.

1. Selecteer uw besturingssysteem.

1. Selecteer een bestaand App Service plan in uw ASE of maak een nieuw abonnement door de volgende stappen uit te voeren:

    a. Selecteer **een resource maken > web-app** in het Azure Portal menu aan de linkerkant.

    b. Selecteer het abonnement.

    c. Selecteer of maak de resource groep.

    d. Voer de naam van uw web-app in.

    e. Selecteer **code** of **DockerContainer**.

    f. Selecteer een runtime stack.

    g. Selecteer **Linux** of **Windows**. 

    h. Selecteer uw ASE in de vervolg keuzelijst **regio** . 

    i. Selecteer of maak een nieuw App Service plan. Als u een nieuw App Service plan maakt, selecteert u de juiste **geïsoleerde** SKU-grootte.

    ![Geïsoleerde prijs Categorieën][2]

    > [!NOTE]
    > Linux-apps en Windows-apps kunnen zich niet in hetzelfde App Service plan bevinden, maar ze kunnen zich in dezelfde App Service Environment bevinden.
    >

1. Selecteer **controleren + maken**, Controleer of de gegevens juist zijn en selecteer vervolgens **maken**.

## <a name="how-scale-works"></a>Hoe schalen werkt

Elke App Service-app wordt uitgevoerd in een App Service plan. App Service omgevingen houden App Service plannen en App Service abonnementen op apps. Wanneer u een app schaalt, kunt u ook het App Service plan en alle apps in hetzelfde abonnement schalen.

Wanneer u een App Service plan schaalt, wordt de benodigde infra structuur automatisch toegevoegd. Er is een vertraging opgetreden bij het schalen van bewerkingen terwijl de infra structuur wordt toegevoegd. Als u verschillende schaal bewerkingen op volg orde uitvoert, wordt de eerste aanvraag voor de infrastructuur schaal uitgevoerd en worden de andere op de wachtrij geplaatst. Wanneer de eerste schaal bewerking is voltooid, worden alle andere infrastructuur aanvragen samen gebruikt. En wanneer de infra structuur wordt toegevoegd, worden de App Service plannen als toepasselijk toegewezen. Het maken van een nieuw App Service plan is zelf een schaal bewerking omdat er extra hardware wordt aangevraagd.

In de multi tenant-App Service is het schalen direct omdat een groep resources eenvoudig beschikbaar is om het te ondersteunen. In een ASE zijn er geen buffers en worden er resources toegewezen op basis van de behoefte.

In een ASE kunt u een App Service plan tot 100 exemplaren schalen. Een ASE kan tot 201 totaal aantal exemplaren hebben in alle App Service plannen in die ASE.

## <a name="ip-addresses"></a>IP-adressen

App Service kunt een toegewezen IP-adres toewijzen aan een app. Deze mogelijkheid is beschikbaar nadat u SSL op basis van IP hebt geconfigureerd, zoals beschreven in [een bestaand aangepast TLS/SSL-certificaat binden aan Azure app service][ConfigureSSL]. In een ILB-ASE kunt u geen IP-adressen meer toevoegen die worden gebruikt voor SSL op basis van IP.

Met een externe ASE kunt u op IP gebaseerde SSL voor uw app op dezelfde manier configureren als in de multi tenant-App Service. Er is altijd één reserve adres in de ASE, Maxi maal 30 IP-adressen. Telkens wanneer u er een gebruikt, wordt er een andere toegevoegd, zodat een adres altijd beschikbaar is. Er is een tijds vertraging vereist om een ander IP-adres toe te wijzen. Deze vertraging voor komt dat er snel achter elkaar IP-adressen worden toegevoegd.

## <a name="front-end-scaling"></a>Front-end schalen

Wanneer u uw App Service-abonnementen uitbreidt, worden werk nemers automatisch toegevoegd ter ondersteuning van de plannen. Elke ASE wordt gemaakt met twee front-ends. De front-ends worden automatisch uitgeschaald met een snelheid van een front-end voor elke set exemplaren van 15 App Service-abonnementen. Als u bijvoorbeeld drie App Service plannen hebt met vijf instanties, hebt u een totaal van 15 exemplaren en drie front-ends. Als u naar een totaal van 30 exemplaren schaalt, hebt u vier front-ends. Dit patroon gaat verder terwijl u uitschaalt.

Het aantal front-ends dat standaard wordt toegewezen, is geschikt voor een gemiddelde belasting. U kunt de verhouding net zo weinig als een front-end voor elke vijf instanties verlagen. U kunt ook de grootte van de front-ends wijzigen. Standaard zijn deze enkelvoudige kernen. In de Azure Portal kunt u de grootte wijzigen in twee of vier kernen.

Er worden kosten in rekening gebracht voor het wijzigen van de verhouding of de front-end-grootte. Zie [Azure app service prijzen][Pricing]voor meer informatie. Als u de laad capaciteit van uw ASE wilt verbeteren, kunt u het beste beter schalen naar twee kern front-ends voordat u de schaal ratio aanpast. Als u de kern grootte van uw front-ends wijzigt, wordt de upgrade van uw ASE uitgevoerd en moet dit buiten kantoor uren worden gedaan.

Front-end-resources zijn het HTTP/HTTPS-eind punt voor de ASE. Met de standaard front-end-configuratie is het geheugen gebruik per front-end consistent rond 60 procent. De belangrijkste reden voor het schalen van uw front-ends is het CPU-gebruik, dat voornamelijk wordt aangedreven door HTTPS-verkeer.

## <a name="app-access"></a>App-toegang

In een externe ASE is het domein achtervoegsel dat wordt gebruikt voor het maken van apps *. &lt; asename &gt; . p.azurewebsites.net*. Als uw ASE de naam _External-ASE_ heeft en u een app met de naam _CONTOSO_ in die ASE host, kunt u deze op de volgende url's bereiken:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Zie [een app service Environment maken][MakeExternalASE]voor informatie over het maken van een externe ASE.

In een ILB-ASE is het domein achtervoegsel dat wordt gebruikt voor het maken van apps *. &lt; asename &gt; . appserviceenvironment.net*. Als uw ASE de naam _ILB-ASE_ heeft en u een app met de naam _CONTOSO_ in die ASE host, kunt u deze op de volgende url's bereiken:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Zie [een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over het maken van een ILB-ASE.

De SCM-URL wordt gebruikt voor toegang tot de kudu-console of voor het publiceren van uw app met behulp van Web Deploy. Zie [kudu-console voor Azure app service][Kudu]voor meer informatie over de kudu-console. De kudu-console biedt u een webinterface voor het opsporen van fouten, het uploaden van bestanden, het bewerken van bestanden en nog veel meer.

### <a name="dns-configuration"></a>DNS-configuratie 

Wanneer u een externe ASE gebruikt, worden apps die in uw ASE zijn gemaakt, geregistreerd bij Azure DNS. Er zijn geen extra stappen in een externe ASE voor uw apps die openbaar beschikbaar moeten zijn. In een ILB ASE-omgeving moet u uw eigen DNS beheren. U kunt dit doen in uw eigen DNS-server of met Azure DNS privézones.

DNS configureren in uw eigen DNS-server met uw ILB-ASE:

1. Maak een zone voor &lt;ASE name&gt;. appserviceenvironment.net
1. Maak in die zone een A-record die * verwijst naar het IP-adres van de ILB
1. Maak in die zone een A-record die @ verwijst naar het IP-adres van de ILB
1. Maak een zone in &lt;ASE name&gt;. appserviceenvironment.net met de naam SCM
1. Maak in die SCM-zone een A-record die * verwijst naar het IP-adres van de ILB

DNS configureren in Azure DNS particuliere zones:

1. Maak een Azure DNS privézone met de naam &lt;ASE-naam&gt;.appserviceenvironment.net
1. Maak in die zone een A-record die * verwijst naar het IP-adres van de ILB
1. Maak in die zone een A-record die @ verwijst naar het IP-adres van de ILB
1. Maak in die zone een A-record die *.scm verwijst naar het IP-adres van de ILB

De DNS-instellingen voor uw ASE-standaard domeinachtervoegsel beperken u niet dat uw apps toegankelijk zijn voor die namen. U kunt een aangepaste domeinnaam instellen zonder validatie voor uw apps in een ILB-ASE. Als u vervolgens een zone met de naam *contoso.net* wilt maken, kunt u dit doen en deze naar het IP-adres van de ILB wijzen. De aangepaste domein naam werkt voor app-aanvragen, maar niet voor de SCM-site. De SCM-site is alleen beschikbaar op *&lt; AppName &gt; . scm. &lt; asename &gt; . appserviceenvironment.net*. 

De zone met de naam *. &lt; asename &gt; . appserviceenvironment.net* is wereld wijd uniek. Voor 2019 konden klanten het achtervoegsel van het domein van de ILB ASE opgeven. Als u *. contoso.com* wilt gebruiken voor het domein achtervoegsel, kunt u dit doen en dat zou de SCM-site zouden kunnen bevatten. Er waren problemen met dat model, waaronder; het beheren van het standaard SSL-certificaat, het ontbreken van eenmalige aanmelding met de SCM-site en de vereiste om een wildcard certificaat te gebruiken. Het upgradeproces van het ILB ASE-standaard certificaat was tevens verstoord en veroorzaakte het opnieuw opstarten van de toepassing. Om deze problemen op te lossen, is het ILB ASE-gedrag gewijzigd om een domeinachtervoegsel te gebruiken op basis van de naam van de ASE en met een achtervoegsel dat eigendom is van Microsoft. De wijziging van het ILB ASE-gedrag heeft alleen invloed op ILB ASE's gemaakt na mei 2019. Bestaande ILB ASE's as moeten nog steeds het standaard certificaat van de ASE en de bijbehorende DNS-configuratie beheren.

## <a name="publishing"></a>Publiceren

In een ASE kunt u, net als bij de multi tenant-App Service, de volgende methoden publiceren:

- Webimplementatie
- FTP
- Continue integratie (CI)
- Slepen en neerzetten in de kudu-console
- Een IDE, zoals Visual Studio, eclips of IntelliJe idee

Met een externe ASE werken deze publicatie opties op dezelfde manier. Zie [implementatie in azure app service][AppDeploy]voor meer informatie.

Met een ILB-ASE zijn de publicatie-eind punten alleen beschikbaar via de ILB. De ILB bevindt zich op een privé-IP in het subnet ASE in het virtuele netwerk. Als u geen netwerk toegang tot de ILB hebt, kunt u geen apps publiceren op die ASE. Zoals vermeld in [Create and use a ILB ASE][MakeILBASE], moet u DNS configureren voor de apps in het systeem. Deze vereiste omvat het SCM-eind punt. Als de eind punten niet correct zijn gedefinieerd, kunt u niet publiceren. Uw Ide's moet ook netwerk toegang hebben tot de ILB om er rechtstreeks naar te publiceren.

Zonder extra wijzigingen werken op internet gebaseerde CI-systemen zoals GitHub en Azure DevOps niet met een ILB ASE omdat het publicatie-eind punt niet toegankelijk is via internet. U kunt publiceren naar een ILB ASE inschakelen vanuit Azure DevOps door een zelf-hostende release agent te installeren in het virtuele netwerk dat de ILB ASE bevat. U kunt ook een CI-systeem gebruiken dat gebruikmaakt van een pull-model, zoals Dropbox.

De publicatie-eindpunten voor apps in een ILB AS-omgeving maken gebruik van het domein waarmee de ILB AS-omgeving is gemaakt. U kunt dit zien in het publicatie Profiel van de app en in het deel venster van de app-Portal (in **overzichts**  >  **kernen** en ook in **Eigenschappen**).

## <a name="storage"></a>Storage

Een ASE heeft 1 TB opslag ruimte voor alle apps in de ASE. Een App Service plan in de geïsoleerde prijs-SKU heeft een limiet van 250 GB. In een ASE wordt 250 GB aan opslag ruimte toegevoegd per App Service de limiet van 1 TB te plannen. U kunt meer App Service plannen hebben dan slechts vier, maar er is geen opslag meer toegevoegd dan de limiet van 1 TB.

## <a name="logging"></a>Logboekregistratie

U kunt uw ASE integreren met Azure Monitor voor het verzenden van logboeken over de ASE naar Azure Storage, Azure Event Hubs of Log Analytics. Deze items worden vandaag vastgelegd:

| Hiervan | Bericht |
|---------|----------|
| ASE is beschadigd | De opgegeven ASE is beschadigd vanwege een ongeldige configuratie van het virtuele netwerk. De ASE wordt onderbroken als de status slecht wordt voortgezet. Zorg ervoor dat de hier gedefinieerde richt lijnen worden gevolgd: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Het ASE-subnet heeft bijna geen ruimte meer | De opgegeven ASE bevindt zich in een subnet dat bijna geen ruimte meer heeft. Er zijn {0} nog andere adressen. Zodra deze adressen zijn uitgeput, kan de ASE niet worden geschaald.  |
| De limiet voor het aantal exemplaren van de ASE is bijna bereikt | De opgegeven ASE is bijna de limiet voor het aantal exemplaren van de ASE. Het bevat momenteel {0} app service plan exemplaren van Maxi maal 201 exemplaren. |
| ASE kan geen afhankelijkheid bereiken | De opgegeven ASE kan niet worden bereikt {0} .  Zorg ervoor dat de hier gedefinieerde richt lijnen worden gevolgd: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE is onderbroken | De opgegeven ASE is onderbroken. De ASE-suspensie kan worden veroorzaakt door een account tekort of een ongeldige configuratie van het virtuele netwerk. Los de hoofd oorzaak op en hervat de ASE om verkeer door te sturen. |
| Upgrade van ASE is gestart | Een platform upgrade naar de opgegeven ASE is gestart. Verwachte vertragingen bij het schalen van bewerkingen. |
| De ASE-upgrade is voltooid | Een platform upgrade naar de opgegeven ASE is voltooid. |
| Schaal bewerkingen zijn gestart | Een App Service plan ( {0} ) is begonnen met schalen. Gewenste status: {1} ik {2} werk nemers.
| Schaal bewerkingen zijn voltooid | Het schalen van een App Service plan ( {0} ) is voltooid. Huidige status: {1} ik {2} werk nemers. |
| Schaal bewerkingen zijn mislukt | Het schalen van een App Service plan ( {0} ) is mislukt. Huidige status: {1} ik {2} werk nemers. |

Logboek registratie inschakelen voor uw ASE:

1. Ga in de portal naar **Diagnostische instellingen**.
1. Selecteer **Diagnostische instelling toevoegen**.
1. Geef een naam op voor de logboek integratie.
1. Selecteer en configureer de gewenste logboek doelen.
1. Selecteer **AppServiceEnvironmentPlatformLogs**.

![Instellingen voor Diagnostische logboeken van ASE][4]

Als u integreert met Log Analytics, kunt u de logboeken bekijken door **Logboeken** te selecteren in de ASE-Portal en een query te maken op basis van **AppServiceEnvironmentPlatformLogs**. Logboeken worden alleen verzonden wanneer uw ASE een gebeurtenis heeft die het kan activeren. Als uw ASE deze gebeurtenis niet heeft, zijn er geen logboeken. Als u snel een voor beeld van Logboeken in uw Log Analytics-werk ruimte wilt weer geven, voert u een schaal bewerking uit met een van de App Service plannen in uw ASE. U kunt vervolgens een query uitvoeren op **AppServiceEnvironmentPlatformLogs** om deze logboeken te bekijken. 

**Een waarschuwing maken**

Volg de instructies in [logboek waarschuwingen maken, weer geven en beheren met Azure monitor](../../azure-monitor/platform/alerts-log.md)om een waarschuwing te maken voor uw logboeken. In het kort:

* Open de pagina waarschuwingen in uw ASE-Portal
* **Nieuwe waarschuwings regel** selecteren
* Selecteer uw resource als Log Analytics werk ruimte
* Stel uw voor waarde in met een aangepaste zoek opdracht in Logboeken voor het gebruik van een query als ' AppServiceEnvironmentPlatformLogs | waarbij ResultDescription "is begonnen met schalen" of wat u wilt. Stel de drempel waarde in als dat nodig is. 
* Een actie groep toevoegen of maken naar wens. De actie groep is de locatie waar u het antwoord op de waarschuwing definieert, zoals het verzenden van een e-mail of SMS-bericht
* Geef een naam op voor de waarschuwing en sla deze op.

## <a name="upgrade-preference"></a>Upgrade voorkeur

Als u meerdere as hebt, is het mogelijk dat u wilt dat sommige as worden bijgewerkt vóór andere. In het object ASE **Hosting Environment Resource Manager** kunt u een waarde instellen voor **upgradePreference**. De instelling **upgradePreference** kan worden geconfigureerd met behulp van een sjabloon, ARMClient of https://resources.azure.com . De drie mogelijke waarden zijn:

- **Geen**: Azure zal uw ASE bijwerken in een bepaalde batch. Dit is de standaardwaarde.
- **Vroeg**: uw ASE wordt bijgewerkt in de eerste helft van de app service upgrades.
- **Te laat**: uw ASE wordt bijgewerkt in de tweede helft van de app service upgrades.

Als u gebruikt https://resources.azure.com , voert u de volgende stappen uit om de **upgradePreferences** -waarde in te stellen:

1. Ga naar resources.azure.com en meld u aan met uw Azure-account.
1. Ga door naar de abonnementen van de \/ \[ abonnements naam \] \/ resourceGroups \/ \[ resource groep naam \] \/ providers \/ micro soft. web \/ hostingEnvironments \/ \[ ASE name \] .
1. Selecteer bovenaan **lezen/schrijven** .
1. Selecteer **Bewerken**.
1. Stel **upgradePreference** in op een van de drie gewenste waarden.
1. Selecteer **patch**.

![bronnen Azure com-weer gave][5]

De functie **upgradePreferences** is het meest zinnig wanneer u meerdere as hebt, omdat uw ' early ' as wordt bijgewerkt vóór uw ' late ' as. Wanneer u meerdere as hebt, moet u uw ontwikkel-en test as zo instellen dat deze ' vroeg ' zijn en dat uw productie as ' laat ' is.

## <a name="pricing"></a>Prijzen

De prijs-SKU met de naam *geïsoleerd* is alleen voor gebruik met as. Alle App Service plannen die worden gehost in de ASE, bevinden zich in de afzonderlijke prijs-SKU. Geïsoleerde tarieven voor App Service abonnementen kunnen per regio verschillen.

Naast de prijs van uw App Service-abonnementen, is er een vast tarief voor de ASE zelf. Het vaste tempo wordt niet gewijzigd door de grootte van uw ASE. Het betaalt voor de ASE-infra structuur tegen een standaard schaal factor van één extra front-end voor elke 15 App Service-abonnement.

Als de standaard schaal snelheid van een front-end voor elke 15 App Service-plan exemplaren niet snel genoeg is, kunt u de verhouding aanpassen waarmee de front-ends worden toegevoegd of de grootte van de front-ends. Wanneer u de verhouding of de grootte aanpast, betaalt u voor de front-end-kernen die niet standaard worden toegevoegd.

Als u bijvoorbeeld de schaal verhouding wijzigt in 10, wordt een front-end toegevoegd voor elke 10 instanties in uw App Service-abonnementen. De vaste kosten omvatten een schaal tarief van een front-end voor elke 15 exemplaren. Met een schaal ratio van 10 betaalt u een vergoeding voor de derde front-end die is toegevoegd voor de tien App Service-plan exemplaren. U hoeft er niet voor te betalen wanneer u 15 exemplaren bereikt, omdat deze automatisch is toegevoegd.

Als u de grootte van de front-ends op twee kernen aanpast, maar de verhouding niet aanpast, betaalt u voor de extra kernen. Een ASE wordt gemaakt met twee front-ends, dus zelfs onder de drempel waarde voor automatisch schalen kunt u twee extra kernen betalen als u de grootte van twee kern front-ends hebt verhoogd.

Zie [Azure app service prijzen][Pricing]voor meer informatie.

## <a name="delete-an-ase"></a>Een ASE verwijderen

Een ASE verwijderen:

1. Selecteer **verwijderen** boven aan het deel venster **app service Environment** .

1. Voer de naam van uw ASE in om te bevestigen dat u deze wilt verwijderen. Wanneer u een ASE verwijdert, verwijdert u ook alle inhoud erin.

    ![ASE verwijderen][3]

1. Selecteer **OK**.

## <a name="ase-cli"></a>ASE CLI

Er zijn opdracht regel mogelijkheden voor het beheren van een ASE.  De AZ cli-opdrachten worden hieronder vermeld.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
