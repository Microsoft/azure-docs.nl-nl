---
title: Problemen met Toegewezen HSM oplossen - Azure Toegewezen HSM | Microsoft Docs
description: Overzicht van Azure Toegewezen HSM, welke mogelijkheden biedt voor sleutelopslag binnen Azure die voldoet aan de FIPS 140-2 Niveau 3-certificering
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0791f2e8d5119c2087286a24cf83b4259ee9e7af
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611647"
---
# <a name="troubleshooting-the-azure-dedicated-hsm-service"></a>Problemen met de Azure Toegewezen HSM-service oplossen

De Azure Toegewezen HSM-service heeft twee afzonderlijke facetten. Ten eerste, de registratie en implementatie van de HSM-apparaten in Azure met hun onderliggende netwerkonderdelen. Ten tweede, de configuratie van de HSM-apparaten als voorbereiding op gebruik/integratie met een bepaalde workload of toepassing. Hoewel de [Thales Luna-HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -apparaten in azure hetzelfde zijn als u rechtstreeks aanschaft vanuit Thales, maakt het feit dat ze een resource zijn in azure enkele unieke overwegingen. Deze overwegingen en eventuele inzichten in probleemoplossing of aanbevolen procedures, zijn hier gedocumenteerd, voor hoge zichtbaarheid en toegang tot kritieke informatie. Zodra de service wordt gebruikt, is definitieve informatie beschikbaar via ondersteuningsaanvragen bij Microsoft of rechtstreeks bij Thales. 

> [!NOTE]
> Let op: voordat u een onlangs geïmplementeerd HSM-apparaat configureert, moet dit apparaat worden bijgewerkt met alle eventuele relevante patches. Een specifieke patch die is vereist, is [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) in de Thales-ondersteuningsportal. Hiermee wordt een probleem opgelost waarbij het systeem niet reageert tijdens het opnieuw opstarten.

## <a name="hsm-registration"></a>HSM-registratie

Toegewezen HSM is niet vrij beschikbaar voor gebruik omdat het hardwareresources in de cloud levert en daarom een kostbare resource is, die moet worden beveiligd. We gebruiken daarom een allowlisiting-proces via e-mail met HSMrequest@microsoft.com . 

### <a name="getting-access-to-dedicated-hsm"></a>Toegang krijgen tot Toegewezen HSM

Stel eerst in welke use-cases u dat niet kunt doen door [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) of door [Azure beheerde HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm/overview). Als u vervolgens denkt dat er alleen specifieke HSM aan uw sleutel opslag vereisten voldoet, krijgt u vervolgens een e-mail HSMrequest@microsoft.com om toegang aan te vragen. Bestudeer uw toepassings-en use-cases, de regio's die u wilt Hsm's en het volume van Hsm's dat u zoekt. Als u werkt met een vertegenwoordiger van Microsoft, bijvoorbeeld een Account Executive of Cloud Solution Architect, voegt u deze persoon als e-mailgeadresseerde toe bij elke aanvraag.

## <a name="hsm-provisioning"></a>HSM-inrichting

Het inrichten van een HSM-apparaat in Azure kan worden uitgevoerd via CLI of PowerShell. Wanneer u zich registreert voor de service, wordt er een ARM-voorbeeldsjabloon geleverd en ontvangt u hulp bij de eerste aanpassing. 

### <a name="hsm-deployment-failure-information"></a>Informatie over HSM-implementatiefout

Toegewezen HSM biedt ondersteuning voor CLI en PowerShell voor de implementatie. Informatie over op de portal gebaseerde fouten is daarom beperkt en niet zeer uitgebreid. U vindt uitgebreidere informatie door Resource Explorer te gebruiken. Op de startpagina van de portal vindt u een pictogram hiervoor en meer gedetailleerde informatie over de fout is hier beschikbaar. De informatie is zeer handig wanneer u deze kopieert en in een ondersteuningsaanvraag plakt wanneer deze betrekking heeft op implementatieproblemen.

![Informatie over de fout](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegatie van HSM-subnet
De belangrijkste reden voor implementatiefouten is wanneer men vergeet om de juiste delegatie in te stellen voor het door de klant gedefinieerde subnet waarin de HSM’s later worden ingericht. Instellen van deze delegatie is onderdeel van de implementatievereisten voor VNets en subnetten. In de zelfstudies vindt u hier meer details over.

![Delegatie van subnet](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Racevoorwaarde voor HSM-implementatie

De ARM-standaardsjabloon die wordt geleverd voor de implementatie, beschikt over resources die zijn gerelateerd aan HSM en ExpressRoute-gateway. Netwerkresources zijn een afhankelijkheid voor een geslaagde HSM-implementatie, en timing is cruciaal.  Zo nu en dan komen we implementatiefouten tegen die betrekking hebben op afhankelijkheidsproblemen. Het probleem wordt dan meestal opgelost door de implementatie opnieuw uit te voeren. Als dit niet het geval is, kan het helpen om de resources te verwijderen en vervolgens opnieuw te implementeren. Als u dit hebt geprobeerd en het probleem nog steeds optreedt, dient u een ondersteuningsaanvraag in de Azure-portal in. Selecteer dan dit probleemtype: Problemen bij het configureren van de Azure-installatie.

### <a name="hsm-deployment-using-terraform"></a>HSM-implementatie met behulp van Terraform

Een aantal klanten heeft Terraform gebruikt als automatiseringsomgeving, in plaats van de geleverde ARM-sjablonen, bij het registreren voor deze service. De HSM’s kunnen op deze manier niet worden geïmplementeerd, maar de afhankelijke netwerkresources wel. Terraform heeft een module voor het aanroepen van een minimale ARM-sjabloon die alleen de HSM-implementatie heeft.  In dit geval moet u ervoor zorgen dat netwerkresources, zoals de vereiste ExpressRoute-gateway, volledig zijn geïmplementeerd, voordat u HSM’s gaat implementeren. U kunt de volgende CLI-opdracht gebruiken om te testen of de implementatie volledig is uitgevoerd en geïntegreerd zoals vereist. Vervang de tijdelijke aanduiding met punthaken door uw specifieke naam. Het resultaat dat u moet zien, is: provisioningState is Geslaagd

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Implementatiefout op basis van quotum
Implementaties kunnen mislukken als u de limiet van 2 HSM's per stempel en 4 HSM's per regio overschrijdt. Om deze situatie te voorkomen moet u ervoor zorgen dat u de resources van eerder mislukte implementaties hebt verwijderd, voordat u opnieuw gaat implementeren. Raadpleeg hieronder het item Hoe kan ik HSM’s zien, om resources te controleren. Als u denkt dat u dit quotum, dat voornamelijk ter bescherming is ingesteld, gaat overschrijden, stuurt u een e-mail naar HSMrequest@microsoft.com met de details.

### <a name="deployment-failure-based-on-capacity"></a>Implementatiefout op basis van capaciteit
Wanneer een bepaalde stempel of regio vol raakt - wanneer bijna alle beschikbare HSM’s zijn ingericht - kan dit leiden tot implementatiefouten. Elk stempel heeft 12 Hsm's beschikbaar voor klanten, wat betekent 24 per regio. Er zijn ook twee reserves en één test apparaat in elk stempel. Als u denkt dat u de limiet hebt bereikt, stuurt u een e-mail naar HSMrequest@microsoft.com voor informatie over het vulniveau van specifieke stempels.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Hoe kan ik HSM’s zien wanneer ze zijn ingericht?
Omdat een speciale HSM een allowlisted-service is, wordt deze als een ' verborgen type ' beschouwd in de Azure Portal. Als u de HSM-resources wilt bekijken, moet u het selectievakje Verborgen typen weergeven inschakelen, zoals hieronder wordt weergegeven. De NIC-resource volgt altijd de HSM en is een goede plek om het IP-adres van de HSM te achterhalen vóórdat u SSH gaat gebruiken om verbinding te maken.

![Schermopname waarin het selectievakje Verborgen typen weergeven is gemarkeerd](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Netwerkresources

De implementatie van Toegewezen HSM is afhankelijk van netwerkresources en van enkele hieruit voortvloeiende beperkingen waarmee u rekening moet houden.

### <a name="provisioning-expressroute"></a>ExpressRoute inrichten

Toegewezen HSM maakt gebruik van ExpressRoute Gateway als een ´tunnel´ voor communicatie tussen de privé-IP-adresruimte van klanten en de fysieke HSN in een Azure-datacentrum.  Aangezien er een limiet geldt van één gateway per VNet, moeten klanten die verbinding met hun on-premises resources nodig hebben via ExpressRoute, een ander VNet gebruiken voor deze verbinding.  

### <a name="hsm-private-ip-address"></a>Privé IP-adres voor HSM

Bij de voorbeeldsjablonen die zijn opgegeven voor Toegewezen HSM, wordt ervan uitgegaan dat het IP-adres voor een HSM automatisch afkomstig is uit een opgegeven subnetbereik. U kunt een expliciet IP-adres voor de HSM opgeven via een kenmerk NetworkInterfaces in de ARM-sjabloon. 

![Schermopname van de voorbeeldsjabloon voor Toegewezen HSM.](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM-initialisatie

Met de initialisatie wordt een nieuwe HSM voorbereid voor gebruik of een bestaande HSM voor hergebruik. De initialisatie van de HSM moet zijn voltooid voordat u objecten kunt genereren of opslaan, clients toestemming kunt geven om verbinding te maken, of cryptografische bewerkingen kunt uitvoeren.

### <a name="lost-credentials"></a>Verloren referenties

Verlies van het Shell-beheerderswachtwoord leidt tot verlies van HSM-sleutelmateriaal. Er moet een ondersteuningsaanvraag worden ingediend om de HSM opnieuw in te stellen.
Sla bij het initialiseren van de HSM de referenties veilig op. De Shell- en HSM-referenties moeten worden bewaard in overeenstemming met beleid in uw bedrijf.

### <a name="failed-logins"></a>Mislukte aanmeldingen

Het opgeven van onjuiste referenties voor HSM's kan destructieve gevolgen hebben. Hieronder ziet u standaardgedrag voor HSM-rollen.

| Rol | Drempelwaarde (aantal pogingen) | Resultaat van te veel mislukte aanmeldingspogingen | Herstel |
|--|--|--|--|
| HSM SO | 3 |  HSM wordt met nullen overschreven (alle HSM-object-id’s en alle partities zijn verwijderd)  |  HSM moet opnieuw worden geïnitialiseerd. Inhoud kan worden teruggezet vanuit een of meer back-ups. | 
| Partitie SO | 10 |  Partitie wordt met nullen overschreven. |  Partitie moet opnieuw worden geïnitialiseerd. Inhoud kan worden hersteld vanuit een back-up. |  
| Controleren | 10 | Vergrendeling | Wordt na 10 minuten automatisch ontgrendeld. |  
| Crypto Officer | 10 (kan worden verlaagd) | Als HSM-beleid 15: Opnieuw instellen van de partitiepincode via SO inschakelen is ingesteld op 1 (ingeschakeld), de rollen CO en CU zijn vergrendeld.<br>Als HSM-beleid 15: Opnieuw instellen van de partitiepincode via SO inschakelen is ingesteld op 0 (uitgeschakeld), de rollen CO en CU zijn permanent vergrendeld, en de partitie-inhoud is niet meer toegankelijk. Dit is de standaardinstelling. | De CO-rol moet worden ontgrendeld en de referentie moet opnieuw worden ingesteld met de Partitie SO, met behulp van `role resetpw -name co`.<br>De partitie moet opnieuw worden geïnitialiseerd, en het sleutelmateriaal moet worden hersteld vanaf een back-upapparaat. |  

## <a name="hsm-configuration"></a>HSM-configuratie 

Hieronder volgen situaties waarin configuratiefouten vaak voorkomen of waarin ze een impact hebben die het vermelden waard is:

### <a name="hsm-documentation-and-software"></a>HSM-documentatie en -software
Software en documentatie voor de [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -apparaten zijn niet beschikbaar van micro soft en moeten rechtstreeks worden gedownload van Thales. Registratie met de Thales-klant-id die u tijdens het registratieproces hebt ontvangen, is vereist. De apparaten, zoals geleverd door Microsoft, hebben softwareversie 7.2 en firmwareversie 7.0.3. Begin 2020 heeft Thales de documentatie openbaar gemaakt. U vindt deze documentatie [hier](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).  

### <a name="hsm-networking-configuration"></a>HSM-netwerkconfiguratie

Wees voorzichtig bij het configureren van het netwerk binnen de HSM.  De HSM heeft via de ExpressRoute-gateway vanaf de privé-IP-adresruimte van een klant rechtstreeks verbinding met de HSM.  Dit communicatiekanaal is alleen bestemd voor communicatie met de klant. Microsoft heeft geen toegang tot het kanaal. Als de HSM zodanig is geconfigureerd dat dit netwerkpad wordt beïnvloed, betekent dit dat alle communicatie met de HSM wordt verwijderd.  In deze situatie is de enige optie om een Microsoft-ondersteuningsaanvraag in te dienen via de Azure-portal, met het verzoek om het apparaat opnieuw in te stellen. Met deze procedure voor opnieuw instellen wordt de HSM teruggezet naar de initiële status. De hele configuratie en al het sleutelmateriaal gaan verloren.  De configuratie moet opnieuw worden gemaakt, en wanneer het apparaat wordt toegevoegd aan de HA-groep, wordt het sleutelmateriaal gerepliceerd.  

### <a name="hsm-device-reboot"></a>HSM-apparaat opnieuw opstarten

Voor sommige configuratiewijzigingen moet de HSM worden uitgeschakeld en opnieuw ingeschakeld, of opnieuw worden opgestart. Tijdens Microsoft-tests voor de HSM in Azure is vastgesteld dat het apparaat in sommige gevallen vastloopt tijdens het opnieuw opstarten. De implicatie is dat er een ondersteuningsaanvraag moet worden gemaakt in de Azure-portal, waarin hard opstarten wordt aangevraagd. Het kan tot 48 uur duren voordat dit is voltooid, aangezien dit een handmatig proces in een Azure-datacentrum is.  Zorg ervoor dat u de patch voor opnieuw opstarten hebt geïmplementeerd om deze situatie te voorkomen. Deze patch is rechtstreeks beschikbaar bij Thales. Raadpleeg [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) in de Thales Luna 7 HSM 7,2 down loads voor een aanbevolen patch voor een probleem waarbij het systeem niet meer reageert tijdens het opnieuw opstarten (Opmerking: u moet zijn geregistreerd bij de Thales voor het downloaden van de [klant ondersteuning](https://supportportal.thalesgroup.com/csm) .

### <a name="ntls-certificates-out-of-sync"></a>NTLS-certificaten zijn niet gesynchroniseerd
De verbinding tussen een client en een HSM kan worden verbroken wanneer een certificaat verloopt of is overschreven vanwege configuratie-updates. De configuratie van de uitwisselingsclient voor certificaten moet opnieuw worden toegepast voor elke HSM.
Voorbeeldaanmelding bij NTLS met een ongeldig certificaat:

> NTLS[8508]: info : 0 : Aanvraag voor binnenkomende verbinding... : 192.168.50.2/59415 NTLS[8508]: Foutbericht van SSLAccept is : error:14094418:SSL routines:ssl3_read_bytes:tlsv1 alert unknown ca NTLS[8508]: Fout tijdens SSLAccept ( RC_SSL_ERROR ) NTLS[8508]: info : 0xc0000711 : Kan geen beveiligd kanaal tot stand brengen met client : 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE NTLS[8508]: info : 0 : NTLS-client ‘Onbekende hostnaam’ Verbindingsinstantie is verwijderd : 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>TCP-communicatie is mislukt

Voor communicatie van de Luna-clientinstallatie met de HSM is minstens TCP-poort 1792 vereist. Houd hier rekening mee bij eventuele wijzigingen in de netwerkconfiguratie in de omgeving.

### <a name="failed-ha-group-member-doesnt-recover"></a>Mislukt HA-groepslid wordt niet hersteld

Als een mislukt HA-groepslid niet automatisch wordt hersteld, moet het handmatig worden hersteld vanaf de Luna-client, met behulp van de opdracht hagroup recover.
U moet het aantal nieuwe pogingen voor een HA-groep configureren om automatisch herstellen in te kunnen schakelen. Voor HA-groepen is standaard ingesteld dat er niet wordt geprobeerd een HA-lid te herstellen in de groep, wanneer het mislukt.

### <a name="ha-group-doesnt-sync"></a>HA-groep synchroniseert niet

Wanneer lidpartities niet hetzelfde domein voor klonen hebben, ziet de opdracht voor synchroniseren van HA er als volgt uit: Waarschuwing: Synchronisatie kan mislukken.  De leden in sleuf 0 en sleuf 1 hebben conflicterende instellingen voor het klonen van persoonlijke sleutels.
Er moet een nieuwe partitie met het juiste domein voor klonen worden toegevoegd aan de HA-groep. Daarna moet de onjuist geconfigureerde partitie worden verwijderd.

## <a name="hsm-deprovisioning"></a>HSM-inrichting ongedaan maken 

De inrichting van een HSM kan pas ongedaan worden gemaakt wanneer deze helemaal is voltooid. De HSM wordt dan door Microsoft opnieuw ingesteld en teruggezet in een pool met beschikbare HSM’s. 

### <a name="how-to-delete-an-hsm-resource"></a>Een HSM-resource verwijderen

De Azure-resource voor een HSM kan pas worden verwijderd, als de HSM de status Met nullen overschreven heeft.  Daarom moet al het sleutelmateriaal zijn verwijderd vóórdat u de HSM verwijdert als resource. De snelste manier om met nullen te overschrijven is om het HSM-beheerderswachtwoord 3 keer fout in te voeren. (Opmerking: dit heeft betrekking op de HSM-beheerder en niet op de beheerder op apparaatniveau.) De Luna-shell heeft een opdracht `hsm -factoryreset` waarmee met nullen wordt overschreven, maar deze opdracht kan alleen worden uitgevoerd via de console op de seriële poort, en klanten hebben hier geen toegang toe.

## <a name="next-steps"></a>Volgende stappen

Dit artikel heeft inzicht geboden in de stadia van de levenscyclus van een HSM-implementatie waarin problemen kunnen optreden, of waarvoor probleemoplossing of zorgvuldige overwegingen zijn vereist. Hopelijk helpt dit artikel u onnodige vertragingen en frustraties te voorkomen. Als u relevante aanvullingen of wijzigingen hebt, kunt u een ondersteuningsaanvraag indienen bij Microsoft en ons dit laten weten. 
