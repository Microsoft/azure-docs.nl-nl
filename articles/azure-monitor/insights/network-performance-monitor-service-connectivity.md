---
title: Netwerkprestatiemeter Solution service Connectivity-Azure Log Analytics
description: Gebruik de monitor functie voor service connectiviteit in Netwerkprestatiemeter om de netwerk verbinding te bewaken met een eind punt met een open TCP-poort.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: a980c0987106a80751a98c48dfc5780708e6f889
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573415"
---
# <a name="service-connectivity-monitor"></a>Bewaking van serviceverbinding

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests toevoegen in een bestaande werk ruimte of een nieuwe werk ruimte inschakelen in Netwerkprestatiemeter. U kunt de tests die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u uw tests van Netwerkprestatiemeter naar de nieuwe verbindings monitor](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) in azure Network Watcher vóór 29 februari 2024.

U kunt de monitor functie voor service connectiviteit in [Netwerkprestatiemeter](network-performance-monitor.md) gebruiken om de netwerk verbinding te bewaken met een eind punt met een open TCP-poort. Dergelijke eind punten omvatten websites, SaaS-toepassingen, PaaS-toepassingen en SQL-data bases. 

U kunt de volgende functies uitvoeren met Service Connectivity monitor: 

- Controleer de netwerk verbinding met uw toepassingen en netwerk services van meerdere filialen of locaties. Toepassingen en netwerk services omvatten Microsoft 365, Dynamics CRM, interne line-of-business-toepassingen en SQL-data bases.
- Gebruik ingebouwde tests om de netwerk verbinding met Microsoft 365 en Dynamics 365-eind punten te bewaken. 
- Bepaal de reactie tijd, netwerk latentie en pakket verlies tijdens het maken van verbinding met het eind punt.
- Bepaal of de prestaties van de toepassing worden veroorzaakt door het netwerk of vanwege een probleem met het einde van de toepassings provider.
- Identificeer hot spots op het netwerk die mogelijk een slechte prestaties van de toepassing veroorzaken door de latentie van elke hop op een topologie kaart weer te geven.


![Bewaking van serviceverbinding](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Configuratie 
Als u de configuratie voor Netwerkprestatiemeter wilt openen, opent u de [Netwerkprestatiemeter oplossing](network-performance-monitor.md) en selecteert **u configureren**.

![Netwerkprestatiemeter configureren](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics agents voor bewaking configureren
Schakel de volgende firewall regels in op de knoop punten die worden gebruikt voor bewaking, zodat de oplossing de topologie van uw knoop punten kan detecteren in het service-eind punt: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Tests voor service connectiviteits monitors maken 

Begin met het maken van uw tests om de netwerk verbinding met de service-eind punten te bewaken.

1. Selecteer het tabblad **monitor voor service connectiviteit** .
2. Selecteer **test toevoegen** en voer de naam en beschrijving van de test in. U kunt Maxi maal 450 tests per werk ruimte maken. 
3. Selecteer het type test:<br>

    * Selecteer **Web** voor het controleren van de verbinding met een service die reageert op http/S-aanvragen, zoals outlook.office365.com of Bing.com.<br>
    * Selecteer **netwerk** om de verbinding met een service te bewaken die op TCP-aanvragen reageert, maar niet reageert op http/S-aanvragen, zoals een SQL-Server, FTP-server of SSH-poort. 
    * Bijvoorbeeld: als u een webtest wilt maken naar een Blob Storage-account, selecteert u **Web** en voert u doel in als *yourstorageaccount*. blob.core.Windows.net. Op dezelfde manier kunt u tests maken voor andere tabel opslag, wachtrij opslag en Azure Files met behulp van [deze koppeling.](../../storage/common/storage-account-overview.md#storage-account-endpoints)
4. Als u geen netwerk metingen wilt uitvoeren, zoals netwerk latentie, pakket verlies en topologie detectie, schakelt u het selectie vakje **netwerk metingen uitvoeren** uit. Zorg ervoor dat deze optie is geselecteerd om Maxi maal voor deel te halen uit de mogelijkheid. 
5. In **doel** voert u de URL/FQDN/IP-adres in waarmee u de netwerk verbinding wilt bewaken.
6. In **poort nummer** voert u het poort nummer van de doel service in. 
7. In **test frequentie** voert u een waarde in voor hoe vaak de test moet worden uitgevoerd. 
8. Selecteer de knoop punten waarvan u de netwerk verbinding met de service wilt bewaken. Zorg ervoor dat het aantal agents dat per test is toegevoegd, kleiner is dan 150. Elke agent kan Maxi maal 150 eind punten/agents testen.

    >[!NOTE]
    > Voor knoop punten op basis van Windows Server gebruikt de mogelijkheid TCP-aanvragen om de netwerk metingen uit te voeren. Voor knoop punten op basis van Windows-clients gebruikt de mogelijkheid ICMP-gebaseerde aanvragen voor het uitvoeren van de netwerk metingen. In sommige gevallen blokkeert de doel toepassing binnenkomende ICMP-gebaseerde aanvragen wanneer de knoop punten op Windows-clients zijn gebaseerd. De oplossing kan geen netwerk metingen uitvoeren. In dergelijke gevallen kunt u het beste Windows Server-knoop punten gebruiken. 

9. Als u geen status gebeurtenissen wilt maken voor de items die u selecteert, schakelt u **status controle inschakelen in de doelen waarop deze test betrekking** heeft. 
10. Kies bewakings voorwaarden. U kunt aangepaste drempel waarden instellen voor het genereren van status gebeurtenissen door drempel waarde in te voeren. Wanneer de waarde van de voor waarde boven de geselecteerde drempelwaarde voor het geselecteerde netwerk of subnetwerk komt, wordt een status gebeurtenis gegenereerd. 
11. Selecteer **Opslaan** om de configuratie op te slaan. 

    ![Test configuraties voor service verbindings monitor](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Walkthrough 

Ga naar de Netwerkprestatiemeter dashboard weergave. Bekijk de pagina **service Connectivity monitor** voor een overzicht van de status van de verschillende tests die u hebt gemaakt. 

![Pagina Service Connectivity monitor](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Selecteer de tegel om de details van de tests op de pagina **tests** weer te geven. In de tabel aan de linkerkant kunt u de tijdgebonden status en waarde van de reactie tijd van de service, netwerk latentie en pakket verlies voor alle tests weer geven. Gebruik het besturings element netwerk status recorder om de netwerk momentopname op een ander tijdstip in het verleden te bekijken. Selecteer de test in de tabel die u wilt onderzoeken. In de grafieken in het deel venster aan de rechter kant kunt u de historische trend van de waarden voor verlies, latentie en reactie tijd bekijken. Selecteer de koppeling **Test Details** om de prestaties van elk knoop punt weer te geven.

![Monitor tests voor service connectiviteit](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

In de weer gave **test knooppunten** kunt u de netwerk verbinding van elk knoop punt bekijken. Selecteer het knoop punt waarop de prestaties verslechteren. Dit is het knoop punt waar de toepassing langzaam wordt uitgevoerd.

Bepaal of de prestaties van de toepassing goed zijn vanwege het netwerk of een probleem op het einde van de toepassings provider door de correlatie tussen de reactie tijd van de toepassing en de netwerk latentie te bestuderen. 

* **Toepassings probleem:** Een piek in de reactie tijd, maar consistentie in de netwerk latentie geeft aan dat het netwerk goed werkt en het probleem kan worden veroorzaakt door een probleem met het einde van de toepassing. 

    ![Toepassings probleem van de service connectiviteit controleren](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Netwerk probleem:** Een piek in reactie tijd, vergezeld van een overeenkomende Prikker in netwerk latentie, geeft aan dat de toename van de reactie tijd kan worden veroorzaakt door een toename van de netwerk latentie. 

    ![Netwerk probleem bewaking service connectiviteit](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Nadat u hebt vastgesteld dat het probleem is veroorzaakt door het netwerk, selecteert u de koppeling **topologie** weergave om de lastige-hop op de topologie kaart te identificeren. Er wordt een voor beeld weer gegeven in de volgende afbeelding. Van de totale latentie van 105 MS tussen het knoop punt en het eind punt van de toepassing, 96 MS wordt veroorzaakt door de hop die rood is gemarkeerd. Nadat u de lastige-hop hebt geïdentificeerd, kunt u corrigerende maat regelen nemen. 

![Eindpunt topologie van de service verbindings monitor](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostiek 

Ga als volgt te werk als u een abnormale procedure ziet:

* Als de reactie tijd van de service, het netwerk verlies en de latentie worden weer gegeven als N.V.T., kunnen de volgende redenen de oorzaak zijn:

    - De toepassing is niet beschikbaar.
    - Het knoop punt dat wordt gebruikt voor het controleren van de netwerk verbinding met de service is niet beschikbaar.
    - Het doel dat is ingevoerd in de test configuratie is onjuist.
    - Het knoop punt heeft geen netwerk verbinding.

* Als er een geldige reactie tijd van de service wordt weer gegeven, maar netwerk verlies en latentie worden weer gegeven als N.V.T., kunnen de volgende redenen zijn:

    - Als het knoop punt dat wordt gebruikt om de netwerk verbinding met de service te controleren, een Windows-client computer is, blokkeert de doel service ICMP-aanvragen of een netwerk firewall blokkeert ICMP-aanvragen die afkomstig zijn van het knoop punt.
    - Het selectie vakje **netwerk metingen uitvoeren** is leeg in de test configuratie. 

* Als de reactie tijd van de service NA, maar netwerk verlies en latentie geldig zijn, is de doel service mogelijk geen webtoepassing. Bewerk de configuratie van de test en kies het test type **netwerk** in plaats van het **Web**. 

* Als de toepassing langzaam wordt uitgevoerd, bepaalt u of de prestaties van de toepassing worden veroorzaakt door het netwerk of door een probleem met het einde van de toepassings provider.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office-Url's voor Amerikaanse overheids klanten
Voor de regio van de Amerikaanse overheids-Virginia zijn alleen DOD-Url's ingebouwd NPM. Klanten die GCC-Url's gebruiken, moeten aangepaste tests maken en elke URL afzonderlijk toevoegen.

| Veld | GCC |
|:---   |:--- |
| Office 365-Portal en gedeeld | portal.apps.mil |
| Office 365-verificatie en-identiteit | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS-teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Volgende stappen
[Zoek logboeken](../logs/log-query-overview.md) om gedetailleerde gegevens records voor netwerk prestaties weer te geven.

