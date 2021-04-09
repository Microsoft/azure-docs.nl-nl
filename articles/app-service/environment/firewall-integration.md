---
title: Uitgaand verkeer vergren delen
description: Meer informatie over het integreren van Azure Firewall voor het beveiligen van uitgaand verkeer vanuit een App Service omgeving.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: ec506546b52a2d137d448f07f4b7a6827c01b4d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594126"
---
# <a name="locking-down-an-app-service-environment"></a>Een App Service Environment vergren delen

De App Service Environment (ASE) heeft een aantal externe afhankelijkheden waarvoor toegang tot is vereist om goed te kunnen functioneren. De ASE in de Azure-klant Virtual Network (VNet). Klanten moeten het ASE-afhankelijkheids verkeer toestaan. Dit is een probleem voor klanten die alle uitgevallen uit hun VNet willen vergren delen.

Er zijn een aantal inkomende eind punten die worden gebruikt om een ASE te beheren. Het inkomende beheer verkeer kan niet via een firewall apparaat worden verzonden. De bron adressen voor dit verkeer zijn bekend en worden gepubliceerd in het document met [app service Environment-beheer adressen](./management-addresses.md) . Er is ook een servicetag met de naam AppServiceManagement die kan worden gebruikt met netwerk beveiligings groepen (Nsg's) om inkomend verkeer te beveiligen.

De uitgaande afhankelijkheden voor ASE zijn bijna volledig gedefinieerd met FQDN-adressen, die geen statische aflopen van hun. Het ontbreken van statische adressen betekent dat netwerk beveiligings groepen niet kunnen worden gebruikt om het uitgaande verkeer van een ASE te vergren delen. De adressen veranderen vaak voldoende, waardoor er geen regels kunnen worden ingesteld op basis van de huidige oplossing en gebruiken om Nsg's te maken. 

De oplossing voor het beveiligen van uitgaande adressen berust op het gebruik van een firewall apparaat waarmee het uitgaande verkeer op basis van domein namen kan worden beheerd. Azure Firewall kunt het uitgaande HTTP-en HTTPS-verkeer beperken op basis van de FQDN van de bestemming.  

## <a name="system-architecture"></a>Systeemarchitectuur

Voor het implementeren van een ASE met uitgaand verkeer via een firewall apparaat moeten routes worden gewijzigd in het subnet ASE. Routes worden op een IP-niveau toegepast. Als u zich niet bij het definiëren van uw routes bevindt, kunt u TCP-antwoord verkeer afdwingen voor de bron van een ander adres. Als uw antwoord adres afwijkt van het adres verkeer dat is verzonden naar, wordt het probleem asymmetrische route ring genoemd en wordt TCP onderbroken.

Er moeten routes zijn gedefinieerd zodat inkomend verkeer naar de ASE kan reageren op dezelfde manier als waarop het verkeer is binnengekomen. Routes moeten worden gedefinieerd voor inkomende beheer aanvragen en voor inkomende toepassings aanvragen.

Het verkeer van en naar een ASE moet naleven met de volgende conventies

* Het verkeer naar Azure SQL, Storage en Event hub wordt niet ondersteund bij gebruik van een firewall apparaat. Dit verkeer moet rechtstreeks naar deze services worden verzonden. De manier om dat te doen is door service-eind punten voor deze drie services te configureren. 
* Er moeten route tabel regels worden gedefinieerd waarmee binnenkomend beheer verkeer kan worden verzonden vanaf waar het is gearriveerd.
* Er moeten regels voor route tabellen worden gedefinieerd die binnenkomende toepassings verkeer terugsturen van waar ze zijn ontvangen. 
* Al het andere verkeer dat de ASE verlaat, kan met een route tabel regel naar uw firewall apparaat worden verzonden.

![ASE met Azure Firewall-verbindings stroom][5]

## <a name="locking-down-inbound-management-traffic"></a>Inkomend beheer verkeer vergren delen

Als aan uw ASE-subnet nog geen NSG is toegewezen, maakt u er een. Stel binnen de NSG de eerste regel in om verkeer toe te staan van de servicetag met de naam AppServiceManagement op poorten 454, 455. De regel voor het toestaan van toegang vanaf de AppServiceManagement-tag is het enige wat vereist is van open bare Ip's om uw ASE te beheren. De adressen achter die servicetag worden alleen gebruikt voor het beheren van de Azure App Service. Het beheer verkeer dat via deze verbindingen loopt, wordt versleuteld en beveiligd met verificatie certificaten. Typisch verkeer op dit kanaal omvat dingen zoals door de klant geïnitieerde opdrachten en status controles. 

As die via de portal worden gemaakt met een nieuw subnet, worden gemaakt met een NSG die de regel toestaan bevat voor de AppServiceManagement-tag.  

Uw ASE moet ook binnenkomende aanvragen van de Load Balancer tag op poort 16001 toestaan. De aanvragen van de Load Balancer op poort 16001 blijven Alive-controles tussen de Load Balancer en de front-ends van ASE. Als poort 16001 is geblokkeerd, wordt uw ASE beschadigd.

## <a name="configuring-azure-firewall-with-your-ase"></a>Azure Firewall configureren met uw ASE 

De stappen voor het vergren delen van uitgaand verkeer van uw bestaande ASE met Azure Firewall zijn:

1. Schakel service-eind punten in op de SQL-, opslag-en Event hub in uw ASE-subnet. Als u service-eind punten wilt inschakelen, gaat u naar de netwerk Portal > subnetten en selecteert u micro soft. EventHub, micro soft. SQL en micro soft. storage in de vervolg keuzelijst service-eind punten. Wanneer u service-eind punten hebt ingeschakeld voor Azure SQL, moeten alle Azure SQL-afhankelijkheden die uw apps hebben, ook worden geconfigureerd met Service-eind punten. 

   ![Service-eind punten selecteren][2]
  
1. Maak een subnet met de naam AzureFirewallSubnet in het VNet waar uw ASE bestaat. Volg de instructies in de [Azure firewall-documentatie](../../firewall/index.yml) om uw Azure firewall te maken.

1. Selecteer in de Azure Firewall UI-> regels > toepassings regel verzameling de optie toepassings regel verzameling toevoegen. Geef een naam, prioriteit en set toestaan op. Geef in de sectie FQDN-Tags een naam op, stel de bron adressen in op * en selecteer de App Service Environment FQDN-code en de Windows Update. 
   
   ![Toepassings regel toevoegen][1]
   
1. Selecteer in de Azure Firewall GEBRUIKERSINTERFACE > regels > netwerk regel verzameling de optie netwerk regel verzameling toevoegen. Geef een naam, prioriteit en set toestaan op. Geef in de sectie regels onder IP-adressen een naam op, selecteer een protocol van **any**, stel * in op bron-en doel adressen en stel de poorten in op 123. Met deze regel kan het systeem klok synchronisatie uitvoeren met behulp van NTP. Maak een andere regel op dezelfde manier als poort 12000 om eventuele systeem problemen te sorteren. 

   ![NTP-netwerk regel toevoegen][3]
   
1. Selecteer in de Azure Firewall GEBRUIKERSINTERFACE > regels > netwerk regel verzameling de optie netwerk regel verzameling toevoegen. Geef een naam, prioriteit en set toestaan op. Geef in de sectie regels onder service Tags een naam op, selecteer een protocol van **any**, stel * in op bron adressen, selecteer een service-tag van AzureMonitor en stel de poorten in op 80, 443. Met deze regel kan het systeem Azure Monitor met informatie over de status en de metrische gegevens opgeven.

   ![NTP-service label toevoegen netwerk regel][6]
   
1. Een route tabel maken met de beheer adressen van [app service Environment-beheer]( ./management-addresses.md) adressen met een volgende hop van Internet. De vermeldingen in de route tabel zijn vereist om problemen met asymmetrische route ring te voor komen. Voeg routes toe voor de hieronder vermelde IP-adres afhankelijkheden in de IP-adres afhankelijkheden met een volgende hop van Internet. Voeg een route voor virtuele apparaten toe aan de route tabel voor 0.0.0.0/0 met de volgende hop die uw Azure Firewall privé-IP-adres is. 

   ![Een route tabel maken][4]
   
1. Wijs de route tabel toe die u hebt gemaakt in uw ASE-subnet.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Uw ASE achter een firewall implementeren

De stappen voor het implementeren van uw ASE achter een firewall zijn hetzelfde als het configureren van uw bestaande ASE met een Azure Firewall, behalve dat u uw ASE-subnet moet maken en vervolgens de vorige stappen kunt volgen. Als u uw ASE wilt maken in een bestaand subnet, moet u een resource manager-sjabloon gebruiken, zoals beschreven in het document over het [maken van uw ASE met een resource manager-sjabloon](./create-from-template.md).

## <a name="application-traffic"></a>Toepassings verkeer 

Met de bovenstaande stappen kan uw ASE zonder problemen worden uitgevoerd. U moet nog steeds dingen configureren om te voldoen aan de behoeften van uw toepassing. Er zijn twee problemen met toepassingen in een ASE die is geconfigureerd met Azure Firewall.  

- Toepassings afhankelijkheden moeten worden toegevoegd aan de Azure Firewall of de route tabel. 
- Er moeten routes worden gemaakt voor het toepassings verkeer om asymmetrische routerings problemen te voor komen

Als uw toepassingen afhankelijkheden hebben, moeten ze aan uw Azure Firewall worden toegevoegd. Maak toepassings regels om HTTP/HTTPS-verkeer en netwerk regels voor alle andere toe te staan. 

Als u het adres bereik weet dat door het verkeer van de toepassing wordt aangevraagd, kunt u dit toevoegen aan de route tabel die is toegewezen aan uw ASE-subnet. Als het adres bereik groot of niet opgegeven is, kunt u een netwerk apparaat gebruiken zoals de Application Gateway om u een adres te geven dat u aan de route tabel wilt toevoegen. Lees voor meer informatie over het configureren van een Application Gateway met uw ILB ASE het [integreren van uw ILB-ASE met een Application Gateway](./integrate-with-application-gateway.md)

Dit gebruik van de Application Gateway is slechts één voor beeld van het configureren van uw systeem. Als u dit pad hebt gevolgd, moet u een route toevoegen aan de route tabel van het ASE-subnet, zodat het antwoord verkeer dat naar de Application Gateway wordt verzonden, rechtstreeks gaat. 

## <a name="logging"></a>Logboekregistratie 

Azure Firewall kunt logboeken naar Azure Storage, Event hub of Azure Monitor logboeken verzenden. Als u uw app wilt integreren met een ondersteunde bestemming, gaat u naar de Azure Firewall-Portal > Diagnostische logboeken en schakelt u de logboeken voor de gewenste bestemming in. Als u integreert met Azure Monitor-logboeken, kunt u de logboek registratie voor elk verkeer dat naar Azure Firewall wordt verzonden, weer geven. Als u het niet-toegestane verkeer wilt zien, opent u de Log Analytics werkruimte Portal > logboeken en voert u een query in zoals 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Het integreren van uw Azure Firewall met Azure Monitor-Logboeken is handig wanneer u voor het eerst van een toepassing werkt wanneer u niet op de hoogte bent van alle toepassings afhankelijkheden. Meer informatie over Azure Monitor Logboeken vindt u [in azure monitor logboek gegevens analyseren](../../azure-monitor/logs/log-query-overview.md).
 
## <a name="dependencies"></a>Afhankelijkheden

De volgende informatie is alleen vereist als u een ander firewall apparaat dan Azure Firewall wilt configureren. 

- Services die geschikt zijn voor service-eind punten moeten worden geconfigureerd met Service-eind punten.
- Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (TCP-en UDP-verkeer)
- FQDN HTTP/HTTPS-eind punten kunnen worden geplaatst op het apparaat van de firewall.
- Joker teken-HTTP/HTTPS-eind punten zijn afhankelijkheden die kunnen variëren met uw ASE op basis van een aantal kwalificaties. 
- Linux-afhankelijkheden zijn alleen relevant als u Linux-apps in uw ASE implementeert. Als u geen Linux-apps in uw ASE implementeert, hoeft u deze adressen niet aan uw firewall toe te voegen. 

#### <a name="service-endpoint-capable-dependencies"></a>Afhankelijkheden voor service-eind punten 

| Eindpunt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adressen

| Eindpunt | Details |
|----------| ----- |
| \*: 123 | NTP-klok controle. Verkeer wordt gecontroleerd op meerdere eind punten op poort 123 |
| \*: 12000 | Deze poort wordt gebruikt voor een systeem bewaking. Als dit is geblokkeerd, zijn er problemen met de sorteren, maar uw ASE blijft actief. |
| 40.77.24.27:80 | Nodig om problemen met ASE te controleren en te melden |
| 40.77.24.27:443 | Nodig om problemen met ASE te controleren en te melden |
| 13.90.249.229:80 | Nodig om problemen met ASE te controleren en te melden |
| 13.90.249.229:443 | Nodig om problemen met ASE te controleren en te melden |
| 104.45.230.69:80 | Nodig om problemen met ASE te controleren en te melden |
| 104.45.230.69:443 | Nodig om problemen met ASE te controleren en te melden |
| 13.82.184.151:80 | Nodig om problemen met ASE te controleren en te melden |
| 13.82.184.151:443 | Nodig om problemen met ASE te controleren en te melden |

Met een Azure Firewall krijgt u automatisch alle onderstaande instellingen die zijn geconfigureerd met de FQDN-Tags. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-afhankelijkheden 

| Eindpunt |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |
|global-dsms.dsms.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Joker teken-HTTP/HTTPS-afhankelijkheden 

| Eindpunt |
|----------|
|Go-Prod- \* . cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
| \*. identity.azure.net:443 |
| \*. ctldl.windowsupdate.com:80 |
| \*. ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Linux-afhankelijkheden 

| Eindpunt |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
|oryx-cdn.microsoft.io:443 |
| \*. cdn.mscr.io:443 |
| \*. data.mcr.microsoft.com:443 |
|mcr.microsoft.com:443 |
|\*. data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov afhankelijkheden

Volg voor as in US Gov regio's de instructies in de sectie [Azure firewall configureren met uw ASE](#configuring-azure-firewall-with-your-ase) in dit document voor het configureren van een Azure Firewall met uw ASE.

Als u een ander apparaat dan Azure Firewall wilt gebruiken in US Gov 

* Services die geschikt zijn voor service-eind punten moeten worden geconfigureerd met Service-eind punten.
* FQDN HTTP/HTTPS-eind punten kunnen worden geplaatst op het apparaat van de firewall.
* Joker teken-HTTP/HTTPS-eind punten zijn afhankelijkheden die kunnen variëren met uw ASE op basis van een aantal kwalificaties.

Linux is niet beschikbaar in US Gov regio's en wordt dus niet weer gegeven als een optionele configuratie.

#### <a name="service-endpoint-capable-dependencies"></a>Afhankelijkheden voor service-eind punten ####

| Eindpunt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adressen

| Eindpunt | Details |
|----------| ----- |
| \*: 123 | NTP-klok controle. Verkeer wordt gecontroleerd op meerdere eind punten op poort 123 |
| \*: 12000 | Deze poort wordt gebruikt voor een systeem bewaking. Als dit is geblokkeerd, zijn er problemen met de sorteren, maar uw ASE blijft actief. |
| 40.77.24.27:80 | Nodig om problemen met ASE te controleren en te melden |
| 40.77.24.27:443 | Nodig om problemen met ASE te controleren en te melden |
| 13.90.249.229:80 | Nodig om problemen met ASE te controleren en te melden |
| 13.90.249.229:443 | Nodig om problemen met ASE te controleren en te melden |
| 104.45.230.69:80 | Nodig om problemen met ASE te controleren en te melden |
| 104.45.230.69:443 | Nodig om problemen met ASE te controleren en te melden |
| 13.82.184.151:80 | Nodig om problemen met ASE te controleren en te melden |
| 13.82.184.151:443 | Nodig om problemen met ASE te controleren en te melden |

#### <a name="dependencies"></a>Afhankelijkheden ####

| Eindpunt |
|----------|
| \*. ctldl.windowsupdate.com:80 |
| \*. management.usgovcloudapi.net:80 |
| \*. update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*. management.usgovcloudapi.net:443 |
|\*. update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |
|global-dsms.dsms.core.usgovcloudapi.net:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
