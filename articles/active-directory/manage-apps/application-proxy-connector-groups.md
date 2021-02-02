---
title: Apps op afzonderlijke netwerken publiceren via connector groepen-Azure AD
description: In dit artikel wordt beschreven hoe u groepen Connect oren maakt en beheert in azure AD-toepassingsproxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca42137192fefbc669fca775b0deff2f5226c5c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253829"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Toepassingen publiceren op afzonderlijke netwerken en locaties met connector groepen

Klanten gebruiken de toepassings proxy van Azure AD voor meer en meer scenario's en toepassingen. Daarom hebben we app proxy nog flexibeler gemaakt door meer topologieën in te scha kelen. U kunt Application proxy connector-groepen maken zodat u specifieke connectors kunt toewijzen voor specifieke toepassingen. Deze mogelijkheid geeft u meer controle en manieren om uw implementatie van de toepassings proxy te optimaliseren.

Elke toepassingsproxy-connector wordt toegewezen aan een connectorgroep. Alle connectors die deel uitmaken van dezelfde connector groep fungeren als een afzonderlijke eenheid voor hoge Beschik baarheid en taak verdeling. Alle connectors horen bij een connector groep. Als u geen groepen maakt, zijn alle connectors in een standaard groep. Uw beheerder kan nieuwe groepen maken en Connect oren aan hen toewijzen in de Azure Portal.

Alle toepassingen worden toegewezen aan een connector groep. Als u geen groepen maakt, worden al uw toepassingen toegewezen aan een standaard groep. Maar als u uw connectors in groepen indeelt, kunt u elke toepassing zo instellen dat deze werkt met een specifieke connector groep. In dit geval dienen alleen de connectors in die groep de toepassing op aanvraag. Deze functie is handig als uw toepassingen worden gehost op verschillende locaties. U kunt connector groepen maken op basis van locatie, zodat toepassingen altijd worden bediend door connectors die zich fysiek dicht bij hen bevinden.

> [!TIP]
> Als u een grote implementatie van een toepassings proxy hebt, wijst u geen toepassingen toe aan de standaard connector groep. Op die manier ontvangen nieuwe connectors geen live verkeer totdat u ze toewijst aan een actieve connector groep. Met deze configuratie kunt u ook connectors in een niet-actieve modus plaatsen door ze terug te verplaatsen naar de standaard groep, zodat u onderhoud kunt uitvoeren zonder dat dit van invloed is op uw gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u uw connectors wilt groeperen, moet u ervoor zorgen dat u [meerdere connectors hebt geïnstalleerd](application-proxy-add-on-premises-application.md). Wanneer u een nieuwe connector installeert, wordt automatisch lid van de **standaard** connector groep.

## <a name="create-connector-groups"></a>Connector groepen maken

Gebruik deze stappen om zoveel connector groepen te maken als u wilt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory**  >    >  **toepassings proxy** voor bedrijfs toepassingen.
1. Selecteer **nieuwe connector groep**. De Blade nieuwe connector groep wordt weer gegeven.

   ![Hiermee wordt het scherm weer gegeven om een nieuwe connector groep te selecteren](./media/application-proxy-connector-groups/new-group.png)

1. Geef een naam op voor uw nieuwe connector groep en gebruik vervolgens de vervolg keuzelijst om te selecteren welke connectors in deze groep horen.
1. Selecteer **Opslaan**.

## <a name="assign-applications-to-your-connector-groups"></a>Toepassingen toewijzen aan uw connector groepen

Volg deze stappen voor elke toepassing die u hebt gepubliceerd met toepassings proxy. U kunt een toepassing toewijzen aan een connector groep wanneer u deze voor het eerst publiceert of u kunt deze stappen gebruiken om de toewijzing op elk gewenst moment te wijzigen.

1. In het beheer dashboard voor uw Directory selecteert u **bedrijfs toepassingen**  >  **alle toepassingen** > de toepassing die u wilt toewijzen aan een connector groep > **toepassings proxy**.
1. Gebruik de vervolg keuzelijst **groep connector** om de groep te selecteren die u door de toepassing wilt laten gebruiken.
1. Selecteer **Opslaan** om de wijziging toe te passen.

## <a name="use-cases-for-connector-groups"></a>Cases voor connector groepen gebruiken

Connector groepen zijn handig voor verschillende scenario's, waaronder:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Sites met meerdere onderling verbonden data centers

Veel organisaties hebben een aantal onderling verbonden data centers. In dit geval wilt u zoveel mogelijk verkeer binnen het Data Center houden omdat koppelingen tussen data centers duur en langzaam zijn. U kunt connectors in elk Data Center implementeren zodat alleen de toepassingen worden gebruikt die zich in het Data Center bevinden. Deze aanpak minimaliseert koppelingen tussen data centers en biedt uw gebruikers een volledig transparante ervaring.

### <a name="applications-installed-on-isolated-networks"></a>Toepassingen die zijn geïnstalleerd in geïsoleerde netwerken

Toepassingen kunnen worden gehost in netwerken die geen deel uitmaken van het hoofd bedrijfs netwerk. U kunt connector groepen gebruiken voor het installeren van specifieke connectors in geïsoleerde netwerken om ook toepassingen op het netwerk te isoleren. Dit gebeurt meestal wanneer een externe leverancier een specifieke toepassing voor uw organisatie bijhoudt.

Connector groepen bieden u de mogelijkheid om specifieke connectors te installeren voor die netwerken die alleen specifieke toepassingen publiceren, waardoor het eenvoudiger en veiliger wordt om het uitbesteden van toepassings beheer aan externe leveranciers.

### <a name="applications-installed-on-iaas"></a>Toepassingen die zijn geïnstalleerd op IaaS

Voor toepassingen die zijn geïnstalleerd op IaaS voor Cloud toegang bieden connector groepen een gemeen schappelijke service om de toegang tot alle apps te beveiligen. Connector groepen maken geen extra afhankelijkheid op uw bedrijfs netwerk of fragmenteren de ervaring van de app. Connectors kunnen worden geïnstalleerd op elk Cloud-Data Center en alleen voor toepassingen die zich in dat netwerk bevinden. U kunt verschillende connectors installeren om hoge Beschik baarheid te garanderen.

Neem een voor beeld van een organisatie met verschillende virtuele machines die zijn verbonden met hun eigen IaaS-gehoste virtuele netwerk. Om werk nemers in staat te stellen deze toepassingen te gebruiken, zijn deze particuliere netwerken verbonden met het bedrijfs netwerk met behulp van site-naar-site-VPN. Dit biedt een goede ervaring voor werk nemers die on-premises zich bevinden. Het is echter mogelijk niet ideaal voor externe werk nemers, omdat hiervoor extra on-premises infra structuur is vereist om toegang te kunnen krijgen, zoals u kunt zien in het onderstaande diagram:

![Diagram dat het Azure AD IaaS-netwerk illustreert](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Met Azure AD-toepassingsproxy-connector groepen kunt u een gemeen schappelijke service inschakelen voor het beveiligen van de toegang tot alle toepassingen zonder extra afhankelijkheid te maken op uw bedrijfs netwerk:

![Azure AD IaaS meerdere Cloud leveranciers](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Meerdere forests: verschillende connector groepen voor elk forest

De meeste klanten die toepassings proxy hebben geïmplementeerd, gebruiken de SSO-mogelijkheden (single sign-on) door Kerberos-beperkte delegering (KCD) uit te voeren. Om dit te bereiken, moeten de computers van de connector lid zijn van een domein dat de gebruikers naar de toepassing kan delegeren. KCD ondersteunt functionaliteit voor meerdere forests. Voor bedrijven die verschillende omgevingen met meerdere forests hebben zonder vertrouwens relatie, kan één connector echter niet worden gebruikt voor alle forests. 

In dit geval kunnen specifieke connectors per forest worden geïmplementeerd en zo worden ingesteld dat toepassingen die zijn gepubliceerd, alleen de gebruikers van het betreffende forest gebruiken. Elke connector groep vertegenwoordigt een ander forest. Hoewel de Tenant en de meeste ervaring voor alle forests zijn geïntegreerd, kunnen gebruikers worden toegewezen aan hun Forest-toepassingen met behulp van Azure AD-groepen.

### <a name="disaster-recovery-sites"></a>Nood herstel sites

Er zijn twee verschillende benaderingen die u met een site voor nood herstel (DR) kunt uitvoeren, afhankelijk van hoe uw sites worden geïmplementeerd:

* Als uw DR-site is ingebouwd in de modus actief-actief, waarbij deze precies overeenkomt met de hoofd site en dezelfde netwerk-en AD-instellingen heeft, kunt u de connectors maken op de DR-site in dezelfde connector groep als de hoofd site. Hierdoor kan Azure AD failovers voor u detecteren.
* Als uw DR-site gescheiden is van de hoofd site, kunt u een andere connector groep maken op de DR-site, en 1 hebben back-uptoepassingen of 2) hand matig de bestaande toepassing naar de groep DR-connector omleiden naar behoefte.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Meerdere bedrijven van één Tenant gebruiken

Er zijn veel verschillende manieren voor het implementeren van een model waarin één service provider Azure AD gerelateerde services voor meerdere bedrijven implementeert en onderhoudt. Connector groepen helpen de beheerder de connectors en toepassingen te scheiden in verschillende groepen. Een van de manieren, die geschikt is voor kleine bedrijven, is om één Azure AD-Tenant te hebben terwijl de verschillende bedrijven hun eigen domein naam en netwerken hebben. Dit geldt ook voor M&van een scenario en situaties waarin een enkele IT-afdeling verschillende bedrijven voor wettelijke of zakelijke redenen heeft.

## <a name="sample-configurations"></a>Voorbeeld configuraties

Enkele voor beelden die u kunt implementeren, zijn de volgende connector groepen.

### <a name="default-configuration--no-use-for-connector-groups"></a>Standaard configuratie: geen gebruik voor connector groepen

Als u geen connector groepen gebruikt, ziet uw configuratie er als volgt uit:

![Voor beelden van Azure AD geen connector groepen](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Deze configuratie is voldoende voor kleine implementaties en tests. Het werkt ook goed als uw organisatie een platte netwerk topologie heeft.

### <a name="default-configuration-and-an-isolated-network"></a>Standaard configuratie en een geïsoleerd netwerk

Deze configuratie is een evolutie van de standaard versie, waarbij er een specifieke app is die wordt uitgevoerd in een geïsoleerd netwerk zoals IaaS virtueel netwerk:

![Voor beelden van Azure AD geen connector groepen en een geïsoleerd netwerk](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Aanbevolen configuratie: verschillende specifieke groepen en een standaard groep voor inactief

De aanbevolen configuratie voor grote en complexe organisaties is om de standaard connector groep te hebben als een groep die geen toepassingen gebruikt en voor niet-actieve of zojuist geïnstalleerde connectors. Alle toepassingen worden geleverd met behulp van aangepaste connector groepen. Hiermee wordt de complexiteit van de hierboven beschreven scenario's mogelijk.

In het onderstaande voor beeld heeft het bedrijf twee data centers, A en B, met twee Connect oren die elke site gebruiken. Elke site heeft verschillende toepassingen die erop worden uitgevoerd.

![Voor beeld van een bedrijf met 2 data centers en twee connectors](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)
* [Eenmalige aanmelding inschakelen](what-is-single-sign-on.md)
