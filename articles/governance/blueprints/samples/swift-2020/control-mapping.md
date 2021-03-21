---
title: Besturingselementen van het blauwdrukvoorbeeld SWIFT CSP-CSCF v2020
description: Beheer de toewijzing van het blauwdrukvoorbeeld SWIFT CSP-CSCF v2020. Elke beheeroptie wordt toegewezen aan een of meer Azure Policy-definities die helpen bij de evaluatie.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 466b37b287d79a891fba39b133ab5fa79dbd147e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98048790"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Beheer de toewijzing van het blauwdrukvoorbeeld SWIFT CSP-CSCF v2020

In het volgende artikel leest u hoe u het blauwdrukvoorbeeld SWIFT CSP-CSCF v2020 van Azure Blueprints toewijst aan de besturingselementen van SWIFT CSP-CSCF v2020. Zie [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp) voor meer informatie over de besturingselementen.

De volgende toewijzingen zijn voor de besturingselementen van **SWIFT CSP-CSCF v2020**. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens het ingebouwde beleidsinitiatief **\[Preview\]: SWIFT CSP-CSCF v2020-besturingselementen controleren en specifieke extensies implementeren ter ondersteuning van de controlevereisten**.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1.2 en 5.1 Account Management

Deze blauwdruk helpt u bij het controleren van accounts die mogelijk niet voldoen aan de accountbeheervereisten van uw organisatie. Met deze blauwdruk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen die externe accounts controleren met lees-, schrijf- en eigenaarsmachtigingen voor een abonnement en afgeschafte accounts. Door de evaluatie van de door dit beleid gecontroleerde accounts kunt u de juiste actie ondernemen om ervoor te zorgen dat aan de vereisten voor accountbeheer wordt voldaan.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1, 6.4 en 6.5A Account Management | Op rollen gebaseerde planningen

[Op rollen gebaseerd toegangsbeheer van Azure](../../../../role-based-access-control/overview.md) (Azure RBAC) helpt u te beheren wie toegang heeft tot Azure-resources. Met behulp van de Azure-portal kunt u controleren wie toegang heeft tot Azure-resources en wat hun machtigingen zijn. Met deze blauwdruk wordt ook [Azure Policy](../../../policy/overview.md)-definities toegewezen om het gebruik van Azure Active Directory-verificatie voor SQL-servers en-Service Fabric te controleren. Met behulp van Azure Active Directory-verificatie maakt vereenvoudigd beheer van machtigingen en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services mogelijk. Daarnaast wordt met deze blauwdruk een Azure Policy-definitie toegewezen om het gebruik van aangepaste Azure RBAC-regels te controleren. Wanneer u weet waar aangepaste Azure RBAC-regels zijn geïmplementeerd, kan dit u helpen om te controleren of deze nodig zijn en of deze correct zijn geïmplementeerd, omdat aangepaste Azure RBAC-regels foutgevoelig zijn.

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers
- VM's controleren die niet gebruikmaken van beheerde schijven
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A Account Management | Accountbewaking/ongewoon gebruik

Met Just-In-Time-VM-toegang wordt binnenkomend verkeer naar uw Azure-VM's vergrendeld, zodat u minder kwetsbaar bent voor aanvallen maar wordt tegelijkertijd eenvoudig toegang geboden om verbinding met VM's te kunnen maken wanneer dat nodig is. Alle JIT-aanvragen voor toegang tot virtuele machines worden vastgelegd in het activiteitenlogboek, zodat u kunt controleren op ongewoon gebruik. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken die Just-In-Time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1 en 6.4 Separation of Duties

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er daarentegen te veel eigenaars van Azure-abonnementen zijn, is er een grotere kans op schendingen via een account van een gecompromitteerde eigenaar. Met deze blauwdruk kunt u het juiste aantal eigenaars van Azure-abonnementen bijhouden door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die het aantal eigenaren van Azure-abonnementen controleren. Met deze blauwdruk worden ook Azure Policy-definities toegewezen waarmee u het lidmaatschap van de beheerdersgroep op virtuele Windows-machines kunt beheren. Met het beheer van abonnementseigenaar en beheerdersmachtigingen voor virtuele machines kunt u een juiste scheiding van taken implementeren.

- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Controleresultaten weergeven van virtuele Windows-machines waarop de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren om virtuele Windows-machines te controleren waarop de groep Administrators niet alle opgegeven leden bevat
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 en 6.4 Least Privilege| Controle van gebruikersbevoegdheden

[Op rollen gebaseerd toegangsbeheer van Azure](../../../../role-based-access-control/overview.md) (Azure RBAC) helpt u te beheren wie toegang heeft tot Azure-resources. Met behulp van de Azure-portal kunt u controleren wie toegang heeft tot Azure-resources en wat hun machtigingen zijn. Met deze blauwdruk wordt [Azure Policy](../../../policy/overview.md)-definities toegewezen om accounts te controleren die prioriteit moeten krijgen voor controles. Door deze accountindicatoren te controleren, kunt u ervoor zorgen dat de beheeropties voor minimale bevoegdheden worden geïmplementeerd.

- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Controleresultaten weergeven van Windows-VM's die niet zijn verbonden met het opgegeven domein
- Vereisten implementeren om Windows-VM's te controleren die niet aan het opgegeven domein worden toegevoegd
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

## <a name="22-and-27-security-attributes"></a>2.2 en 2.7 Security Attributes

De gegevensdetectie en -classificatiefunctie voor Advanced Data Protection voor Azure SQL Database biedt mogelijkheden voor het detecteren, classificeren, labelen en beveiligen van de gevoelige gegevens in uw databases. Het kan worden gebruikt voor het zichtbaar maken van de classificatiestatus van gegevens in uw database, en het traceren van de toegang tot gevoelige gegevens binnen en buiten de database. Met Advanced Data Security kunt u de gegevens waarborgen die zijn gekoppeld aan de juiste beveiligingskenmerken voor uw organisatie. Met deze blauwdruk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen die Advanced Data Security controleren en afdwingen op SQL-servers.

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Advanced Data Security implementeren op SQL-servers

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7, 4.1 en 6.1 Remote Access | Geautomatiseerd(e) bewaking/beheer

Met deze blauwdruk kunt u externe toegang bewaken en beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen om te controleren of externe foutopsporing voor de Azure App Service-toepassing is uitgeschakeld en beleidsdefinities die virtuele Linux-machines controleren zodat externe verbindingen vanaf accounts zonder wachtwoorden zijn toegestaan. Daarnaast wordt met deze blauwdruk een Azure Policy-definitie toegewezen waarmee u onbeperkte toegang tot opslagaccounts kunt controleren. Met de bewaking van deze indicatoren kunt u ervoor zorgen dat externe toegangsmethoden voldoen aan uw beveiligingsbeleid.

- Controleresultaten weergeven van virtuele Linux-machines waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan
- Vereisten implementeren om virtuele Linux-machines te controleren waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan
- Netwerktoegang tot opslagaccounts moet zijn beperkt
- Externe foutopsporing moet worden uitgeschakeld voor API-app
- Externe foutopsporing moet worden uitgeschakeld voor functie-app
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassing

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 en 6.4 Content of Audit Records | Gecentraliseerd beheer van recordinhoud van geplande controles

Logboekgegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werkruimte, waardoor configuratie en beheer gecentraliseerd kan plaatsvinden. Met deze blauwdruk kunt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die de implementatie van de Log Analytics-agent op virtuele Azure-machines controleren en afdwingen.

- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Log Analytics-agent voor virtuele Linux-machines implementeren
- Log Analytics-agent voor virtuele Windows-machines implementeren

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 en 6.4 Response to Audit Processing Failures

Met deze blauwdruk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen waarmee configuraties van logboekregistraties van controles en gebeurtenissen worden gecontroleerd. Het bewaken van deze configuraties kan een indicatie geven van een storing in een controlesysteem of een verkeerde configuratie, op basis waarvan u corrigerende maatregelen kunt uitvoeren.

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Diagnostische instelling voor controleren
- Controle op SQL-servers moet zijn ingeschakeld

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 en 6.4 Audit Review, Analysis, and Reporting | Centrale controle en analyse

Logboekgegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werkruimte, waardoor gecentraliseerde rapportage en analyse mogelijk wordt. Met deze blauwdruk kunt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die de implementatie van de Log Analytics-agent op virtuele Azure-machines controleren en afdwingen.

- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Log Analytics-agent voor virtuele Linux-machines implementeren
- Log Analytics-agent voor virtuele Windows-machines implementeren

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2, 2.7, 6.4 en 6.5A Audit Generation

Met deze blauwdruk kunt u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee u logboekinstellingen op Azure-resources kunt controleren.
Met deze beleidsdefinities wordt de implementatie gecontroleerd en afgedwongen van de Log Analytics-agent op virtuele Azure-machines en het controle-instellingen voor andere Azure-resourcetypen geconfigureerd. Met deze beleidsdefinities wordt ook de configuratie gecontroleerd van diagnostische logboeken om inzicht te bieden in bewerkingen die worden uitgevoerd binnen Azure-resources. Daarnaast worden controle en Advanced Data Security geconfigureerd op SQL-servers.

- Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Log Analytics-agent voor Linux-VM-schaalsets (VMSS) implementeren
- Log Analytics-agent voor virtuele Linux-machines implementeren
- Log Analytics-agent voor Windows-VM-schaalsets (VMSS) implementeren
- Log Analytics-agent voor virtuele Windows-machines implementeren
- Diagnostische instelling voor controleren
- Instellingen voor controle op SQL-serverniveau controleren
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Advanced Data Security implementeren op SQL-servers
- Controle op SQL-servers moet zijn ingeschakeld
- Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 Least Functionality | Programma-uitvoering voorkomen

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het filteren van toepassingen die kan blokkeren of voorkomen dat specifieke software wordt uitgevoerd op uw virtuele machines. Toepassingsbeheer kan worden uitgevoerd in een afdwingingsmodus waardoor niet-goedgekeurde toepassingen niet kunnen worden uitgevoerd. Met deze blauwdruk wordt een Azure Policy-definitie toegewezen waarmee u virtuele machines kunt bewaken waarvoor een acceptatielijst voor toepassingen wordt aanbevolen, maar die nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 Least Functionality | Geautoriseerde software/in toegestane lijst opnemen

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het filteren van toepassingen die kan blokkeren of voorkomen dat specifieke software wordt uitgevoerd op uw virtuele machines. Met toepassingsbeheer kunt u lijsten met goedgekeurde toepassingen maken voor uw virtuele machines. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken waarvoor een acceptatielijst voor toepassingen wordt aanbevolen, maar die nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers

## <a name="11-user-installed-software"></a>1.1 User-Installed Software

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het filteren van toepassingen die kan blokkeren of voorkomen dat specifieke software wordt uitgevoerd op uw virtuele machines. Met toepassingsbeheer kunt u naleving van softwarerestrictiebeleid afdwingen en bewaken. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken waarvoor een acceptatielijst voor toepassingen wordt aanbevolen, maar die nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 Identification and Authentication (gebruikers binnen organisatie) | Netwerktoegang tot bevoegde accounts

Met deze blauwdruk kunt u bevoegde toegang beperken en beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee accounts met eigenaars- en/of schrijfmachtigingen worden gecontroleerd waarvoor geen meervoudige verificatie is ingeschakeld. U kunt met meervoudige verificatie accounts veilig houden, zelfs als er wordt geknoeid met één onderdeel van de verificatiegegevens. Door accounts te controleren waarvoor geen meervoudige verificatie is ingeschakeld, kunt u accounts identificeren waarvoor het waarschijnlijker is dat deze worden gecompromitteerd.

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 Identification and Authentication (gebruikers binnen organisatie) | Netwerktoegang tot niet-bevoegde accounts

Met deze blauwdruk kunt u toegang beperken en beheren door een [Azure Policy](../../../policy/overview.md)-definitie toe te wijzen om accounts met leesmachtigingen te controleren waarvoor geen meervoudige verificatie is ingeschakeld. U kunt met meervoudige verificatie accounts veilig houden, zelfs als er wordt geknoeid met één onderdeel van de verificatiegegevens. Door accounts te controleren waarvoor geen meervoudige verificatie is ingeschakeld, kunt u accounts identificeren waarvoor het waarschijnlijker is dat deze worden gecompromitteerd.

- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

## <a name="23-and-41-authenticator-management"></a>2.3 en 4.1 Authenticator Management

Met deze blauwdruk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen waarmee virtuele Linux-machines worden gecontroleerd die externe verbindingen toestaan met accounts zonder wachtwoorden en/of waarbij onjuiste machtigingen in het passwd-bestand zijn ingesteld. Met deze blauwdruk worden ook beleidsdefinities toegewezen waarmee de configuratie van het type wachtwoordversleuteling voor virtuele Windows-machines wordt gecontroleerd. Door deze indicatoren te controleren, zorgt u ervoor dat systeemverificators voldoen aan het identificatie- en verificatiebeleid van uw organisatie.

- Controleresultaten weergeven van virtuele Linux-machines waarvoor de machtigingen van het passwd-bestand niet zijn ingesteld op 0644
- Vereisten implementeren om Linux-VM's te controleren waarvoor de machtigingen van het passwd-bestand niet zijn ingesteld op 0644
- Controleresultaten weergeven van virtuele Linux-machines met accounts zonder wachtwoorden
- Vereisten implementeren om Linux-VM's met accounts zonder wachtwoorden te controleren
- Controleresultaten weergeven van virtuele Windows-machines waarop wachtwoorden niet worden opgeslagen met omkeerbare versleuteling
- Vereisten implementeren om Windows-VM's te controleren waarop wachtwoorden niet worden opgeslagen met omkeerbare versleuteling

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3 en 4.1 Authenticator Management | Verificatie op basis van wachtwoorden

Met deze blauwdruk kunt u sterke wachtwoorden afdwingen door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee virtuele Windows-machines worden gecontroleerd waarop geen vereisten voor minimale sterkte en andere wachtwoordvereisten worden afgedwongen. Wanneer u op de hoogte bent van virtuele machines die in strijd zijn met het beleid voor wachtwoordsterkte, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle gebruikersaccounts van de virtuele machines voldoen aan het wachtwoordbeleid van uw organisatie.

- Controleresultaten weergeven van virtuele Windows-machines waarvoor de voorgaande 24 wachtwoorden opnieuw kunnen worden gebruikt
- Controleresultaten weergeven van virtuele Windows-machines waarvoor geen maximale wachtwoordduur van 70 dagen is ingesteld
- Controleresultaten weergeven van virtuele Windows-machines waarvoor geen minimale wachtwoordduur van 1 dag is ingesteld
- Controleresultaten weergeven van virtuele Windows-machines waarop de instelling voor wachtwoordcomplexiteit niet is ingeschakeld
- Controleresultaten weergeven van virtuele Windows-machines waarvoor de minimale wachtwoordlengte niet wordt beperkt tot 14 tekens
- Controleresultaten weergeven van virtuele Windows-machines waarop wachtwoorden niet worden opgeslagen met omkeerbare versleuteling
- Vereisten implementeren om virtuele Windows-machines te controleren waarvoor de voorgaande 24 wachtwoorden opnieuw kunnen worden gebruikt
- Vereisten implementeren om virtuele Windows-machines te controleren waarvoor geen maximale wachtwoordduur van 70 dagen is ingesteld
- Vereisten implementeren om virtuele Windows-machines te controleren waarvoor geen minimale wachtwoordduur van 1 dag is ingesteld
- Vereisten implementeren om virtuele Windows-machines te controleren waarop de instelling voor wachtwoordcomplexiteit niet is ingeschakeld
- Vereisten implementeren om virtuele Windows-machines te controleren waarvoor de minimale wachtwoordlengte niet wordt beperkt tot 14 tekens
- Vereisten implementeren om virtuele Windows-machines te controleren waarop wachtwoorden niet worden opgeslagen met omkeerbare versleuteling

## <a name="22-and-27-vulnerability-scanning"></a>2.2 en 2.7 Vulnerability Scanning

Met deze blauwdruk kunt u beveiligingsproblemen met informatiesystemen beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee wordt gecontroleerd op beveiligingsproblemen met het besturingssysteem, SQL en virtuele machines in Azure Security Center.
Azure Security Center biedt rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources. Met deze blauw druk worden ook beleidsdefinities toegewezen die Advanced Data Security controleren en afdwingen op SQL-servers. In Advanced Data Security is de evaluatie van beveiligingsproblemen en Advanced Threat Protection-functies opgenomen, waarmee u meer inzicht krijgt in beveiligingsproblemen in de door u geïmplementeerde resources.

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Controle op SQL-servers moet zijn ingeschakeld
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost 
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld

## <a name="13-denial-of-service-protection"></a>1.3 Denial of Service Protection

De DDoS-standaardlaag (DDoS: Distributed Denial of service) van Azure biedt extra functies en mogelijkheden voor risicobeperking via de Basic-servicelaag. Deze aanvullende functies omvatten Azure Monitor-integratie en de mogelijkheid om risicobeperkingsrapporten te bekijken na aanvallen. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen die controleert of de DDoS standaardlaag is ingeschakeld. Wanneer u inzicht hebt in het verschil in functionaliteit tussen de servicelagen, kunt u de beste oplossing selecteren voor DDos-beveiligingen voor uw Azure-omgeving.

- Azure DDoS-beveiligingsstandaard moet zijn ingeschakeld

## <a name="11-and-61-boundary-protection"></a>1.1 en 6.1 Boundary Protection

Met deze blauwdruk kunt u de systeemgrens beheren en bepalen door een [Azure Policy](../../../policy/overview.md)-definitie toe te wijzen waarmee u kunt controleren op aanbevelingen voor de beveiliging van netwerkbeveiligingsgroepen in Azure Security Center. Azure Security Center analyseert verkeerspatronen van virtuele machines die vanaf het internet toegankelijk zijn, en formuleert aanbevelingen voor netwerkbeveiligingsgroepsregels die de mogelijkheid op aanvallen beperken Daarnaast wordt met deze blauwdruk ook beleidsdefinities toegewezen waarmee onbeveiligde eindpunten, toepassingen en opslagaccounts worden bewaakt. Eindpunten en toepassingen die niet zijn beveiligd door een firewall, en opslagaccounts met onbeperkte toegang, kunnen onbedoeld toegang verlenen tot gegevens in het informatiesysteem.

- Aanbevelingen voor Adaptieve netwerkbeveiliging moeten worden toegepast op op internet gerichte virtuele machines
- Toegang via een eindpunt gericht op internet moet worden beperkt
- Onbeperkte netwerktoegang tot opslagaccounts controleren

## <a name="29a-boundary-protection--access-points"></a>2.9A Boundary Protection | Toegangspunten

Met Just-In-Time-VM-toegang wordt binnenkomend verkeer naar uw Azure-VM's vergrendeld, zodat u minder kwetsbaar bent voor aanvallen maar wordt tegelijkertijd eenvoudig toegang geboden om verbinding met VM's te kunnen maken wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u het aantal externe verbindingen met uw resources in Azure beperken. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken die Just-In-Time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A Boundary Protection | Externe telecommunicatieservices

Met Just-In-Time-VM-toegang wordt binnenkomend verkeer naar uw Azure-VM's vergrendeld, zodat u minder kwetsbaar bent voor aanvallen maar wordt tegelijkertijd eenvoudig toegang geboden om verbinding met VM's te kunnen maken wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u uitzonderingen beheren voor uw verkeersstroombeleid door de processen voor toegangsaanvragen en -goedkeuring te vergemakkelijken. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken die Just-In-Time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4, 2.4A, 2.5A en 2.6 Transmission Confidentiality and Integrity | Cryptografische of alternatieve fysieke beveiliging

Met deze blauwdruk kunt u de vertrouwelijkheid en integriteit van verzonden informatie beschermen door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee u het cryptografische mechanisme kunt controleren dat is geïmplementeerd voor communicatieprotocollen. Zorg ervoor dat de communicatie op de juiste wijze wordt versleuteld, zodat u aan de vereisten van uw organisatie kunt voldoen of informatie kunt beschermen tegen onbevoegde openbaarmaking en wijziging.

- API-app mag alleen toegankelijk zijn via HTTPS
- Controleresultaten weergeven van Windows-webservers die geen veilige communicatieprotocollen gebruiken
- Vereisten implementeren om Windows-webservers te controleren waarvoor geen veilige communicatieprotocollen worden gebruikt
- Functie-app mag alleen toegankelijk zijn via HTTPS
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Webtoepassing mag alleen toegankelijk zijn via HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3, 2.5, 4.1 en 2.7 Protection of Information at Rest | Cryptografische beveiliging

Met deze blauwdruk kunt u uw beleid afdwingen voor het gebruik van cryptografische beheeropties voor het beveiligen van inactieve informatie door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee u specifieke cryptografische beheeropties kunt afdwingen en het gebruik van zwakke cryptografische instellingen kunt controleren. Wanneer u weet waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat resources worden geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. Met name voor de beleidsdefinities die door deze blauwdrukken worden toegewezen, is vereist dat data lake-opslagaccounts en transparante gegevensversleuteling voor SQL-databases zijn versleuteld, en dat wordt gecontroleerd op ontbrekende versleuteling voor SQL-databases, schijven van virtuele machines en accountvariabelen voor automatisering.

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Advanced Data Security implementeren op SQL-servers
- Transparante gegevensversleuteling in SQL DB implementeren
- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 en 2.7 Flaw Remediation

Met deze blauwdruk kunt u fouten in informatiesystemen beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die waarmee wordt gecontroleerd op ontbrekende systeemupdates, beveiligingsproblemen met het besturingssysteem, SQL en virtuele machines in Azure Security Center. Azure Security Center biedt rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources. Met deze blauwdruk wordt ook een beleidsdefinitie toegewezen die ervoor zorgt dat er patches op het besturingssysteem worden uitgevoerd voor virtuele-machineschaalsets.

- Automatische patching van besturingssysteeminstallatiekopieën op virtuele-machineschaalsets vereisen
- Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd
- Er moeten systeemupdates op uw virtuele machines zijn geïnstalleerd
- Implementatie van Dependency Agent in virtuele-machineschaalsets controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- Automation-accountvariabelen moeten worden versleuteld
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele machines moeten worden hersteld
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost

## <a name="61-malicious-code-protection"></a>6.1 Malicious Code Protection

Met deze blauwdruk kunt u Endpoint Protection beheren, met inbegrip van beveiliging tegen schadelijke code door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee u kunt controleren op ontbrekende Endpoint Protection op virtuele machines in Azure Security Center en de Microsoft-antimalware-oplossing kunt afdwingen op virtuele Windows-machines.

- Microsoft IaaSAntimalware-standaarduitbreiding voor Windows Server implementeren
- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Ontbrekende Endpoint Protection bewaken in Azure Security Center
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources

## <a name="61-malicious-code-protection--central-management"></a>6.1 Malicious Code Protection | Centraal beheer

Met deze blauwdruk kunt u Endpoint Protection beheren, met inbegrip van beveiliging tegen schadelijke code, door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee wordt gecontroleerd op ontbrekende Endpoint Protection op virtuele machines in Azure Security Center. Azure Security Center biedt gecentraliseerd beheer en rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources.

- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Ontbrekende Endpoint Protection bewaken in Azure Security Center

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3, 2.2, 2.7, 2.8 en 6.4 Information System Monitoring

Met deze blauwdruk kunt u uw systeem controleren door logboekregistraties en gegevensbeveiliging in Azure-resources te controleren en af te dwingen. Met name wordt door de toegewezen beleidsregels de implementatie gecontroleerd en afgedwongen van de Log Analytics-agent, en verbeterde beveiligingsinstellingen voor SQL-databases, opslagaccounts en netwerkbronnen. Deze mogelijkheden kunnen u helpen bij het detecteren van afwijkend gedrag en aanwijzingen van aanvallen, zodat u de juiste actie kunt ondernemen.

- Controleresultaten weergeven van Windows-VM's waarop de Log Analytics-agent niet is verbonden zoals verwacht
- Log Analytics-agent voor Linux-VM-schaalsets (VMSS) implementeren
- Log Analytics-agent voor virtuele Linux-machines implementeren
- Log Analytics-agent voor Windows-VM-schaalsets (VMSS) implementeren
- Log Analytics-agent voor virtuele Windows-machines implementeren
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- De Advanced Data Security-instellingen voor SQL Server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- Diagnostische logboeken in Azure Stream Analytics moeten zijn ingeschakeld
- Advanced Data Security implementeren op SQL-servers
- Controle op SQL-servers implementeren
- Network Watcher implementeren wanneer er virtuele netwerken worden gemaakt
- Detectie van bedreigingen implementeren op SQL-servers

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 en 2.8 Information System Monitoring | Verkeer analyseren/exfiltratie converteren

Advanced Threat Protection voor Azure Storage detecteert op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of op aanvallen op opslagaccounts. Beveiligingswaarschuwingen bevatten afwijkende toegangspatronen, afwijkende extracten/uploads en verdachte opslagactiviteit. Met deze aanwijzingen kunt u heimelijke exfiltratie van informatie detecteren.

- Detectie van bedreigingen implementeren op SQL-servers

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan verschillen in Azure Government en andere nationale clouds.

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van beheeropties van blauwdruk SWIFT CSP-CSCF v2020 hebt bekeken, kunt u naar de volgende artikelen gaan voor informatie over de blauwdruk en de implementatie van dit voorbeeld:

> [!div class="nextstepaction"]
> [Blauwdruk SWIFT CSP-CSCF v2020: overzicht](./index.md)
> [Blauwdruk SWIFT CSP-CSCF v2020: implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
