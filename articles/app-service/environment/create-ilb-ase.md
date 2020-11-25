---
title: Een ILB-ASE met ARM maken
description: Meer informatie over het maken van een App Service-omgeving met een interne load balancer (ILB ASE) met behulp van Azure Resource Manager-sjablonen. Uw apps volledig isoleren van internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 27c9198558a730d0af49077d6f5baa6db4789416
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009548"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Een App Service-omgeving voor een interne load balancer maken en gebruiken 

De Azure App Service-omgeving is een implementatie van Azure App Service in een subnet in een virtueel Azure-netwerk (VNet). Er zijn twee manieren om een Azure App Service-omgeving (ASE) te implementeren: 

- Met een VIP-adres op een extern IP-adres, vaak aangeduid als Externe AS-omgeving.
- Met een VIP-adres op een intern IP-adres, vaak aangeduid als een ILB AS-omgeving omdat het interne eindpunt een ILB (Internal Load Balancer) is. 

In dit artikel wordt uitgelegd hoe u een ILB AS-omgeving maakt. Zie [Inleiding tot de App Service-omgevingen][Intro] voor een overzicht van de ASE. Zie [Create an External ASE][MakeExternalASE] (Een Externe AS-omgeving maken) voor informatie over het maken van een Externe AS-omgeving.

## <a name="overview"></a>Overzicht 

U kunt een AS-omgeving implementeren met een eindpunt dat toegankelijk is via internet of met een IP-adres in uw VNet. De AS-omgeving moet zijn geïmplementeerd met een ILB om het IP-adres in te stellen op een VNet-adres. Als u de ASE-omgeving implementeert met een ILB, moet u de naam van uw ASE opgeven. De naam van uw ASE wordt gebruikt in het domeinachtervoegsel voor de apps in uw ASE.  Het domeinachtervoegsel voor uw ILB-ASE is &lt;ASE naam&gt;.appserviceenvironment.net. Apps die zijn gemaakt in een ILB-ASE, worden niet in de openbare DNS geplaatst. 

Bij eerdere versies van de ILB-ASE was het nodig dat u een domeinachtervoegsel en een standaardcertificaat voor HTTPS-verbindingen opgaf. Het domeinachtervoegsel wordt niet meer gebruikt bij het maken van de ILB-ASE en er is ook geen standaardcertificaat meer nodig. Wanneer u nu een ILB-ASE maakt, wordt het standaardcertificaat door Microsoft verzorgd en wordt dit vertrouwd door de browser. U kunt nog steeds aangepaste domeinnamen instellen voor apps in uw ASE en certificaten instellen voor die aangepaste domeinnamen. 

Met een ILB-ASE kunt u de volgende dingen doen:

-   Intranettoepassingen veilig hosten in de cloud, waartoe u toegang hebt via een site-naar-site- of ExpressRoute.
-   Apps beveiligen met een WAF-apparaat
-   Apps die niet worden vermeld op openbare DNS-servers, hosten in de cloud.
-   Back-end-apps met internetisolatie maken, waarmee front-end-apps veilig kunnen worden geïntegreerd.

### <a name="disabled-functionality"></a>Uitgeschakelde functionaliteit ###

Er is een aantal dingen dat u niet kunt doen wanneer u een ILB AS-omgeving gebruikt:

-   Op IP-gebaseerd SSL gebruiken.
-   IP-adressen toewijzen aan specifieke apps.
-   Een certificaat kopen en gebruiken met een app via Azure Portal. U kunt certificaten rechtstreeks bij een certificeringsinstantie verkrijgen en ze gebruiken met uw apps. U kunt ze niet verkrijgen via Azure Portal.

## <a name="create-an-ilb-ase"></a>Een ILB AS-omgeving maken ##

Ga als volgt te werk om een ILB AS-omgeving te maken:

1. Selecteer in de Azure-portal achtereenvolgens **Een resource maken** > **Web** > **App Service Environment**.

2. Selecteer uw abonnement.

3. Selecteer of maak een resourcegroep.

4. Voer de naam van uw App Service-omgeving in.

5. Selecteer Intern als virtueel IP-adres.

    ![ASE maken](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> De naam van de App Service-omgeving mag niet langer zijn dan 37 tekens.

6. Selecteer Netwerken

7. Selecteer of maak een virtueel netwerk. Als u hier een nieuw VNet maakt, krijgt deze een adresbereik van 192.168.250.0/23. Als u een VNet wilt maken met een ander adresbereik of in een andere resourcegroep dan de AS-omgeving, gebruikt u Azure Portal voor het maken van virtuele netwerken. 

8. Selecteer of maak een leeg subnet. Als u een subnet wilt selecteren, moet dit leeg zijn en niet gedelegeerd. De grootte van het subnet kan niet worden gewijzigd nadat de AS-omgeving is gemaakt. We raden een grootte aan van `/24`. Dit formaat bevat 256 adressen en kan de grootst mogelijke AS-omgeving verwerken en voldoen aan alle schaalbehoeften. 

    ![Netwerken voor AS-omgeving][1]

7. Selecteer **Beoordelen en maken** en selecteer vervolgens **Maken**.


## <a name="create-an-app-in-an-ilb-ase"></a>Een app maken in een ILB AS-omgeving ##

Het maken van een app in een ILB AS-omgeving werkt hetzelfde als het maken van een app in een AS-omgeving.

1. Selecteer in Azure Portal **Een resource maken** > **Web** > **Web-app**.

1. Voer de naam van de app in.

1. Selecteer het abonnement.

1. Selecteer of maak een resourcegroep.

1. Selecteer uw publicatie, runtimestack en besturingssysteem.

1. Selecteer een locatie waar de locatie een bestaande ILB-AS-omgeving is.  U kunt ook een nieuwe AS-omgeving maken tijdens het maken van een app door een geïsoleerd App Service-plan te selecteren. Als u een nieuwe AS-omgeving wilt maken, selecteert u de regio waarin u wilt dat de AS-omgeving wordt gemaakt.

1. Selecteer of maak een App Service-plan. 

1. Selecteer **Beoordelen en maken** en selecteer vervolgens **Maken** wanneer u klaar bent.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs, Functions en de ILB AS-omgeving 

Een ILB AS-omgeving biedt ondersteuning voor zowel Functions als WebJobs. Als u echter met deze wilt werken via de portal, hebt u netwerktoegang tot de SCM-site nodig.  Dit betekent dat de host van de browser zich in het virtuele netwerk moet bevinden of ermee moet zijn verbonden. Als uw ILB-AS-omgeving een domeinnaam heeft die niet eindigt op *appserviceenvironment.net*, moet u uw browser vragen het HTTPS-certificaat te vertrouwen dat wordt gebruikt door uw SCM-site.

## <a name="dns-configuration"></a>DNS-configuratie 

Wanneer u een externe ASE gebruikt, worden apps die in uw ASE zijn gemaakt, geregistreerd bij Azure DNS. Er zijn geen extra stappen in een externe ASE voor uw apps die openbaar beschikbaar moeten zijn. In een ILB ASE-omgeving moet u uw eigen DNS beheren. U kunt dit doen in uw eigen DNS-server of met Azure DNS privézones.

DNS configureren in uw eigen DNS-server met uw ILB-ASE:

1. Maak een zone voor &lt;ASE name&gt;. appserviceenvironment.net
2. Maak in die zone een A-record die * verwijst naar het IP-adres van de ILB
3. Maak in die zone een A-record die @ verwijst naar het IP-adres van de ILB
4. Maak een zone in &lt;ASE name&gt;. appserviceenvironment.net met de naam SCM
5. Maak in die SCM-zone een A-record die * verwijst naar het IP-adres van de ILB

DNS configureren in Azure DNS particuliere zones:

1. Maak een Azure DNS privézone met de naam &lt;ASE-naam&gt;.appserviceenvironment.net
2. Maak in die zone een A-record die * verwijst naar het IP-adres van de ILB
3. Maak in die zone een A-record die @ verwijst naar het IP-adres van de ILB
4. Maak in die zone een A-record die *.scm verwijst naar het IP-adres van de ILB

De DNS-instellingen voor uw ASE-standaard domeinachtervoegsel beperken u niet dat uw apps toegankelijk zijn voor die namen. U kunt een aangepaste domeinnaam instellen zonder validatie voor uw apps in een ILB-ASE. Als u vervolgens een zone met de naam contoso.net wilt maken, kunt u dit doen en deze naar het IP-adres van de ILB wijzen. De aangepaste domein naam werkt voor app-aanvragen, maar niet voor de SCM-site. De SCM-site is alleen beschikbaar op &lt;app-naam&gt;.scm.&lt;ASE-naam&gt;.appserviceenvironment.net.

De zone met de naam &lt;ASE-naam&gt;.appserviceenvironment.net is wereldwijd uniek. Voor 2019 konden klanten het achtervoegsel van het domein van de ILB ASE opgeven. Als u. contoso.com wilde gebruiken voor het domeinachtervoegsel, kon u dit doen en dat zou de SCM-site kunnen bevatten. Er waren problemen met dat model, waaronder; het beheren van het standaard SSL-certificaat, het ontbreken van eenmalige aanmelding met de SCM-site en de vereiste om een wildcard certificaat te gebruiken. Het upgradeproces van het ILB ASE-standaard certificaat was tevens verstoord en veroorzaakte het opnieuw opstarten van de toepassing. Om deze problemen op te lossen, is het ILB ASE-gedrag gewijzigd om een domeinachtervoegsel te gebruiken op basis van de naam van de ASE en met een achtervoegsel dat eigendom is van Microsoft. De wijziging van het ILB ASE-gedrag heeft alleen invloed op ILB ASE's gemaakt na mei 2019. Bestaande ILB ASE's as moeten nog steeds het standaard certificaat van de ASE en de bijbehorende DNS-configuratie beheren.

## <a name="publish-with-an-ilb-ase"></a>Publiceren met een ILB AS-omgeving

Elke app die wordt gemaakt, heeft twee eindpunten. In een ILB ASE-omgeving hebt u *&lt;app-naam&gt;.&lt;Domein voor ILB AS-omgeving&gt;* en *&lt;app-naam&gt;.scm.&lt;Domein voor ILB AS-omgeving&gt;* . 

De SCM-sitenaam leidt naar de Kudu-console, genaamd de **Geavanceerde portal**, binnen Azure Portal. Met behulp van de Kudu-console kunt u omgevingsvariabelen bekijken, de schijf verkennen, een console gebruiken, en nog veel meer. Zie [Kudu-console voor Azure App Service][Kudu] voor meer informatie. 

Op internet gebaseerde CI-systemen, zoals GitHub en Azure DevOps, werken nog steeds met een ILB AS-omgeving, als de buildagent toegankelijk is via internet en zich op hetzelfde netwerk bevindt als de ILB AS-omgeving. Als de buildagent dus, in het geval van Azure DevOps, is gemaakt in hetzelfde VNET als de ILB AS-omgeving (verschillende subnetten vormen geen probleem), kan met deze agent code worden opgehaald uit Azure DevOps-git en worden geïmplementeerd in de ILB AS-omgeving. Als u niet zelf een buildagent wilt maken, moet u een CI-systeem met een pull-model gebruiken, zoals Dropbox.

De publicatie-eindpunten voor apps in een ILB AS-omgeving maken gebruik van het domein waarmee de ILB AS-omgeving is gemaakt. Dit domein wordt weergegeven in het publicatieprofiel van de app en in de portalblade van de app (**Overzicht** > **Essentials** en ook **Eigenschappen**). Als u een ILB-AS-omgeving met het domeinachtervoegsel *&lt;ASE name&gt;. appserviceenvironment.net* en een app met de naam *mytest* hebt, gebruikt u *mytest.&lt;ASE name&gt;. appserviceenvironment.net* voor FTP en *mytest.scm.contoso.net* voor webimplementatie.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Een ILB AS-omgeving configureren met een WAF-apparaat ##

U kunt een Web Application Firewall-apparaat (WAF) koppelen aan uw ILB-AS-omgeving om alleen de apps weer te geven die u nodig hebt via internet en de rest alleen toegankelijk vanuit het VNet te houden. Zo kunt u onder andere beveiligde toepassingen met meerdere lagen bouwen.

Zie [Een WAF (Web Application Firewall) configureren met uw App Service-omgeving][ASEWAF] voor meer informatie over het configureren van de ILB AS-omgeving met een WAF-apparaat. In dit artikel leest u hoe u een virtueel Barracuda-apparaat gebruikt met de AS-omgeving. Een andere optie is het gebruik van Azure Application Gateway. Application Gateway maakt gebruik van de OWASP-kernregels om alle toepassingen te beveiligen die erachter zijn geplaatst. Zie [Introduction to the Azure web application firewall][AppGW] (Inleiding tot de WAF (Web Application Firewall) in Azure) voor meer informatie over Application Gateway.

## <a name="ilb-ases-made-before-may-2019"></a>ILB AS-omgevingen die zijn gemaakt vóór mei 2019

Voor ILB AS-omgevingen die werden gemaakt vóór 2019, moest u het domeinachtervoegsel instellen tijdens het maken van de AS-omgeving. U moest ook een standaardcertificaat uploaden dat was gebaseerd op het achtervoegsel van dat domein. Met een oudere ILB AS-omgeving is het niet mogelijk om eenmalige aanmelding uit te voeren op de Kudu-console met apps in die ILB AS-omgeving. Bij het configureren van een DNS voor een oudere ILB AS-omgeving moet u het A-record met jokerteken instellen op een zone die overeenkomt met uw domeinachtervoegsel. 

## <a name="get-started"></a>Aan de slag ##

* Zie [Introduction to App Service environments][Intro] (Inleiding tot App Service-omgevingen) om aan de slag te gaan met AS-omgevingen. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
