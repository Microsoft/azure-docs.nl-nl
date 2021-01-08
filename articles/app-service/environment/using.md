---
title: Een App Service-omgeving gebruiken
description: Meer informatie over het gebruik van uw App Service Environment om geïsoleerde toepassingen te hosten.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c0ceae8727681c045c3bbf3e6626937633b38997
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013529"
---
# <a name="using-an-app-service-environment"></a>Een App Service Environment gebruiken

De App Service Environment (ASE) is een implementatie van één Tenant van de Azure App Service die rechtstreeks in een Azure-Virtual Network (VNet) van uw keuze wordt ingevoerd. Het is een systeem dat slechts door één klant wordt gebruikt. Apps die zijn geïmplementeerd in de ASE, zijn onderhevig aan de netwerk functies die worden toegepast op het ASE-subnet. Er zijn geen extra functies die moeten worden ingeschakeld op uw apps om aan deze netwerk functies te kunnen voldoen. 

## <a name="create-an-app-in-an-ase"></a>Een app maken in een ASE

Als u een app in een ASE wilt maken, gebruikt u hetzelfde proces als wanneer u normaal gesp roken een app maakt, maar met enkele kleine verschillen. Wanneer u een nieuw App Service plan maakt:

- In plaats van een geografische locatie te kiezen voor het implementeren van uw app, kiest u een ASE als uw locatie.
- Alle App Service plannen die in een ASE zijn gemaakt, kunnen zich alleen in de prijs categorie geïsoleerd v2 bevindt.

Als u geen ASE hebt, kunt u er een maken door de instructies in [Create a app service Environment][MakeASE]te volgen.

Een app maken in een ASE:

1. Selecteer **een resource maken**  >  **Web en mobiel**  >  **Web-app**.

1. Selecteer een abonnement.

1. Voer een naam in voor een nieuwe resource groep of selecteer **bestaande gebruiken** en selecteer er een in de vervolg keuzelijst.

1. voer een naam voor de app in. Als u al een App Service plan in een ASE hebt geselecteerd, komt de domein naam voor de app overeen met de domein naam van de ASE:

    ![een app maken in een ASE][1]

1. Selecteer het publicatie type, de stack en het besturings systeem.

1.  Regio selecteren. Hier moet u een reeds bestaande App Service Environment v3 selecteren.  U kunt geen ASEv3 maken tijdens het maken van een app 

1. Selecteer een bestaand App Service plan in uw ASE of maak een nieuw abonnement. Als u een nieuwe app maakt, selecteert u de gewenste grootte voor het App Service plan. De enige SKU die u voor uw app kunt selecteren, is een geïsoleerde v2-prijs-SKU.

    ![Geïsoleerde v2-prijs Categorieën][2]

    > [!NOTE]
    > Linux-apps en Windows-apps kunnen zich niet in hetzelfde App Service plan bevinden, maar ze kunnen zich in dezelfde App Service Environment bevinden.
    >

1. Selecteer * * volgende: bewaking * * als u app Insights met uw app wilt inschakelen, kunt u dit hier doen tijdens de aanmaak stroom. 

1.  Selecteer **volgende: Labels** labels toevoegen aan de app  

1. Selecteer **controleren + maken**, Controleer of de gegevens juist zijn en selecteer vervolgens **maken**.

## <a name="how-scale-works"></a>Hoe schalen werkt

Elke App Service-app wordt uitgevoerd in een App Service plan. App Service omgevingen houden App Service plannen en App Service abonnementen op apps. Wanneer u een app schaalt, kunt u ook het App Service plan en alle apps in hetzelfde abonnement schalen.

Wanneer u een App Service plan schaalt, wordt de benodigde infra structuur automatisch toegevoegd. Er is een vertraging opgetreden bij het schalen van bewerkingen terwijl de infra structuur wordt toegevoegd. Wanneer u een App Service plan schaalt, wachten alle andere schaal bewerkingen die zijn aangevraagd voor hetzelfde besturings systeem en de grootte, totdat de eerste is voltooid. Nadat de blokkerings schaal bewerking is voltooid, worden alle aanvragen in de wachtrij op hetzelfde moment verwerkt. Een schaal bewerking op één grootte en het besturings systeem blokkeert niet het schalen van de andere Combi Naties van grootte en besturings systeem. Als u bijvoorbeeld een planning voor Windows I2v2 App Service hebt geschaald, worden alle andere aanvragen voor het schalen van Windows I2v2 in die ASE in de wachtrij geplaatst totdat deze is voltooid.   

In de multi tenant-App Service is het schalen direct omdat een groep resources eenvoudig beschikbaar is om het te ondersteunen. In een ASE zijn er geen buffers en worden er resources toegewezen op basis van de behoefte.

## <a name="app-access"></a>App-toegang

In een ASE is het domein achtervoegsel dat wordt gebruikt voor het maken van de app *. &lt; asename &gt; . appserviceenvironment.net*. Als uw ASE de naam _mijn-ASE_ heeft en u een app met de naam _CONTOSO_ in die ASE host, kunt u deze op de volgende url's bereiken:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Zie [een app service Environment maken][MakeASE]voor informatie over het maken van een ASE.

De SCM-URL wordt gebruikt voor toegang tot de kudu-console of voor het publiceren van uw app met behulp van Web Deploy. Zie [kudu-console voor Azure app service][Kudu]voor meer informatie over de kudu-console. De kudu-console biedt u een webinterface voor het opsporen van fouten, het uploaden van bestanden, het bewerken van bestanden en nog veel meer.

### <a name="dns-configuration"></a>DNS-configuratie 

De ASE maakt gebruik van privé-eind punten voor inkomend verkeer. Het wordt niet automatisch geconfigureerd met Azure DNS persoonlijke zones. Als u uw eigen DNS-server wilt gebruiken, moet u de volgende records toevoegen:

1. Maak een zone voor &lt;ASE name&gt;. appserviceenvironment.net
1. een A-record in die zone maken die verwijst naar * naar het binnenkomende IP-adres dat wordt gebruikt door uw persoonlijke ASE-eind punt
1. een A-record in die zone maken die verwijst naar @ naar het inkomende IP-adres dat wordt gebruikt door uw ASE-privé-eind punt
1. Maak een zone in &lt;ASE name&gt;. appserviceenvironment.net met de naam SCM
1. een A-record maken in de SCM-zone die * verwijst naar het IP-adres dat wordt gebruikt door uw persoonlijke ASE-eind punt

DNS configureren in Azure DNS particuliere zones:

1. Maak een Azure DNS privézone met de naam <ASE name>. appserviceenvironment.net
1. Maak in die zone een A-record die * verwijst naar het IP-adres van de ILB
1. Maak in die zone een A-record die @ verwijst naar het IP-adres van de ILB
1. Maak in die zone een A-record die *.scm verwijst naar het IP-adres van de ILB

De DNS-instellingen voor het standaard domein achtervoegsel van uw ASE beperken uw apps niet zodat deze alleen toegankelijk zijn voor die namen. U kunt een aangepaste domein naam instellen zonder validatie voor uw apps in een ASE. Als u vervolgens een zone met de naam *contoso.net* wilt maken, kunt u dit doen en deze naar het binnenkomende IP-adres laten wijzen. De aangepaste domein naam werkt voor app-aanvragen, maar niet voor de SCM-site. De SCM-site is alleen beschikbaar op *&lt; AppName &gt; . scm. &lt; asename &gt; . appserviceenvironment.net*. 

## <a name="publishing"></a>Publiceren

In een ASE kunt u, net als bij de multi tenant-App Service, de volgende methoden publiceren:

- Webimplementatie
- Continue integratie (CI)
- Slepen en neerzetten in de kudu-console
- Een IDE, zoals Visual Studio, eclips of IntelliJe idee

Met een ASE zijn de publicatie-eind punten alleen beschikbaar via het inkomende adres dat door het persoonlijke eind punt wordt gebruikt. Als u geen netwerk toegang hebt tot het adres van het persoonlijke eind punt, kunt u geen apps op die ASE publiceren.  Uw Ide's moet ook netwerk toegang hebben tot de ILB om er rechtstreeks naar te publiceren.

Zonder extra wijzigingen werken op internet gebaseerde CI-systemen zoals GitHub en Azure DevOps niet met een ILB ASE omdat het publicatie-eind punt niet toegankelijk is via internet. U kunt publiceren naar een ILB ASE inschakelen vanuit Azure DevOps door een zelf-hostende release agent te installeren in het virtuele netwerk dat de ILB ASE bevat. 

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
1. Selecteer **Diagnostische instellingen toevoegen**.
1. Geef een naam op voor de logboek integratie.
1. Selecteer en configureer de gewenste logboek doelen.
1. Selecteer **AppServiceEnvironmentPlatformLogs**.

![Instellingen voor Diagnostische logboeken van ASE][4]

Als u integreert met Log Analytics, kunt u de logboeken bekijken door **Logboeken** te selecteren in de ASE-Portal en een query te maken op basis van **AppServiceEnvironmentPlatformLogs**. Logboeken worden alleen verzonden wanneer uw ASE een gebeurtenis heeft die het kan activeren. Als uw ASE geen gebeurtenis heeft, zijn er geen logboeken. Als u snel een voor beeld van Logboeken in uw Log Analytics-werk ruimte wilt weer geven, voert u een schaal bewerking uit met een van de App Service plannen in uw ASE. U kunt vervolgens een query uitvoeren op **AppServiceEnvironmentPlatformLogs** om deze logboeken te bekijken. 

**Een waarschuwing maken**

Volg de instructies in [logboek waarschuwingen maken, weer geven en beheren met Azure monitor](../../azure-monitor/platform/alerts-log.md)om een waarschuwing te maken voor uw logboeken. In het kort:

* Open de pagina waarschuwingen in uw ASE-Portal
* **Nieuwe waarschuwings regel** selecteren
* Selecteer uw resource als Log Analytics werk ruimte
* Stel uw voor waarde in met een aangepaste zoek opdracht in Logboeken voor het gebruik van een query als ' AppServiceEnvironmentPlatformLogs | waarbij ResultDescription "is begonnen met schalen" of wat u wilt. Stel de drempel waarde in als dat nodig is. 
* Een actie groep toevoegen of maken naar wens. De actie groep is de locatie waar u het antwoord op de waarschuwing definieert, zoals het verzenden van een e-mail of SMS-bericht
* Geef een naam op voor de waarschuwing en sla deze op.

## <a name="internal-encryption"></a>Interne versleuteling

De App Service Environment fungeert als een zwarte doossysteem waarin u de interne onderdelen of de communicatie binnen het systeem niet kunt zien. Voor een hogere doorvoer is versleuteling niet standaard ingeschakeld tussen interne onderdelen. Het systeem is veilig omdat het verkeer op geen enkele manier worden bekeken of geopend. Als u een nalevings vereiste hebt die een volledige versleuteling vereist van het gegevenspad van end-to-end-versleuteling, kunt u dit inschakelen in de gebruikers interface van de ASE- **configuratie** .

![Interne versleuteling inschakelen][5]

Hiermee versleutelt u het interne netwerkverkeer in uw ASE tussen de front-ends en werkrollen, versleutelt u het wisselbestand en versleutelt u ook de schijven van werkrollen. Nadat de InternalEncryption clusterSetting is ingeschakeld, kan dit gevolgen hebben voor de prestaties van uw systeem. Wanneer u de wijziging aanbrengt om InternalEncryption in te schakelen, heeft uw ASE een instabiele status totdat de wijziging volledig is doorgegeven. Het voltooien van de wijziging kan enkele uren duren, afhankelijk van het aantal instanties dat u in uw ASE hebt. We raden u ten zeerste aan dit niet in te schakelen op een ASE terwijl deze in gebruik is. Als u dit wilt inschakelen op een actief gebruikte ASE, wordt u ten zeerste aangeraden om verkeer door te sturen naar een back-upomgeving totdat de bewerking is voltooid.

## <a name="upgrade-preference"></a>Upgrade voorkeur

Als u meerdere as hebt, is het mogelijk dat u wilt dat sommige as worden bijgewerkt vóór andere. In het object ASE **Hosting Environment Resource Manager** kunt u een waarde instellen voor **upgradePreference**. De instelling **upgradePreference** kan worden geconfigureerd met behulp van een sjabloon, ARMClient of https://resources.azure.com . De drie mogelijke waarden zijn:

- **Geen**: Azure zal uw ASE bijwerken in een bepaalde batch. Dit is de standaardwaarde.
- **Vroeg**: uw ASE wordt bijgewerkt in de eerste helft van de app service upgrades.
- **Te laat**: uw ASE wordt bijgewerkt in de tweede helft van de app service upgrades.

Als u de voor keur voor de upgrade wilt configureren, gaat u naar de gebruikers interface van de ASE- **configuratie** . 

De functie **upgradePreferences** is het meest zinnig wanneer u meerdere as hebt, omdat uw ' early ' as wordt bijgewerkt vóór uw ' late ' as. Wanneer u meerdere as hebt, moet u uw ontwikkel-en test as zo instellen dat deze ' vroeg ' zijn en dat uw productie as ' laat ' is.

## <a name="delete-an-ase"></a>Een ASE verwijderen

Een ASE verwijderen:

1. Selecteer **verwijderen** boven aan het deel venster **app service Environment** .

1. Voer de naam van uw ASE in om te bevestigen dat u deze wilt verwijderen. Wanneer u een ASE verwijdert, verwijdert u ook alle inhoud erin.

    ![ASE verwijderen][3]

1. Selecteer **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
