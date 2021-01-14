---
title: Beheeropties voor DoD Impact Level 5-blauwdrukvoorbeeld
description: Beheeropties toewijzen voor het DoD Impact Level 5-blauwdrukvoorbeeld. Elke beheeroptie wordt toegewezen aan een of meer Azure Policy-definities die helpen bij de evaluatie.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 01f786684e5f8d73f57eb9f4741593c01fe1c8d4
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034778"
---
# <a name="control-mapping-of-the-dod-impact-level-5-blueprint-sample"></a>Beheeropties toewijzen voor het DoD Impact Level 5-blauwdrukvoorbeeld

Het volgende artikel bevat informatie over de manier waarop de Azure Blueprints Department of Defense Impact Level 5 (DoD IL5) blauwdrukvoorbeelden toewijst aan de beheeropties voor DoD Impact Level 5. Zie voor meer informatie over de beheeropties [DoD Cloud Computing Security Requirements Guide (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf).
De Defense Information Systems Agency (DISA) is een instantie van het Amerikaanse ministerie van defensie dat verantwoordelijk is voor het ontwikkelen en onderhouden van de DoD Cloud Computing Security Requirements Guide (SRG). De SRG definieert de basisbeveiligingsvereisten voor cloudserviceproviders (CSP's) die als host dienen voor DoD-informatie, systemen en toepassingen, en voor het gebruik van cloudservices door DoD.  

De volgende toewijzingen zijn voor de **DoD Impact Level 5**-beheeropties. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens de **\[Preview\]: DoD Impact Level 5** ingebouwd beleidsinitiatief.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-5/control-mapping.md).

## <a name="ac-2-account-management"></a>AC-2 Account Management

Deze blauwdruk helpt u bij het controleren van accounts die mogelijk niet voldoen aan de accountbeheervereisten van uw organisatie. Met deze blauwdruk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen die externe accounts controleren met lees-, schrijf- en eigenaarsmachtigingen voor een abonnement en afgeschafte accounts. Door de evaluatie van de door dit beleid gecontroleerde accounts kunt u de juiste actie ondernemen om ervoor te zorgen dat aan de vereisten voor accountbeheer wordt voldaan.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Account Management | Op rollen gebaseerde planningen

In Azure wordt [op rollen gebaseerde toegangsbeheer van Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) geïmplementeerd, waarmee u de toegang tot resources in Azure kunt beheren. Met behulp van de Azure-portal kunt u controleren wie toegang heeft tot Azure-resources en wat hun machtigingen zijn. Met deze blauwdruk wordt ook [Azure Policy](../../../policy/overview.md)-definities toegewezen om het gebruik van Azure Active Directory-verificatie voor SQL-servers en-Service Fabric te controleren. Met behulp van Azure Active Directory-verificatie maakt vereenvoudigd beheer van machtigingen en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services mogelijk. Daarnaast wordt met deze blauwdruk een Azure Policy-definitie toegewezen om het gebruik van aangepaste Azure RBAC-regels te controleren. Wanneer u weet waar aangepaste Azure RBAC-regels zijn geïmplementeerd, kan dit u helpen om te controleren of deze nodig zijn en of deze correct zijn geïmplementeerd, omdat aangepaste Azure RBAC-regels foutgevoelig zijn.

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers
- Het gebruik van aangepaste RBAC-regels controleren
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Account Management | Accountbewaking/ongewoon gebruik

Just-In-Time-VM-toegang vergrendelt binnenkomend verkeer naar uw Azure-VM's, zodat u minder kwetsbaar bent voor aanvallen maar tegelijkertijd eenvoudig toegang wordt geboden om verbinding met VM's te kunnen maken wanneer dat nodig is. Alle JIT-aanvragen voor toegang tot virtuele machines worden vastgelegd in het activiteitenlogboek, zodat u dit kunt controleren op ongewoon gebruik. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Information Flow Enforcement

Cross Origin Resource Sharing (CORS) kan toestaan dat App Services-resources worden aangevraagd vanuit een extern domein. Microsoft raadt u aan om alleen vereiste domeinen te laten communiceren met uw API, functie en webtoepassingen. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u de toegangsbeperkingen voor CORS-bronnen in Azure Security Center kunt bewaken. Met inzicht in CORS-implementaties kunt u controleren of de beheeropties voor informatiestromen zijn geïmplementeerd.

- Gebruik van CORS mag er niet toe leiden dat elke resource toegang heeft tot uw webtoepassingen

## <a name="ac-5-separation-of-duties"></a>AC-5 Separation of Duties

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er daarentegen te veel eigenaars van Azure-abonnementen zijn, is er een grotere kans op schendingen via een account van een gecompromitteerde eigenaar. Met deze blauwdruk kunt u het juiste aantal eigenaars van Azure-abonnementen bijhouden door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die het aantal eigenaren van Azure-abonnementen controleren. Met deze blauwdruk worden ook Azure Policy-definities toegewezen waarmee u het lidmaatschap van de beheerdersgroep op virtuele Windows-machines kunt beheren. Met het beheer van abonnementseigenaar en beheerdersmachtigingen voor virtuele machines kunt u een juiste scheiding van taken implementeren.

- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Controleresultaten weergeven van virtuele Windows-machines waarop de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarop de groep Administrators niet alle opgegeven leden bevat
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Least Privilege | Controle van gebruikersbevoegdheden

In Azure wordt [op rollen gebaseerde toegangsbeheer van Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) geïmplementeerd, waarmee u de toegang tot resources in Azure kunt beheren. Met behulp van de Azure-portal kunt u controleren wie toegang heeft tot Azure-resources en wat hun machtigingen zijn. Met deze blauwdruk wordt [Azure Policy](../../../policy/overview.md)-definities toegewezen om accounts te controleren die prioriteit moeten krijgen voor controles. Door deze accountindicatoren te controleren, kunt u ervoor zorgen dat de beheeropties voor minimale bevoegdheden worden geïmplementeerd.

- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Controleresultaten weergeven van virtuele Windows-machines waarop de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarop de groep Administrators niet alle opgegeven leden bevat
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Remote Access | Automated Monitoring / Control

Met deze blauwdruk kunt u externe toegang bewaken en beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen om te controleren of externe foutopsporing voor de Azure App Service-toepassing is uitgeschakeld en beleidsdefinities die virtuele Linux-machines controleren zodat externe verbindingen vanaf accounts zonder wachtwoorden zijn toegestaan. Daarnaast wordt met deze blauwdruk een Azure Policy-definitie toegewezen waarmee u onbeperkte toegang tot opslagaccounts kunt controleren. Met de bewaking van deze indicatoren kunt u ervoor zorgen dat externe toegangsmethoden voldoen aan uw beveiligingsbeleid.

- Controleresultaten weergeven van virtuele Linux-machines waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan
- Vereisten implementeren om virtuele Linux-machines te controleren waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan
- Netwerktoegang tot opslagaccounts moet zijn beperkt
- Externe foutopsporing moet worden uitgeschakeld voor API-apps
- Externe foutopsporing moet worden uitgeschakeld voor Function-apps
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen

## <a name="ac-23-data-mining"></a>AC-23 Gegevensanalyse

Deze blauwdruk zorgt ervoor dat controle en geavanceerde gegevensbeveiliging wordt geconfigureerd op SQL-servers.

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance
- Controle op SQL-servers moet zijn ingeschakeld

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Content of Audit Records | Gecentraliseerd beheer van recordinhoud van geplande controles

Logboekgegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werkruimte, waardoor gecentraliseerd(e) configuratie en beheer mogelijk wordt. Met deze blauwdruk kunt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die de implementatie van de Log Analytics-agent op virtuele Azure-machines controleren en afdwingen.

- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Implementatie van de Log Analytics-agent in virtuele-machineschaalsets controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- Log Analytics-werkruimte voor VM controleren - niet-overeenkomende items rapporteren
- De Log Analytics-agent moet worden geïnstalleerd op virtuele-machineschaalsets
- De Log Analytics-agent moet worden geïnstalleerd op virtuele machines

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Response to Audit Processing Failures

Met deze blauw druk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen waarmee configuraties van logboekregistraties van controles en gebeurtenissen worden gecontroleerd. Het bewaken van deze configuraties kan een indicatie geven van een storing in een controlesysteem of een verkeerde configuratie, op basis waarvan u corrigerende maatregelen kunt uitvoeren.

- Diagnostische instelling voor controleren
- Controle op SQL-servers moet zijn ingeschakeld
- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Audit Review, Analysis, and Reporting | Centrale controle en analyse

Logboekgegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werkruimte, waardoor gecentraliseerde rapportage en analyse mogelijk wordt. Met deze blauwdruk kunt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die de implementatie van de Log Analytics-agent op virtuele Azure-machines controleren en afdwingen.

- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Implementatie van de Log Analytics-agent in virtuele-machineschaalsets controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- Log Analytics-werkruimte voor VM controleren - niet-overeenkomende items rapporteren

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) controle, analyse en rapportage | Integratie/scan- en bewakingsmogelijkheden

Deze blauwdruk bevat beleidsdefinities waarmee u records kunt controleren met evaluatie van beveiligingsproblemen op virtuele machines, virtuele-machineschaalsets, SQL Database-servers en servers van beheerde SQL-exemplaren. Met deze beleidsdefinities wordt ook de configuratie gecontroleerd van diagnostische logboeken om inzicht te bieden in bewerkingen die worden uitgevoerd binnen Azure-resources. Deze inzichten bieden realtime informatie over de beveiligingsstatus van uw geïmplementeerde resources en kunnen u helpen bij het rangschikken van herstelacties. Voor uitgebreide scans en controles van beveiligingsproblemen raden we u aan om ook Azure Sentinel en Azure Security Center te gebruiken.

- Diagnostische instelling voor controleren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld voor SQL Managed Instance
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Implementatie van de Log Analytics-agent in virtuele-machineschaalsets controleren - VM-installatiekopie (besturingssysteem) niet vermeld

## <a name="au-12-audit-generation"></a>AU-12 Audit Generation

Deze blauwdruk bevat beleidsdefinities waarmee de implementatie van de Log Analytics-agent op virtuele Azure-machines wordt gecontroleerd en afgedwongen en de controle-instellingen voor andere Azure-resourcetypen worden geconfigureerd.
Met deze beleidsdefinities wordt ook de configuratie gecontroleerd van diagnostische logboeken om inzicht te bieden in bewerkingen die worden uitgevoerd binnen Azure-resources. Daarnaast worden controle en Advanced Data Security geconfigureerd op SQL-servers.

- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Implementatie van de Log Analytics-agent in virtuele-machineschaalsets controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- Log Analytics-werkruimte voor VM controleren - niet-overeenkomende items rapporteren
- Diagnostische instelling voor controleren
- Controle op SQL-servers moet zijn ingeschakeld
- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) Genereren van controles | Tijdgecorreleerde audittrail van het volledige systeem

Met deze blauwdruk kunt u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)definities toe te wijzen die logboekinstellingen op Azure-resources controleren.
Voor dit ingebouwde beleid moet u een matrix met resourcetypen opgeven om te controleren of diagnostische instellingen zijn ingeschakeld.

- Diagnostische instelling voor controleren

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Least Functionality | Programma-uitvoering voorkomen

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het accepteren van toepassingen die kan blokkeren of voorkomen dat specifieke software wordt uitgevoerd op uw virtuele machines. Toepassingsbeheer kan worden uitgevoerd in een afdwingingsmodus waardoor niet-goedgekeurde toepassingen niet kunnen worden uitgevoerd. Met deze blauwdruk wordt een Azure Policy-definitie toegewezen waarmee u virtuele machines kunt bewaken waarvoor een acceptatielijst voor toepassingen wordt aanbevolen, maar die nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Least Functionality | Geautoriseerde software/in whitelist opnemen

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het accepteren van toepassingen die kan blokkeren of voorkomen dat specifieke software wordt uitgevoerd op uw virtuele machines. Met toepassingsbeheer kunt u lijsten met goedgekeurde toepassingen maken voor uw virtuele machines. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken waarvoor een acceptatielijst voor toepassingen wordt aanbevolen, maar die nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers

## <a name="cm-11-user-installed-software"></a>CM-11 User-Installed Software

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het accepteren van toepassingen die kan blokkeren of voorkomen dat specifieke software wordt uitgevoerd op uw virtuele machines. Met toepassingsbeheer kunt u naleving van softwarerestrictiebeleid afdwingen en bewaken. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken waarvoor een acceptatielijst voor toepassingen wordt aanbevolen, maar die nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers

## <a name="cp-7-alternate-processing-site"></a>CP-7 Alternate Processing Site

Met Azure Site Recovery worden workloads die worden uitgevoerd op virtuele machines gerepliceerd van een primaire site naar een secundaire locatie. Als er een storing optreedt op de primaire site, wordt een failover-overschakeling voor de workload uitgevoerd naar de secundaire locatie. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee virtuele machines worden gecontroleerd waarop geen noodherstel is geconfigureerd. Door deze indicator te bewaken, kunt u ervoor zorgen dat de noodzakelijke beheeropties voor onvoorziene gebeurtenissen zijn ingeschakeld.

- Virtuele machines controleren waarop geen noodherstel is geconfigureerd

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) Back-up van informatiesysteem | Overdracht naar alternatieve opslagsite

Deze blauwdruk wijst Azure-beleidsdefinities toe waarmee de systeemback-upinformatie van de organisatie op de alternatieve opslagsite elektronisch kan worden gecontroleerd. Overweeg het gebruik van Azure Data Box voor fysieke verzending van opslagmetagegevens.

- Geografisch redundante opslag moet zijn ingeschakeld voor opslagaccounts
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL
- Geografisch redundante back-up op de lange termijn moet zijn ingeschakeld voor Azure SQL Databases

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identification and Authentication (gebruikers binnen organisatie) | Netwerktoegang tot bevoegde accounts

Met deze blauw druk kunt u bevoegde toegang beperken en beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen om accounts met eigenaars- en/of schrijfmachtigingen te controleren waarvoor geen meervoudige verificatie is ingeschakeld. U kunt met meervoudige verificatie accounts veilig houden, zelfs als er wordt geknoeid met één onderdeel van de verificatiegegevens. Door accounts te controleren waarvoor geen meervoudige verificatie is ingeschakeld, kunt u accounts identificeren waarvoor het waarschijnlijker is dat deze worden gecompromitteerd.

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identification and Authentication (gebruikers binnen organisatie) | Netwerktoegang tot niet-bevoegde accounts

Met deze blauwdruk kunt u toegang beperken en beheren door een [Azure Policy](../../../policy/overview.md)-definitie toe te wijzen om accounts met leesmachtigingen te controleren waarvoor geen meervoudige verificatie is ingeschakeld. U kunt met meervoudige verificatie accounts veilig houden, zelfs als er wordt geknoeid met één onderdeel van de verificatiegegevens. Door accounts te controleren waarvoor geen meervoudige verificatie is ingeschakeld, kunt u accounts identificeren waarvoor het waarschijnlijker is dat deze worden gecompromitteerd.

- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

## <a name="ia-5-authenticator-management"></a>IA-5 Authenticator Management

Met de blauwdruk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen waarmee virtuele Linux-machines worden gecontroleerd die externe verbindingen toestaan met accounts zonder wachtwoorden en/of waarbij onjuiste machtigingen op het wachtwoordbestand zijn ingesteld. Met deze blauwdruk worden ook beleidsdefinities toegewezen waarmee de configuratie van het type wachtwoordversleuteling voor virtuele Windows-machines wordt gecontroleerd. Door deze indicatoren te controleren, zorgt u ervoor dat systeemverificators voldoen aan het identificatie- en verificatiebeleid van uw organisatie.

- Controleresultaten weergeven van virtuele Linux-machines waarvoor de machtigingen van het passwd-bestand niet zijn ingesteld op 0644
- Controleresultaten weergeven van virtuele Linux-machines met accounts zonder wachtwoorden
- Controleresultaten weergeven van virtuele Windows-machines waarop wachtwoorden niet worden opgeslagen met omkeerbare versleuteling
- Vereisten implementeren om Linux-VM's te controleren waarvoor de machtigingen van het passwd-bestand niet zijn ingesteld op 0644
- Vereisten implementeren om Linux-VM's met accounts zonder wachtwoorden te controleren
- Vereisten implementeren om virtuele Windows-machines te controleren waarop wachtwoorden niet worden opgeslagen met omkeerbare versleuteling

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Authenticator Management | Verificatie op basis van wachtwoorden

Met deze blauw druk kunt u sterke wachtwoorden afdwingen door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee virtuele Windows-machines worden gecontroleerd die geen vereisten voor minimale sterkte en andere wachtwoordvereisten afdwingen. Wanneer u op de hoogte bent van virtuele machines die in strijd zijn met het beleid voor wachtwoordsterkte, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle gebruikersaccounts van de virtuele machines voldoen aan het wachtwoordbeleid van uw organisatie.

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

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) Melden van incidenten | Beveiligingsproblemen met betrekking tot incidenten

Deze blauwdruk bevat beleidsdefinities waarmee u records kunt controleren met evaluatie van beveiligingsproblemen op virtuele machines, virtuele-machineschaalsets en SQL-servers. Deze inzichten bieden realtime informatie over de beveiligingsstatus van uw geïmplementeerde resources en kunnen u helpen bij het rangschikken van herstelacties.

- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Vulnerability Scanning

Met deze blauwdruk kunt u beveiligingsproblemen met informatiesystemen beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op beveiligingsproblemen met het besturingssysteem, SQL en virtuele machines in Azure Security Center.
Azure Security Center biedt rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources. Met deze blauw druk worden ook beleidsdefinities toegewezen die Advanced Data Security controleren en afdwingen op SQL-servers. In Advanced Data Security is de evaluatie van beveiligingsproblemen en Advanced Threat Protection-functies opgenomen, waarmee u meer inzicht krijgt in beveiligingsproblemen in de door u geïmplementeerde resources.

- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Denial of Service Protection

De DDoS-standaardlaag (DDoS: Distributed Denial of service) van Azure biedt extra functies en mogelijkheden voor risicobeperking via de Basic-servicelaag. Deze aanvullende functies omvatten Azure Monitor-integratie en de mogelijkheid om risicobeperkingsrapporten te bekijken na aanvallen. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen die controleert of de DDoS standaardlaag is ingeschakeld. Wanneer u inzicht hebt in het verschil in functionaliteit tussen de servicelagen, kunt u de beste oplossing selecteren voor DDos-beveiligingen voor uw Azure-omgeving.

- Azure DDoS-beveiligingsstandaard moet zijn ingeschakeld

## <a name="sc-7-boundary-protection"></a>SC-7 Boundary Protection

Met deze blauwdruk kunt u de systeemgrens beheren en bepalen door een [Azure Policy](../../../policy/overview.md)-definitie toe te wijzen die controleert op aanbevelingen voor de beveiliging van netwerkbeveiligings groepen in Azure Security Center. Azure Security Center analyseert verkeerspatronen van virtuele machines die vanaf het internet toegankelijk zijn, en formuleert aanbevelingen voor netwerkbeveiligingsgroepsregels die de mogelijkheid op aanvallen beperken Daarnaast wordt met deze blauwdruk ook beleidsdefinities toegewezen waarmee onbeveiligde eindpunten, toepassingen en opslagaccounts worden bewaakt. Eindpunten en toepassingen die niet zijn beveiligd door een firewall, en opslagaccounts met onbeperkte toegang, kunnen onbedoeld toegang verlenen tot gegevens in het informatiesysteem.

- Toegang via een eindpunt gericht op internet moet worden beperkt
- Netwerktoegang tot opslagaccounts moet zijn beperkt

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Boundary Protection | Toegangspunten

Just-In-Time-VM-toegang vergrendelt binnenkomend verkeer naar uw Azure-VM's, zodat u minder kwetsbaar bent voor aanvallen maar tegelijkertijd eenvoudig toegang wordt geboden om verbinding met VM's te kunnen maken wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u het aantal externe verbindingen met uw resources in Azure beperken. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Boundary Protection | Externe telecommunicatieservices

Just-In-Time-VM-toegang vergrendelt binnenkomend verkeer naar uw Azure-VM's, zodat u minder kwetsbaar bent voor aanvallen maar tegelijkertijd eenvoudig toegang wordt geboden om verbinding met VM's te kunnen maken wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u uitzonderingen beheren voor uw verkeersstroombeleid door de processen voor toegangsaanvragen en -goedkeuring te vergemakkelijken. Met deze blauwdruk wordt een [Azure Policy](../../../policy/overview.md)-definitie toegewezen waarmee u virtuele machines kunt bewaken die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Transmission Confidentiality and Integrity | Cryptografische of alternatieve fysieke beveiliging

Met deze blauwdruk kunt u de vertrouwelijkheid en integriteit van verzonden informatie beschermen door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee u het cryptografische mechanisme kunt controleren dat is geïmplementeerd voor communicatieprotocollen. Zorg ervoor dat de communicatie op de juiste wijze wordt versleuteld, zodat u aan de vereisten van uw organisatie kunt voldoen of informatie kunt beschermen tegen onbevoegde openbaarmaking en wijziging.

- API-app mag alleen toegankelijk zijn via HTTPS
- Controleresultaten weergeven van Windows-webservers die geen veilige communicatieprotocollen gebruiken
- Vereisten implementeren om Windows-webservers te controleren waarvoor geen veilige communicatieprotocollen worden gebruikt
- Functie-app mag alleen toegankelijk zijn via HTTPS
- Alleen beveiligde verbindingen met uw Azure Cache voor Redis moeten zijn ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Webtoepassing mag alleen toegankelijk zijn via HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Protection of Information at Rest | Cryptografische beveiliging

Met deze blauwdruk kunt u uw beleid afdwingen voor het gebruik van cryptografische beheeropties voor het beveiligen van inactieve informatie door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die specifieke cryptografische beheeropties afdwingen en het gebruik van zwakke cryptografische instellingen controleren. Wanneer u weet waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat resources worden geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. Met name de beleidsdefinities die door deze blauwdrukken worden toegewezen, vereisen versleuteling voor data lake-opslagaccounts en transparante gegevensversleuteling voor SQL-databases, en controleren op ontbrekende versleuteling voor SQL-databases, schijven van virtuele machines en accountvariabelen voor automatisering.

- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Schijfversleuteling moet worden toegepast op virtuele machines
- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld

## <a name="si-2-flaw-remediation"></a>SI-2 Flaw Remediation

Met deze blauwdruk kunt u fouten in informatiesystemen beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op ontbrekende systeemupdates, beveiligingsproblemen met het besturingssysteem, SQL en virtuele machines in Azure Security Center. Azure Security Center biedt rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources. Met deze blauwdruk wordt ook een beleidsdefinitie toegewezen die ervoor zorgt dat er patches op het besturingssysteem worden uitgevoerd voor virtuele-machineschaalsets.

- Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd
- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>SI-02 (06) Foutherstel | Eerdere versies van software/firmware verwijderen

Met deze blauwdruk worden beleidsdefinities toegewezen waarmee u ervoor kunt zorgen dat toepassingen de nieuwste versie van HTTP, Java, PHP, Python en TLS gebruiken. Met deze blauwdruk wordt ook een beleidsdefinitie toegewezen die ervoor zorgt dat Kubernetes-services worden bijgewerkt naar een niet-kwetsbare versie.

- Controleren of de HTTP-versie de meest recente is, als deze wordt gebruikt om de API-app uit te voeren
- Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de functie-app te openen
- Controleren of de HTTP-versie de meest recente is, als deze wordt gebruikt om de web-app te openen
- Controleren of de Java-versie de meest recente is, als deze wordt gebruikt als onderdeel van de API-app
- Zorg ervoor dat de nieuwste versie van Java wordt gebruikt als onderdeel van de functie-app
- Controleren of de Java-versie de meest recente is, als deze wordt gebruikt als onderdeel van de web-app
- Controleren of de PHP-versie de meest recente is, als deze wordt gebruikt als onderdeel van de API-app
- Controleren of de PHP-versie de meest recente is, als deze wordt gebruikt als onderdeel van de web-app
- Controleren of de Python-versie de meest recente is, als deze wordt gebruikt als onderdeel van de API-app
- Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de functie-app
- Controleren of de Python-versie de meest recente is, als deze wordt gebruikt als onderdeel van de web-app
- De nieuwste TLS-versie moet worden gebruikt in uw API-app
- De nieuwste TLS-versie moet worden gebruikt in uw functie-app
- De nieuwste TLS-versie moet worden gebruikt in uw web-app
- Kubernetes-services moeten worden geüpgraded naar een niet-kwetsbare Kubernetes-versie

## <a name="si-3-malicious-code-protection"></a>SI-3 Malicious Code Protection

Met deze blauwdruk kunt u Endpoint Protection beheren, met inbegrip van beveiliging tegen schadelijke code, door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in Azure Security Center en de Microsoft-antimalware-oplossing afdwingen op virtuele Windows-machines.

- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Ontbrekende Endpoint Protection bewaken in Azure Security Center
- Microsoft IaaSAntimalware-uitbreiding moet zijn geïmplementeerd op Windows-servers

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Malicious Code Protection | Centraal beheer

Met deze blauwdruk kunt u Endpoint Protection beheren, met inbegrip van beveiliging tegen schadelijke code, door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in Azure Security Center. Azure Security Center biedt gecentraliseerd beheer en rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources.

- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Ontbrekende Endpoint Protection bewaken in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 Information System Monitoring

Met deze blauwdruk kunt u uw systeem controleren door logboekregistraties en gegevensbeveiliging in Azure-resources te controleren en af te dwingen. Met name wordt door de toegewezen beleidsregels de implementatie gecontroleerd en afgedwongen van de Log Analytics-agent, en verbeterde beveiligingsinstellingen voor SQL-databases, opslagaccounts en netwerkbronnen. Deze mogelijkheden kunnen u helpen bij het detecteren van afwijkend gedrag en aanwijzingen van aanvallen, zodat u de juiste actie kunt ondernemen.

- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Implementatie van de Log Analytics-agent in virtuele-machineschaalsets controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- Log Analytics-werkruimte voor VM controleren - niet-overeenkomende items rapporteren
- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Network Watcher moet zijn ingeschakeld

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) Bewaking van informatiesystemen | Automatische waarschuwingen

Deze blauwdruk bevat beleidsdefinities waarmee u ervoor kunt zorgen dat meldingen over gegevensbeveiliging correct zijn ingeschakeld. Bovendien zorgt deze blauwdruk ervoor dat de prijscategorie Standaard is ingeschakeld voor Azure Security Center. De prijscategorie Standard voorziet in detectie van bedreigingen voor netwerken en virtuele machines en biedt bedreigingsinformatie, anomaliedetectie en gedragsanalyse in Azure Security Center.

- E-mailmelding aan abonnementseigenaar voor waarschuwingen met hoge urgentie moet zijn ingeschakeld
- Er moet een e-mailadres van de contactpersoon voor beveiliging worden opgeven voor uw abonnement
- Er moet een telefoonnummer van de contactpersoon voor beveiliging worden opgeven voor uw abonnement

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan verschillen in Azure Government en andere nationale clouds. 

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van beheeropties van blauwdruk DoD Impact Level 5 hebt bekeken, kunt u naar de volgende artikelen gaan voor informatie over de blauwdruk en de implementatie van dit voorbeeld:

> [!div class="nextstepaction"]
> [DoD Impact Level 5-blauwdruk - Overzicht](./index.md)
> [DoD Impact Level 5-blauwdruk - Implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).