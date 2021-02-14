---
title: Voor komen dat subdomein-overname met Azure DNS alias records en de aangepaste domein verificatie van Azure App Service
description: Meer informatie over het voor komen van de veelvoorkomende bedreiging van het hoge urgentie niveau van subdomein overname
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: c3a821156074727d02ab36cf88f3e998756b8cc4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389447"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Dangling DNS-vermeldingen voor komen en de overname van subdomeinen voor komen

In dit artikel wordt de veelvoorkomende bedreiging van de beveiliging van subdomeinen beschreven en de stappen die u kunt nemen om het probleem te verhelpen.


## <a name="what-is-a-subdomain-takeover"></a>Wat is een overname voor subdomeinen?

De overname van subdomeinen is een veelvoorkomende bedreiging van hoge urgentie voor organisaties die regel matig veel resources maken en verwijderen. Een overname van een subdomein kan optreden wanneer u een [DNS-record](../../dns/dns-zones-records.md#dns-records) hebt die naar een niet-ingerichte Azure-resource verwijst. Dergelijke DNS-records worden ook wel ' Dangling DNS-vermeldingen genoemd. CNAME-records zijn met name gevoelig voor deze bedreiging. Met de overname van subdomeinen kunnen kwaadwillende actoren verkeer dat is bedoeld voor het domein van een organisatie, omleiden naar een site die schadelijke activiteiten uitvoert.

Een veelvoorkomend scenario voor een overname van subdomeinen:

1. **ZELF**

    1. U richt een Azure-resource in met een Fully Qualified Domain Name (FQDN) van `app-contogreat-dev-001.azurewebsites.net` .

    1. U wijst een CNAME-record in uw DNS-zone toe met het subdomein `greatapp.contoso.com` dat verkeer naar uw Azure-resource routeert.

1. **INRICHTING**

    1. De Azure-resource wordt niet meer ingericht of verwijderd nadat deze niet meer nodig is. 
    
        Op dit moment `greatapp.contoso.com` *moet* de CNAME-record worden verwijderd uit de DNS-zone. Als de CNAME-record niet wordt verwijderd, wordt deze geadverteerd als een actief domein, maar stuurt het geen verkeer naar een actieve Azure-resource. Dit is de definitie van een DNS-record met ' Dangling '.

    1. Het subdomein Dangling `greatapp.contoso.com` is nu kwetsbaar en kan worden overgenomen door te worden toegewezen aan de resource van een ander Azure-abonnement.

1. **OVERNEMEN**

    1. Met behulp van algemeen beschik bare methoden en hulpprogram ma's detecteert een bedreigings actor het subdomein Dangling.  

    1. De bedreigings actor richt zich op een Azure-resource met dezelfde FQDN van de resource die u eerder hebt beheerd. In dit voor beeld `app-contogreat-dev-001.azurewebsites.net` .

    1. Verkeer dat wordt verzonden naar het subdomein `greatapp.contoso.com` , wordt nu doorgestuurd naar de bron van de schadelijke actor waar ze de inhoud beheren.



![Subdomein-overname van een website die niet wordt ingericht](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>De Risico's van overname van subdomeinen

Wanneer een DNS-record verwijst naar een resource die niet beschikbaar is, moet de record zelf uit de DNS-zone worden verwijderd. Als deze niet is verwijderd, is dit een Dangling DNS-record en maakt u de mogelijkheid voor de overname van subdomeinen.

Dangling DNS-vermeldingen maken het mogelijk om de bijbehorende DNS-naam in te stellen voor het hosten van een schadelijke website of service. Schadelijke pagina's en services op het subdomein van een organisatie kunnen het volgende veroorzaken:

- **Verlies van de controle over de inhoud van het subdomein** -negatief druk over het onvermogen van uw organisatie om de inhoud ervan te beveiligen, evenals het merk beschadiging en het verlies van vertrouwen.

- Het **verzamelen van cookies van onvermoede bezoekers** : het is gebruikelijk voor web apps om sessie cookies beschikbaar te maken voor subdomeinen (*. contoso.com), waardoor elk subdomein er toegang toe heeft. Threat actors kunnen subdomein overname gebruiken om een authentiek ogende pagina samen te stellen, het niet vermoeden van gebruikers om deze te bezoeken en hun cookies te verzamelen (zelfs beveiligde cookies). Een veelvoorkomende, niet-algemene manier is dat met behulp van SSL-certificaten uw site en de cookies van uw gebruikers worden beschermd vanuit een overname. Een bedreigings actor kan echter gebruikmaken van het gehackte subdomein om toe te passen en een geldig SSL-certificaat te ontvangen. Geldige SSL-certificaten geven hen toegang tot beveiligde cookies en kunnen de waargenomen rechtmatigheid van de schadelijke site verder verg Roten.

- **Phishing campagnes** : authentiek-Zoek subdomeinen kunnen worden gebruikt in phishing-campagnes. Dit geldt voor schadelijke sites en voor MX-records waarmee de bedreigings actor e-mails kan ontvangen die zijn geadresseerd aan een rechtmatig subdomein van een bekend veilig merk.

- **Verdere Risico's** : schadelijke sites kunnen worden gebruikt om te escaleren naar andere klassieke aanvallen, zoals XSS, csrf, CORS bypass en meer.



## <a name="identify-dangling-dns-entries"></a>Dangling DNS-vermeldingen identificeren

Gebruik de GitHub van micro soft gehoste Power shell-hulpprogram ma's [' Get-DanglingDnsRecords '](https://aka.ms/DanglingDNSDomains)om DNS-vermeldingen binnen uw organisatie te identificeren die mogelijk Dangling zijn.

Met dit hulp programma kunnen Azure-klanten alle domeinen weer geven met een CNAME dat is gekoppeld aan een bestaande Azure-resource die is gemaakt op hun abonnementen of tenants.

Als uw CNAME-gegevens zich in andere DNS-services bevinden en naar Azure-resources verwijzen, geeft u de CNAME-namen op in een invoer bestand voor het hulp programma.

Het hulp programma ondersteunt de Azure-resources die in de volgende tabel worden weer gegeven. Het hulp programma extraheert of gaat als invoer, alle CNAME-gegevens van de Tenant.


| Service                   | Type                                        | FQDNproperty                               | Voorbeeld                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | micro soft. Network/frontdoors                | Properties. cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | micro soft. Storage/Storage accounts           | Properties. primaire. blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | micro soft. CDN/profielen/eind punten            | Properties. hostName                        | `abc.azureedge.net`             |
| Openbare IP-adressen       | micro soft. Network/publicipaddresses         | Eigenschappen. dnsSettings. FQDN                | `abc.EastUs.cloudapp.azure.com` |
| Azure Traffic Manager     | micro soft. Network/trafficmanagerprofiles    | Properties. dnsConfig. FQDN                  | `abc.trafficmanager.net`        |
| Azure Container Instance  | micro soft. containerinstance/containergroups | Eigenschappen. ipAddress. FQDN                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | micro soft. apimanagement/service             | Properties. hostnameConfigurations. hostName | `abc.azure-api.net`             |
| Azure App Service         | micro soft. web/sites                         | Eigenschappen. defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service-sleuven | micro soft. web/sites/sleuven                   | Eigenschappen. defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Vereisten

Voer de query uit als een gebruiker met:

- ten minste toegang op lezerniveau tot de Azure-abonnementen
- Lees toegang tot Azure-resource grafiek

Als u een globale beheerder van de Tenant van uw organisatie bent, kunt u uw account verhogen om toegang te hebben tot al het abonnement van uw organisatie met behulp van de richt lijnen in [toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren](../../role-based-access-control/elevate-access-global-admin.md).


> [!TIP]
> Azure resource Graph heeft beperkingen en paginerings limieten waarmee u rekening moet houden als u een grote Azure-omgeving hebt. 
> 
> [Meer informatie over het werken met grote Azure-resource gegevens sets](../../governance/resource-graph/concepts/work-with-data.md).
> 
> Het hulp programma gebruikt abonnements batches om deze beperkingen te voor komen.

### <a name="run-the-script"></a>Het script uitvoeren

Meer informatie over het Power shell-script, **Get-DanglingDnsRecords.ps1** en downloaden van github: https://aka.ms/Get-DanglingDnsRecords .

## <a name="remediate-dangling-dns-entries"></a>Dangling DNS-vermeldingen herstellen 

Controleer uw DNS-zones en Identificeer CNAME-records die zijn Dangling of worden overgenomen. Als er subdomeinen worden gevonden die moeten worden Dangling of worden overgenomen, verwijdert u de kwets bare subdomeinen en vermindert u de Risico's met de volgende stappen:

1. Verwijder in uw DNS-zone alle CNAME-records die verwijzen naar FQDN-namen van resources die niet meer zijn ingericht.

1. Als u wilt dat verkeer wordt gerouteerd naar resources in uw besturings element, moet u aanvullende resources inrichten met de FQDN-namen die zijn opgegeven in de CNAME-records van de Dangling-subdomeinen.

1. Controleer de toepassings code op verwijzingen naar specifieke subdomeinen en werk eventuele onjuiste of verouderde subdomein verwijzingen bij.

1. Onderzoek of er inbreuk is opgetreden en Onderneem actie te ondernemen volgens de procedures van de reactie op incidenten van uw organisatie. Tips en aanbevolen procedures voor het onderzoeken van dit probleem vindt u hieronder.

    Als uw toepassings logica zodanig is dat geheimen zoals OAuth-referenties zijn verzonden naar het Dangling-subdomein, of als er privacy gevoelige informatie is verzonden naar de Dangling-subdomeinen, zijn deze gegevens mogelijk blootgesteld aan derden.

1. Begrijp waarom de CNAME-record niet is verwijderd uit de DNS-zone toen de inrichting van de resource werd opgeheven en onderneem stappen om ervoor te zorgen dat DNS-records correct worden bijgewerkt wanneer Azure-resources in de toekomst worden verwijderd.


## <a name="prevent-dangling-dns-entries"></a>Dangling DNS-vermeldingen voor komen

Zorg ervoor dat uw organisatie de processen heeft geïmplementeerd om te voor komen dat DNS-vermeldingen voor Dangling en de resulterende subdomeinen van het beveiligings programma een belang rijk onderdeel van zijn.

Sommige functies van Azure Services bieden ondersteuning bij het maken van preventieve maat regelen en worden hieronder beschreven. Andere methoden om dit probleem te voor komen, moeten worden vastgesteld door de aanbevolen procedures van uw organisatie of door de standaard-werk procedures.

### <a name="enable-azure-defender-for-app-service"></a>Azure Defender voor App Service inschakelen

Het geïntegreerde CWPP (Cloud workload Protection platform) van Azure Security Center, Azure Defender, biedt diverse plannen voor het beveiligen van uw Azure-, hybride en multi-cloud resources en-workloads.

Het **Azure Defender for app service** -abonnement bevat Dangling DNS-detectie. Als dit abonnement is ingeschakeld, ontvangt u beveiligings waarschuwingen als u een App Service website buiten gebruik stelt, maar het aangepaste domein niet uit uw DNS-REGI ster verwijdert.

De Dangling DNS-beveiliging van Azure Defender is beschikbaar, ongeacht of uw domeinen worden beheerd met Azure DNS of een externe domein registratie en van toepassing zijn op App Service in Windows en Linux.

Meer informatie over deze en andere voor delen van dit Azure Defender-plan vindt [u in Inleiding tot Azure Defender voor app service](../../security-center/defender-for-app-service-introduction.md).

### <a name="use-azure-dns-alias-records"></a>Azure DNS alias records gebruiken

De [alias records](../../dns/dns-alias.md#scenarios) van Azure DNS kunnen Dangling-verwijzingen verhinderen door de levens cyclus van een DNS-record te koppelen aan een Azure-resource. Denk bijvoorbeeld aan een DNS-record die is gekwalificeerd als alias record om te verwijzen naar een openbaar IP-adres of een Traffic Manager profiel. Als u deze onderliggende bronnen verwijdert, wordt de DNS-alias record een lege recordset. Er wordt niet langer verwezen naar de verwijderde resource. Het is belang rijk te weten dat er beperkingen zijn voor wat u met alias records kunt beveiligen. De lijst is nu beperkt tot:

- Azure Front Door
- Traffic Manager-profielen
- Azure Content Delivery Network (CDN)-eind punten
- Openbare IP-adressen

Ondanks de beperkte service aanbiedingen van vandaag, raden we u aan om, indien mogelijk, alias records te gebruiken om te beschermen tegen de overname van subdomeinen.

Meer [informatie over de mogelijkheden van de alias records van Azure DNS](../../dns/dns-alias.md#capabilities).



### <a name="use-azure-app-services-custom-domain-verification"></a>Aangepaste domein verificatie van Azure App Service gebruiken

Maak een asuid bij het maken van DNS-vermeldingen voor Azure App Service. subdomein TXT-record met de verificatie-ID van het domein. Wanneer een dergelijke TXT-record bestaat, kan geen ander Azure-abonnement het aangepaste domein valideren. 

Met deze records wordt niet voor komen dat iemand de Azure App Service maakt met dezelfde naam in de CNAME-vermelding. Zonder de mogelijkheid om eigenaar te worden van de domein naam, kunnen Threat actors geen verkeer ontvangen of de inhoud beheren.

Meer [informatie over het toewijzen van een bestaande aangepaste DNS-naam aan Azure app service](../../app-service/app-service-web-tutorial-custom-domain.md).



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Bouw en automatiseer processen om de dreiging te beperken

Het is vaak tot ontwikkel aars en operationele teams om opschoon processen uit te voeren om Dangling DNS-bedreigingen te voor komen. Met de volgende procedures kunt u ervoor zorgen dat uw organisatie niet kan lijden aan deze bedreiging. 

- **Procedures voor preventie maken:**

    - Train uw toepassings ontwikkelaars om adressen opnieuw te routeren wanneer ze resources verwijderen.

    - Plaats ' DNS-vermelding verwijderen ' in de lijst met vereiste controles bij het buiten gebruik stellen van een service.

    - Plaats [Verwijder vergrendelingen](../../azure-resource-manager/management/lock-resources.md) op alle resources met een aangepaste DNS-vermelding. Een verwijderings vergrendeling fungeert als een indicator die de toewijzing moet verwijderen voordat de inrichting van de resource ongedaan wordt gemaakt. Maat staven zoals deze kunnen alleen worden gebruikt in combi natie met interne onderwijs Programma's.

- **Procedures voor detectie maken:**

    - Controleer uw DNS-records regel matig om ervoor te zorgen dat uw subdomeinen allemaal zijn toegewezen aan Azure-resources die:

        - Existing: Query's uitvoeren op uw DNS-zones voor resources die verwijzen naar Azure-subdomeinen, zoals *. azurewebsites.net of *. cloudapp.azure.com (Zie de [naslag lijst van Azure-domeinen](azure-domains.md)).
        - U bent eigenaar van de resources die zijn gericht op uw DNS-subdomeinen.

    - Onderhoud van een service catalogus van uw Azure Fully Qualified Domain Name (FQDN)-eind punten en de eigen aren van de toepassing. Als u uw service catalogus wilt maken, voert u het volgende Azure resource Graph-query script uit. Dit script projecteert de FQDN-eindpunt gegevens van de resources waartoe u toegang hebt en voert deze uit in een CSV-bestand. Als u toegang hebt tot alle abonnementen voor uw Tenant, beschouwt het script al deze abonnementen, zoals wordt weer gegeven in het volgende voorbeeld script. Als u de resultaten wilt beperken tot een specifieke set abonnementen, bewerkt u het script zoals wordt weer gegeven.


- **Procedures voor herstel maken:**
    - Wanneer Dangling DNS-vermeldingen worden gevonden, moet uw team onderzoeken of er inbreuk is opgetreden.
    - Onderzoek waarom het adres niet is omgeleid toen de resource uit bedrijf werd genomen.
    - Verwijder de DNS-record als deze niet meer in gebruik is of wijs deze naar de juiste Azure-resource (FQDN) van uw organisatie.
 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende pagina's voor meer informatie over gerelateerde services en Azure-functies die u kunt gebruiken om te beschermen tegen de overname van subdomeinen.

- [Azure Defender voor app service inschakelen](../../security-center/defender-for-app-service-introduction.md) : er worden waarschuwingen weer gegeven wanneer Dangling DNS-vermeldingen worden gedetecteerd

- [Dangling DNS-records met Azure DNS voor komen](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Een domein verificatie-ID gebruiken bij het toevoegen van aangepaste domeinen in Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Quickstart: Uw eerste Resource Graph-query uitvoeren met Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)
