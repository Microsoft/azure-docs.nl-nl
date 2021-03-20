---
title: Architecturen voor het implementeren van Oracle-apps op Azure Virtual Machines | Microsoft Docs
description: Toepassings architecturen voor het implementeren van Oracle-apps met inbegrip van E-Business Suite, JD Edwards EnterpriseOne en People Soft op Microsoft Azure virtuele machines met data bases in azure of in OCI (Oracle Cloud Infrastructure).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: 862bb886c7ec5dfd40c7acdbae2f70f6698a711b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669936"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architecturen voor het implementeren van Oracle-toepassingen in azure

Micro soft en Oracle hebben samengewerkt om klanten in staat te stellen Oracle-toepassingen zoals Oracle E-Business Suite, JD Edwards EnterpriseOne en People Soft in de cloud te implementeren. Met de introductie van de preview-versie van het [particuliere netwerk interconnectiviteit](configure-azure-oci-networking.md) tussen Microsoft Azure en Oracle Cloud Infrastructure (OCI), kunnen Oracle-toepassingen nu worden geïmplementeerd op Azure met hun back-end-data bases in azure of OCI. Oracle-toepassingen kunnen ook worden geïntegreerd met Azure Active Directory, zodat u eenmalige aanmelding kunt instellen, zodat gebruikers zich kunnen aanmelden bij de Oracle-toepassing met behulp van de referenties van hun Azure Active Directory (Azure AD).

OCI biedt meerdere Oracle-database opties voor Oracle-toepassingen, waaronder DBaaS, Exadata Cloud service, Oracle RAC en Infrastructure-as-a-Service (IaaS). De autonome data base is momenteel geen ondersteunde back-end voor Oracle-toepassingen.

Er zijn [meerdere opties](oracle-overview.md) voor het implementeren van Oracle-toepassingen in azure, met inbegrip van een Maxi maal beschik bare en veilige manier. Azure biedt ook [Oracle data base-VM-installatie kopieën](oracle-vm-solutions.md) die u kunt implementeren als u ervoor kiest om uw Oracle-toepassingen volledig uit te voeren op Azure.

De volgende secties bevatten een overzicht van architectuur aanbevelingen voor zowel micro soft als Oracle voor het implementeren van Oracle E-Business Suite, JD Edwards EnterpriseOne en People Soft in een configuratie voor meerdere Clouds of volledig in Azure. Micro soft en Oracle hebben deze toepassingen getest en bevestigd dat de prestaties voldoen aan de standaarden die door Oracle zijn ingesteld voor deze toepassingen.

## <a name="architecture-considerations"></a>Architectuur overwegingen

Oracle-toepassingen bestaan uit meerdere services die kunnen worden gehost op dezelfde of meerdere virtuele machines in Azure en optioneel in OCI. 

Toepassings exemplaren kunnen worden ingesteld met privé-of open bare eind punten. Micro soft en Oracle raden aan om een *bastion-host-VM* in te stellen met een openbaar IP-adres in een apart subnet voor beheer van de toepassing. Wijs vervolgens alleen privé IP-adressen toe aan de andere computers, met inbegrip van de data base-laag. 

Bij het instellen van een toepassing in een architectuur met meerdere Clouds is planning vereist om ervoor te zorgen dat de IP-adres ruimte in het virtuele netwerk van Azure de privé-IP-adres ruimte in het OCI virtuele Cloud netwerk niet overlapt.

Voor extra beveiliging stelt u netwerk beveiligings groepen op subnetniveau in om ervoor te zorgen dat alleen verkeer op specifieke poorten en IP-adressen is toegestaan. Machines in de middelste laag moeten bijvoorbeeld alleen verkeer ontvangen van binnen het virtuele netwerk. Externe verkeer moet de machines van de middelste laag rechtstreeks bereiken.

Voor maximale Beschik baarheid kunt u redundante exemplaren van de verschillende servers instellen in dezelfde beschikbaarheidsset of in verschillende beschikbaarheids zones. Met beschikbaarheids zones kunt u een SLA van 99,99% behaalt, terwijl u met beschikbaarheids sets een SLA van 99,95% in de regio kunt bezorgen. Voor beelden van architecturen die in dit artikel worden weer gegeven, worden in twee beschikbaarheids zones geïmplementeerd.

Wanneer u een toepassing implementeert met behulp van de cross-Cloud Interconnect, kunt u een bestaand ExpressRoute-circuit blijven gebruiken om uw Azure-omgeving te verbinden met uw on-premises netwerk. U hebt echter een apart ExpressRoute-circuit nodig voor de Interconnect naar OCI dan de verbinding met uw on-premises netwerk.

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) is een suite van toepassingen, waaronder Supply Chain Management (SCM) en Customer Relationship Management (CRM). Als u wilt profiteren van de beheerde data base-portefeuille van OCI, kan EBS worden geïmplementeerd met behulp van de cross-Cloud Interconnect tussen Microsoft Azure en OCI. In deze configuratie worden de presentatie-en toepassings lagen uitgevoerd in Azure en de database laag in OCI, zoals wordt geïllustreerd in het volgende architectuur diagram (afbeelding 1).

![Architectuur van E-Business Suite voor meerdere clouds](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Afbeelding 1: cross-Cloud Architecture van E-Business Suite* 

In deze architectuur is het virtuele netwerk in azure verbonden met een virtueel Cloud netwerk in OCI met behulp van de cross-Cloud Interconnect. De toepassingslaag is ingesteld in azure, terwijl de data base is ingesteld in OCI. U kunt het beste elk onderdeel implementeren in een eigen subnet met netwerk beveiligings groepen om alleen verkeer toe te staan van specifieke subnetten op specifieke poorten.

De architectuur kan ook worden aangepast voor implementatie in azure met Maxi maal beschik bare Oracle-data bases die zijn geconfigureerd met Oracle Data Guard in twee beschikbaarheids zones in een regio. Het volgende diagram (afbeelding 2) is een voor beeld van dit architectuur patroon:

![Alleen Azure-architectuur voor E-Business Suite](media/oracle-oci-applications/ebs-arch-azure.png)

*Afbeelding 2: alleen Azure-architectuur voor E-Business Suite*

In de volgende secties worden de verschillende onderdelen op een hoog niveau beschreven.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Toepassing (middelste) laag

De toepassingslaag is geïsoleerd in een eigen subnet. Er zijn meerdere virtuele machines ingesteld voor fout tolerantie en eenvoudig patch beheer. Deze Vm's kunnen worden ondersteund door gedeelde opslag, die wordt aangeboden door Azure NetApp Files en ultra Ssd's. Met deze configuratie kunt u eenvoudig patches implementeren zonder uitval tijd. De computers in de toepassingslaag moeten worden gefronteerd door een open bare load balancer zodat aanvragen voor de EBS worden verwerkt, zelfs als een computer in de laag offline is vanwege een fout.

### <a name="load-balancer"></a>Load balancer

Met een Azure-load balancer kunt u verkeer verdelen over meerdere exemplaren van uw workload om hoge Beschik baarheid te garanderen. In dit geval wordt een open bare load balancer ingesteld, omdat gebruikers via het web toegang hebben tot de toepassing EBS. De load balancer distribueert de belasting naar beide computers in de middelste laag. Voor extra beveiliging is alleen verkeer toegestaan van gebruikers die toegang hebben tot het systeem vanuit uw bedrijfs netwerk met behulp van een site-naar-site VPN-of ExpressRoute-en netwerk beveiligings groepen.

### <a name="database-tier"></a>Database laag

Deze laag fungeert als host voor de Oracle-data base en wordt onderverdeeld in een eigen subnet. Het is raadzaam om netwerk beveiligings groepen toe te voegen die alleen verkeer van de toepassingslaag toestaan aan de data base-laag op de Oracle-specifieke database poort 1521.

Micro soft en Oracle raden een configuratie met hoge Beschik baarheid aan. Hoge Beschik baarheid in azure kan worden bereikt door twee Oracle-data bases in te stellen in twee beschikbaarheids zones met Oracle Data Guard, of door Oracle Database Exadata-Cloud service in OCI te gebruiken. Wanneer u Oracle Database Exadata Cloud service gebruikt, wordt uw data base geïmplementeerd in twee subnetten. U kunt ook Oracle Database instellen in Vm's in OCI in twee beschik bare domeinen met Oracle Data Guard.


### <a name="identity-tier"></a>Identiteits categorie

De id-laag bevat de EBS Assertion-VM. Met EBS Assertion kunt u identiteiten synchroniseren vanuit Oracle Identity Cloud service (IDCS) en Azure AD. De EBS-bevestigings verklaring is vereist omdat EBS geen ondersteuning biedt voor eenmalige aanmeldings protocollen zoals SAML 2,0 of OpenID Connect Connect. De EBS-Assertie gebruikt de OpenID Connect Connect-token (gegenereerd door IDCS), valideert deze en maakt vervolgens een sessie voor de gebruiker in EBS. 

Hoewel deze architectuur IDCS-integratie toont, kan Azure AD Unified Access en eenmalige aanmelding ook worden ingeschakeld met Oracle Access Manager met Oracle Internet Directory of Oracle Unified Directory. Zie voor meer informatie het Witboek over het [implementeren van Oracle EBS met IDCS Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) of het [implementeren van Oracle EBS met OAM-integratie](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Voor maximale Beschik baarheid kunt u het beste redundante servers van de EBS-Assertie implementeren in meerdere beschikbaarheids zones met een load balancer.

Zodra de infra structuur is ingesteld, kan E-Business Suite worden geïnstalleerd door de installatie handleiding van Oracle te volgen.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

De JD Edwards EnterpriseOne van Oracle is een geïntegreerd toepassings pakket met uitgebreide software voor het plannen van bedrijfs resources. Het is een toepassing met meerdere lagen die kan worden ingesteld met een Oracle-of SQL Server-back-end van de data base. In deze sectie wordt beschreven hoe u JD Edwards EnterpriseOne implementeert met een Oracle data base-back-end in OCI of in Azure.

In de volgende aanbevolen architectuur (afbeelding 3) worden de beheer-, presentatie-en middelste lagen geïmplementeerd in het virtuele netwerk in Azure. De data base wordt geïmplementeerd in een virtueel Cloud netwerk in OCI.

Net als bij E-Business Suite kunt u een optionele Bastion-laag instellen voor veilige administratieve doel einden. Gebruik de Bastion VM host als een Jump server voor toegang tot de toepassing en de data base-exemplaren.

![JD Edwards EnterpriseOne-architectuur voor meerdere clouds](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Afbeelding 3: JD Edwards EnterpriseOne-architectuur voor meerdere clouds*

In deze architectuur is het virtuele netwerk in azure verbonden met het virtuele Cloud netwerk in OCI met behulp van de cross-Cloud Interconnect. De toepassingslaag is ingesteld in azure, terwijl de data base is ingesteld in OCI. U kunt het beste elk onderdeel implementeren in een eigen subnet met netwerk beveiligings groepen om alleen verkeer toe te staan van specifieke subnetten op specifieke poorten.

De architectuur kan ook worden aangepast voor implementatie in azure met Maxi maal beschik bare Oracle-data bases die zijn geconfigureerd met Oracle Data Guard in twee beschikbaarheids zones in een regio. Het volgende diagram (afbeelding 4) is een voor beeld van dit architectuur patroon:

![JD Edwards EnterpriseOne architectuur voor alleen Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Afbeelding 4: JD Edwards EnterpriseOne architectuur voor alleen Azure*

In de volgende secties worden de verschillende onderdelen op een hoog niveau beschreven.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Administratieve laag

Zoals de naam wordt voorgesteld, wordt deze laag gebruikt voor beheer taken. U kunt een afzonderlijk subnet reserverenen voor de administratieve laag. De services en servers in deze laag worden voornamelijk gebruikt voor de installatie en het beheer van de toepassing. Daarom zijn enkele instanties van deze servers voldoende. Overbodige exemplaren zijn niet vereist voor de hoge Beschik baarheid van uw toepassing.

De onderdelen van deze laag zijn als volgt:
    
 - **Inrichtings server** : deze server wordt gebruikt voor end-to-end-implementatie van de verschillende onderdelen van de toepassing. De service communiceert met de instanties in de andere lagen, met inbegrip van de instanties in de data base-laag, via poort 22. Het fungeert als host voor de Serverbeheer-console voor JD Edwards EnterpriseOne.
 - **Implementatie server** : deze server is voornamelijk vereist voor de installatie van JD Edwards EnterpriseOne. Tijdens het installatie proces fungeert deze server als de centrale opslag plaats voor vereiste bestanden en installatie pakketten. De software wordt gedistribueerd of geïmplementeerd op andere servers en clients van deze server.
 - **Development-client** : deze server bevat onderdelen die worden uitgevoerd in een webbrowser, evenals systeem eigen toepassingen.

### <a name="presentation-tier"></a>Presentatielaag

Deze laag bevat verschillende onderdelen, zoals Application Interface Services (AIS), Application Development Framework (ADF) en Java Application servers (JAS). De servers in deze laag communiceren met de servers in de middelste laag. Ze worden getransporteerd door een load balancer die verkeer naar de benodigde server routeert op basis van het poort nummer en de URL waarop het verkeer wordt ontvangen. Het wordt aanbevolen dat u meerdere exemplaren van elk server type implementeert voor hoge Beschik baarheid.

Hier volgen de onderdelen in deze laag:
    
- **Application Interface Services (AIS)** : de AIS-server biedt de communicatie interface tussen JD Edwards EnterpriseOne Mobile Enter prise-toepassingen en JD Edwards EnterpriseOne.
- **Java-toepassings server (jas)** : de jas ontvangt aanvragen van de Load Balancer en geeft deze door aan de middelste laag om complexe taken uit te voeren. De JAS biedt de mogelijkheid om eenvoudige bedrijfs logica uit te voeren.
- **Bi Publisher-server (BIP)** : deze server geeft rapporten weer op basis van de gegevens die zijn verzameld door de JD Edwards EnterpriseOne-toepassing. U kunt ontwerpen en bepalen hoe het rapport de gegevens presenteert op basis van verschillende sjablonen.
- **Business Services-server (BSS)** : de BSS maakt uitwisseling en interoperabiliteit van gegevens mogelijk met andere Oracle-toepassingen.
- **Real-time Events server (RTE)** : de RTE-server stelt u in staat om meldingen in te stellen op externe systemen over trans acties die in het JDE EnterpriseOne-systeem plaatsvinden. Er wordt gebruikgemaakt van een abonnee model en Hiermee kunnen systemen van derden zich abonneren op gebeurtenissen. Zorg ervoor dat de servers zich in een cluster bevinden om aanvragen voor beide RTE-servers te verdelen.
- **Application Development Framework-server (ADF)** : de ADF-server wordt gebruikt voor het uitvoeren van JD Edwards EnterpriseOne-toepassingen die zijn ontwikkeld met Oracle ADF. Dit wordt geïmplementeerd op een Oracle WebLogic-Server met ADF runtime.

### <a name="middle-tier"></a>Middelste laag

De middelste laag bevat de logische server en de batch server. In dit geval worden beide servers op dezelfde virtuele machine geïnstalleerd. Voor productie scenario's is het echter raadzaam om Logic Server en batch server op afzonderlijke servers te implementeren. Meerdere-servers worden in de middelste laag geïmplementeerd in twee beschikbaarheids zones voor een hogere Beschik baarheid. Er moet een Azure-load balancer worden gemaakt en deze servers moeten worden geplaatst in de back-end-groep om ervoor te zorgen dat beide servers actief zijn en aanvragen verwerken.

De servers in de middelste laag ontvangen aanvragen van de servers in de presentatielaag en de open bare load balancer. De regels voor netwerk beveiligings groepen moeten worden ingesteld om verkeer te weigeren van elk ander adres dan het subnet met de presentatielaag en de load balancer. Een NSG-regel kan ook worden ingesteld voor het toestaan van verkeer op poort 22 van de bastion-host voor beheer doeleinden. Mogelijk kunt u de open bare load balancer gebruiken om aanvragen te verdelen tussen de virtuele machines in de middelste laag.

De volgende twee onderdelen bevinden zich in de middelste laag:

- **Logic-Server** : de bedrijfs logica of zakelijke functies bevatten.
- **Batch server** : wordt gebruikt voor batch verwerking

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>People

De People Soft Application Suite van Oracle bevat software voor human resources en financieel beheer. Het toepassings pakket is multi-tier en toepassingen zijn onder andere HRMS (Human Resource Management Systems), CRM (Customer Relationship Management), Financials en Supply Chain Management (FSCM) en Enter prise Performance Management (EPM).

Het is raadzaam dat elke laag van het software pakket wordt geïmplementeerd in een eigen subnet. Een Oracle-data base of Microsoft SQL Server is vereist als back-end-Data Base voor de toepassing. In deze sectie vindt u informatie over het implementeren van People Soft met een Oracle data base-back-end.

Het volgende is een canonieke architectuur voor het implementeren van het People Soft-toepassings pakket in een architectuur met meerdere Clouds (afbeelding 5).

![De architectuur van People Soft voor meerdere clouds](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Afbeelding 5: de architectuur van People-Cloud*

In deze voorbeeld architectuur is het virtuele netwerk in azure verbonden met het virtuele Cloud netwerk in OCI met behulp van de cross-Cloud Interconnect. De toepassingslaag is ingesteld in azure, terwijl de data base is ingesteld in OCI. U kunt het beste elk onderdeel implementeren in een eigen subnet met netwerk beveiligings groepen om alleen verkeer toe te staan van specifieke subnetten op specifieke poorten.

De architectuur kan ook worden aangepast voor implementatie in azure met Maxi maal beschik bare Oracle-data bases die zijn geconfigureerd met Oracle Data Guard in twee beschikbaarheids zones in een regio. Het volgende diagram (afbeelding 6) is een voor beeld van dit architectuur patroon:

![People Soft Azure-enige architectuur](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Afbeelding 6: People Soft Azure-enige architectuur*

In de volgende secties worden de verschillende onderdelen op een hoog niveau beschreven.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Toepassingslaag

De toepassingslaag bevat exemplaren van de People Soft-toepassings servers, People-webservers, elastisch zoeken en People Soft Process Scheduler. Een Azure-load balancer is ingesteld om aanvragen te accepteren van gebruikers die worden doorgestuurd naar de juiste server in de toepassingslaag.

Voor maximale Beschik baarheid kunt u een redundante instantie van elke server in de toepassingslaag instellen in verschillende beschikbaarheids zones. De Azure-load balancer kan worden ingesteld met meerdere back-endservers om elke aanvraag naar de juiste server te sturen.

### <a name="peopletools-client"></a>People-upclient

De People-upclient wordt gebruikt om beheer activiteiten uit te voeren, zoals ontwikkeling, migratie en upgrade. Omdat de People-upclient niet vereist is om een hoge Beschik baarheid van uw toepassing te bereiken, zijn redundante servers van de People-upclient niet nodig.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Volgende stappen

Gebruik [terraform-scripts](https://github.com/microsoft/azure-oracle) voor het instellen van Oracle-apps in Azure en het tot stand brengen van connectiviteit tussen verschillende Clouds met OCI.

Zie de [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) Documentation (Engelstalig) voor meer informatie en witboeken over OCI.
