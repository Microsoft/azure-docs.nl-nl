---
title: Algemene implementatie scenario's voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over een aantal algemene scenario's en use-cases voor Azure Active Directory Domain Services om waarde te bieden en te voldoen aan de behoeften van uw bedrijf.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619025"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Veelvoorkomend gebruik: cases en scenario's voor Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeindeelname, groepsbeleid, Lightweight Directory Access Protocol (LDAP) en Kerberos/NTLM-verificatie. Azure AD DS is geïntegreerd met uw bestaande Azure AD-Tenant, waardoor gebruikers zich kunnen aanmelden met hun bestaande referenties. U gebruikt deze domein Services zonder dat u domein controllers in de Cloud hoeft te implementeren, beheren en te patchen. Dit biedt een soepelere lift-en-verschuiving van on-premises resources naar Azure.

In dit artikel vindt u een overzicht van een aantal algemene bedrijfs scenario's waarbij Azure AD DS een waarde biedt en aan die behoeften voldoet.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Algemene manieren om identiteitsoplossingen in de cloud te bieden

Wanneer u bestaande workloads naar de cloud migreert, wordt voor toepassingen met een mappenstructuur mogelijk LDAP gebruikt voor lees- of schrijftoegang tot een on-premises AD DS-map. Toepassingen die op Windows Server worden uitgevoerd, worden doorgaans geïmplementeerd op met een domein samengevoegde virtuele machines (VM's) zodat ze veilig kunnen worden beheerd met behulp van groepsbeleid. Voor het verifiëren van eindgebruikers kunnen de toepassingen ook afhankelijk zijn van met Windows geïntegreerde verificatie, zoals Kerberos of NTLM-verificatie.

IT-beheerders gebruiken vaak een van de volgende oplossingen om een identiteitsservice te bieden voor toepassingen die worden uitgevoerd in Azure:

* Configureer een site-naar-site VPN-verbinding tussen workloads die worden uitgevoerd in Azure en een on-premises AD DS-omgeving.
    * De on-premises domeincontrollers bieden vervolgens verificatie via de VPN-verbinding.
* Maak replica's van domeincontrollers met behulp van virtuele Azure-machines (VM's) om het AD DS-domein/forest uit te breiden van on-premises.
    * De domeincontrollers die worden uitgevoerd op virtuele Azure-machines bieden verificatie en repliceren mapgegevens tussen de on-premises AD DS-omgeving.
* Implementeer een zelfstandige AD DS-omgeving in Azure met behulp van domeincontrollers die worden uitgevoerd op virtuele Azure-machines.
    * De domeincontrollers die worden uitgevoerd op virtuele Azure-machines bieden verificatie, maar er worden geen mapgegevens gerepliceerd vanuit een on-premises AD DS-omgeving.

Met deze methoden maken VPN-verbindingen met de on-premises map toepassingen kwetsbaar voor tijdelijke netwerkstoringen of uitval. Als u domeincontrollers implementeert met behulp van virtuele machines in Azure, moet het IT-team de virtuele machines beheren en deze vervolgens beveiligen, patchen en bewaken, er een back-up van maken en problemen oplossen.

Azure AD DS biedt alternatieven voor het moeten maken van VPN-verbindingen met een on-premises AD DS-omgeving of het uitvoeren en beheren van virtuele machines in Azure om identiteitsservices te bieden. Als beheerde service zorgt Azure AD DS ervoor dat het maken van een geïntegreerde identiteitsoplossing voor zowel hybride omgevingen als omgevingen die alleen in de cloud bestaan minder complex is.

> [!div class="nextstepaction"]
> [Azure AD DS vergelijken met Azure AD en zelfbeheerde AD DS op virtuele Azure-machines of on-premises][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS voor hybride organisaties

Veel organisaties voeren een hybride infrastructuur uit met toepassingsworkloads in zowel de cloud als on-premises. Voor verouderde toepassingen die naar Azure zijn gemigreerd als onderdeel van een lift-and-shift-strategie worden mogelijk traditionele LDAP-verbindingen gebruikt om identiteitsgegevens te bieden. Ter ondersteuning van deze hybride infrastructuur kunnen identiteitsgegevens van een on-premises AD DS-omgeving worden gesynchroniseerd naar een Azure AD-tenant. Azure AD DS biedt deze verouderde toepassingen vervolgens in Azure met een identiteitsbron, zonder toepassingsconnectiviteit weer naar on-premises mapservices te hoeven configureren en te beheren.

Laten we eens kijken naar een voorbeeld voor Litware Corporation, een hybride organisatie die zowel on-premises resources als Azure-resources uitvoert:

![Azure Active Directory Domain Services voor een hybride organisatie met on-premises synchronisatie](./media/overview/synced-tenant.png)

* Toepassingen en serverworkloads waarvoor domeinservices moeten worden geïmplementeerd in een virtueel netwerk in Azure.
    * Dit kunnen verouderde toepassingen zijn die naar Azure zijn gemigreerd als onderdeel van een lift-and-shift-strategie.
* Voor het synchroniseren van identiteitsgegevens van hun on-premises map naar hun Azure AD-tenant implementeert Litware Corporation [Azure AD Connect][azure-ad-connect].
    * Identiteitsgegevens die worden gesynchroniseerd, zijn onder andere gebruikersaccounts en groepslidmaatschappen.
* Het IT-team van Litware schakelt Azure AD DS in voor hun Azure AD-tenant in dit, of in een gepeerd, virtueel netwerk.
* Toepassingen en virtuele machines die in het virtuele Azure-netwerk zijn geïmplementeerd, kunnen vervolgens gebruik maken van Azure AD DS-functies zoals domeindeelname, LDAP-leesbewerkingen, LDAP-bindingen, NTLM- en Kerberos-verificatie en groepsbeleid.

> [!IMPORTANT]
> Azure AD Connect moet alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Het installeren van Azure AD Connect in een beheerd domein om objecten weer naar Azure AD te synchroniseren, wordt niet ondersteund.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS voor organisaties die alleen de cloud gebruiken

Een Azure AD-tenant die alleen in de cloud bestaat, beschikt niet over een on-premises identiteitsbron. Gebruikersaccounts en groepslidmaatschappen worden bijvoorbeeld rechtstreeks in Azure AD gemaakt en beheerd.

Laten we eens kijken naar een voorbeeld voor Contoso, een organisatie die alleen de cloud gebruikt en die Azure AD voor identiteiten gebruikt. Alle gebruikersidentiteiten, hun referenties en groepslidmaatschappen worden gemaakt en beheerd in Azure AD. Er is geen extra configuratie van Azure AD Connect nodig om identiteitsgegevens van een on-premises map te synchroniseren.

![Azure Active Directory Domain Services voor een organisatie die alleen de cloud gebruikt zonder on-premises synchronisatie](./media/overview/cloud-only-tenant.png)

* Toepassingen en serverworkloads waarvoor domeinservices moeten worden geïmplementeerd in een virtueel netwerk in Azure.
* Het IT-team van Contoso schakelt Azure AD DS in voor hun Azure AD-tenant in dit, of in een gepeerd, virtueel netwerk.
* Toepassingen en virtuele machines die in het virtuele Azure-netwerk zijn geïmplementeerd, kunnen vervolgens gebruik maken van Azure AD DS-functies zoals domeindeelname, LDAP-leesbewerkingen, LDAP-bindingen, NTLM- en Kerberos-verificatie en groepsbeleid.

## <a name="secure-administration-of-azure-virtual-machines"></a>Veilig beheer van virtuele Azure-machines

Als u één set AD-referenties wilt gebruiken, kunnen virtuele Azure-machines (Vm's) worden toegevoegd aan een door Azure AD DS beheerd domein. Deze aanpak vermindert problemen met het beheer van referenties, zoals het onderhouden van lokale beheerders accounts op elke virtuele machine of het scheiden van accounts en wacht woorden tussen omgevingen.

Vm's die zijn gekoppeld aan een beheerd domein kunnen ook worden beheerd en beveiligd met groeps beleid. Vereiste beveiligings basislijnen kunnen worden toegepast op Vm's om ze te vergren delen in overeenstemming met de beveiligings richtlijnen van het bedrijf. U kunt bijvoorbeeld de mogelijkheden van groeps beleids beheer gebruiken om de typen toepassingen te beperken die kunnen worden gestart op de virtuele machine.

![Gestroomlijnd beheer van virtuele Azure-machines](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Laten we eens kijken naar een gemeen schappelijk voorbeeld scenario. Als servers en andere infra structuur het einde van de levens duur bereiken, wil Contoso toepassingen die momenteel on-premises worden gehost, verplaatsen naar de Cloud. Het huidige IT-standaard mandaat dat servers die bedrijfs toepassingen hosten, moeten lid zijn van een domein en worden beheerd met groeps beleid.

De IT-beheerder van Contoso zou de voor keur geven aan Vm's die in azure zijn geïmplementeerd om het beheer eenvoudiger te maken, terwijl gebruikers zich kunnen aanmelden met hun bedrijfs referenties. Bij het lid van een domein kunnen Vm's ook worden geconfigureerd om te voldoen aan de vereiste beveiligings basislijnen met behulp van groeps beleidsobjecten (Gpo's). Contoso zou het voor keur hebben om hun eigen domein controllers in azure te implementeren, te controleren en te beheren.

Azure AD DS is in het bijzonder geschikt voor deze gebruiks case. Met een beheerd domein kunt u domein deel nemen aan Vm's, een enkele set referenties gebruiken en groeps beleid Toep assen. En omdat het een beheerd domein is, hoeft u de domein controllers niet zelf te configureren en te onderhouden.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* In beheerde domeinen wordt standaard één platte organisatie-eenheid (OE) gebruikt. Alle virtuele machines die lid zijn van een domein, bevinden zich in één organisatie-eenheid. U kunt desgewenst [aangepaste organisatie-eenheden][custom-ou]maken.
* Azure AD DS maakt gebruik van een ingebouwde GPO voor de containers gebruikers en computers. Voor extra besturings elementen kunt u [aangepaste groeps beleidsobjecten maken][create-gpo] en deze op aangepaste organisatie-eenheden richten.
* Azure AD DS ondersteunt het basis-schema voor AD-computer objecten. U kunt het schema van het computer object niet uitbreiden.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>On-premises toepassingen gebruiken die gebruikmaken van LDAP-bindings verificatie

Als voorbeeld scenario heeft Contoso een on-premises toepassing die veel jaren geleden van een ISV is gekocht. De toepassing bevindt zich momenteel in de onderhouds modus door de ISV en het aanvragen van wijzigingen aan de toepassing is prohibitively duur. Deze toepassing heeft een webfrontend waarmee gebruikers referenties worden verzameld met behulp van een webformulier en vervolgens gebruikers verifieert door een LDAP-binding uit te voeren op de on-premises AD DS omgeving.

![LDAP-binding](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso wil deze toepassing migreren naar Azure. De toepassing moet blijven werken, zonder dat u wijzigingen hoeft aan te brengen. Daarnaast moeten gebruikers zich kunnen verifiëren met hun bestaande bedrijfs referenties en zonder extra training. Het moet transparant zijn voor eind gebruikers waarop de toepassing wordt uitgevoerd.

Voor dit scenario kunnen toepassingen met Azure AD DS LDAP-bindingen uitvoeren als onderdeel van het verificatie proces. Bestaande on-premises toepassingen kunnen in azure overstappen en verschuiven en kunnen gebruikers naadloos verifiëren zonder enige wijziging in de configuratie of gebruikers ervaring.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* Zorg ervoor dat de toepassing niet aan de directory hoeft te wijzigen/schrijven. LDAP-schrijf toegang voor een beheerd domein wordt niet ondersteund.
* U kunt wacht woorden niet rechtstreeks wijzigen voor een beheerd domein. Eind gebruikers kunnen hun wacht woord wijzigen met behulp [van de self-service voor wachtwoord wijziging van Azure AD][sspr] of de on-premises Directory. Deze wijzigingen worden vervolgens automatisch gesynchroniseerd en beschikbaar in het beheerde domein.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>On-premises toepassingen die gebruikmaken van LDAP, gebruiken om toegang te krijgen tot de adres lijst

Net als bij het vorige voorbeeld scenario gaan we ervan uitgaan dat contoso een on-premises LOB-toepassing (line-of-Business) heeft die bijna een jaar geleden is ontwikkeld. Deze toepassing is Directory-Aware en is ontworpen om LDAP te gebruiken voor het lezen van informatie/kenmerken over gebruikers van AD DS. De toepassing wijzigt geen kenmerken of op een andere manier naar de map schrijven.

Contoso wil deze toepassing migreren naar Azure en buiten gebruik stellen op de lokale hardware die momenteel als host fungeert voor deze toepassing. De toepassing kan niet worden herschreven voor het gebruik van moderne Directory-Api's, zoals de op REST gebaseerde Microsoft Graph-API. Er is een lift-en Shift-optie gewenst waarbij de toepassing kan worden gemigreerd om te worden uitgevoerd in de Cloud, zonder code te wijzigen of de toepassing te herschrijven.

Om u te helpen bij dit scenario kunnen toepassingen met Azure AD DS LDAP-lees bewerkingen uitvoeren op het beheerde domein om de benodigde kenmerk gegevens op te halen. De toepassing hoeft niet opnieuw te worden geschreven, dus met een lift-en-verschuiving in azure kunnen gebruikers de app blijven gebruiken zonder dat er sprake is van een wijziging in de locatie waar deze wordt uitgevoerd.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* Zorg ervoor dat de toepassing niet aan de directory hoeft te wijzigen/schrijven. LDAP-schrijf toegang voor een beheerd domein wordt niet ondersteund.
* Zorg ervoor dat de toepassing geen aangepast/uitgebreid Active Directory schema nodig heeft. Schema-uitbrei dingen worden niet ondersteund in azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Een on-premises service of daemon-toepassing migreren naar Azure

Sommige toepassingen bevatten meerdere lagen, waarbij een van de lagen een geauthenticeerde aanroep naar een backend-laag, zoals een Data Base, moet uitvoeren. AD-service accounts worden vaak gebruikt in deze scenario's. Wanneer u toepassingen in azure opneemt, kunt u met Azure AD DS de service accounts op dezelfde manier blijven gebruiken. U kunt ervoor kiezen om hetzelfde service account te gebruiken dat is gesynchroniseerd vanuit uw on-premises Directory naar Azure AD of een aangepaste organisatie-eenheid te maken en vervolgens een afzonderlijk service account in die organisatie-eenheid te maken. Met beide benaderingen blijven toepassingen op dezelfde manier werken om geverifieerde aanroepen naar andere lagen en services te maken.

![Service account met behulp van WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

In dit voorbeeld scenario heeft Contoso een aangepaste software kluis toepassing die een web-front-end, een SQL-Server en een back-end-FTP-server bevat. Geïntegreerde Windows-verificatie met Service accounts verifieert de web-front-end naar de FTP-server. De web-front-end is ingesteld om te worden uitgevoerd als een service account. De back-endserver is geconfigureerd om toegang te verlenen van het service account voor de web-front-end. Contoso wil niet dat hun eigen domein controller-Vm's in de cloud worden geïmplementeerd en beheerd om deze toepassing naar Azure te verplaatsen.

Voor dit scenario kunnen de servers die als host fungeren voor de web-front-end, SQL Server en de FTP-server worden gemigreerd naar virtuele Azure-machines en worden toegevoegd aan een beheerd domein. De Vm's kunnen vervolgens hetzelfde service account in hun on-premises Directory gebruiken voor de verificatie doeleinden van de app, die wordt gesynchroniseerd via Azure AD met behulp van Azure AD Connect.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* Zorg ervoor dat de toepassingen een gebruikers naam en wacht woord voor verificatie gebruiken. Verificatie op basis van een certificaat of Smart Card wordt niet ondersteund door Azure AD DS.
* U kunt wacht woorden niet rechtstreeks wijzigen voor een beheerd domein. Eind gebruikers kunnen hun wacht woord wijzigen met behulp [van de self-service voor wachtwoord wijziging van Azure AD][sspr] of de on-premises Directory. Deze wijzigingen worden vervolgens automatisch gesynchroniseerd en beschikbaar in het beheerde domein.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server-implementaties van extern bureau blad-Services in azure

U kunt Azure AD DS gebruiken om beheerde domein services te bieden aan extern bureau blad-servers die zijn geïmplementeerd in Azure.

Zie [Azure AD Domain Services integreren met uw RDS-implementatie][windows-rds]voor meer informatie over dit implementatie scenario.

## <a name="domain-joined-hdinsight-clusters"></a>HDInsight-clusters die zijn gekoppeld aan een domein

U kunt een Azure HDInsight-cluster instellen dat is gekoppeld aan een beheerd domein waarvoor Apache zwerver is ingeschakeld. U kunt Hive-beleid maken en Toep assen via Apache zwerver, en gebruikers, zoals gegevens wetenschappers, toestaan verbinding te maken met hive met behulp van ODBC-hulpprogram ma's zoals Excel of tableau. We blijven werken om andere workloads, zoals HBase, Spark en Storm, toe te voegen aan HDInsight die lid is van een domein.

Zie [HDInsight-clusters die zijn gekoppeld aan een domein configureren][hdinsight] voor meer informatie over dit implementatie scenario.

## <a name="next-steps"></a>Volgende stappen

[Maak en configureer een Azure Active Directory Domain Services beheerd domein][tutorial-create-instance]om aan de slag te gaan.

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds