---
title: Beheeropties voor UK OFFICIAL- en UK NHS-blauwdrukvoorbeelden
description: Toewijzing van beheeropties voor de UK OFFICIAL- en UK NHS-blauwdrukvoorbeelden. Elke beheeroptie wordt toegewezen aan een of meer Azure Policy-definities die helpen bij de evaluatie.
ms.date: 11/05/2020
ms.topic: sample
ms.openlocfilehash: 352ba30a21c638c68401e2f8e471096a777fbde9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009378"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Toewijzing van beheeropties voor de UK OFFICIAL- en UK NHS-blauwdrukvoorbeelden

In het volgende artikel wordt uitgelegd hoe de UK OFFICIAL- en UK NHS-blauwdrukvoorbeelden worden toegewezen aan de UK OFFICIAL- en UK NHS-beheeropties. Zie [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications) voor meer informatie over de beheeropties.

De volgende toewijzingen zijn naar de **UK OFFICIAL**- en **UK NHS**-beheeropties. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens het ingebouwde beleidsinitiatief **\[Preview\]UK OFFICIAL- en UK NHS-beheeropties controleren en specifieke VM-extensies implementeren ter ondersteuning van de controlevereisten**.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Beveiliging van gegevens tijdens de overdracht

Deze blauwdruk zorgt voor een beveiligde overdracht van gegevens met Azure-services door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee onbeveiligde verbindingen met opslagaccounts en Redis Cache worden gecontroleerd.

- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Controleresultaten weergeven van Windows-webservers die geen veilige communicatieprotocollen gebruiken
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- Functie-app mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS

## <a name="23-data-at-rest-protection"></a>2.3 Beveiliging van data-at-rest

Met deze blauwdruk kunt u uw beleid afdwingen voor het gebruik van cryptografische beheeropties door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die specifieke cryptografische beheeropties afdwingen en het gebruik van zwakke cryptografische instellingen controleren. Wanneer u weet waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat resources worden geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. Met name de beleidsregels die met deze blauwdruk worden toegewezen, vereisen versleuteling voor data lake-opslagaccounts; vereisen transparante gegevensversleuteling voor SQL-databases; controleren op ontbrekende versleuteling voor opslagaccounts, SQL-databases, schijven van virtuele machines en variabelen van Automation-accounts; controleren op onbeveiligde verbindingen met opslagaccounts en Redis Cache; controleren op zwakke versleuteling van wachtwoorden van virtuele machines; en controleren op niet-versleutelde Service Fabric-communicatie.

- Schijfversleuteling moet worden toegepast op virtuele machines
- Automation-accountvariabelen moeten worden versleuteld
- Voor Service Fabric-clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign
- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld
- Transparante gegevensversleuteling in SQL DB implementeren
- Versleuteling van Data Lake Store-accounts vereisen
- Toegestane locaties (is vastgelegd als 'UK SOUTH' en 'UK WEST')
- Toegestane locaties voor resourcegroepen (is vastgelegd als 'UK SOUTH' en 'UK WEST')

## <a name="52-vulnerability-management"></a>5.2 Beheer van beveiligingsproblemen

Met deze blauwdruk kunt u beveiligingsproblemen in informatiesystemen beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op ontbrekende, eindpuntbeveiliging, ontbrekende systeemupdates, beveiligingsproblemen met het besturingssysteem, SQL en virtuele machines. Deze inzichten bieden realtime informatie over de beveiligingsstatus van uw geïmplementeerde resources en kunnen u helpen bij het rangschikken van herstelacties.

- Ontbrekende Endpoint Protection bewaken in Azure Security Center
- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld voor uw met SQL beheerde exemplaar
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Advanced Data Security moet zijn ingeschakeld voor uw met SQL beheerde exemplaar
- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers

## <a name="53-protective-monitoring"></a>5.3 Bewaking van de beveiliging

Met deze blauwdruk kunt u informatiesysteemassets beveiligen door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die bewaking van de beveiliging bieden voor onbeperkte toegang, een lijst met toegestane activiteiten en bedreigingen.

- Netwerktoegang tot opslagaccounts moet zijn beperkt
- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten zijn ingeschakeld op uw computers
- Virtuele machines controleren waarop geen herstel na noodgeval is geconfigureerd
- Azure DDoS-beveiligingsstandaard moet zijn ingeschakeld
- Advanced Threat Protection-typen moeten ingesteld zijn op Alles in de Advanced Data Security-instellingen
- Advanced Threat Protection-typen moeten worden ingesteld op All in de Advanced Data Security-instellingen van SQL Server
- Detectie van bedreigingen implementeren op SQL-servers
- Microsoft IaaSAntimalware-standaarduitbreiding voor Windows Server implementeren

## <a name="9-secure-user-management"></a>9 Beveiligd gebruikersbeheer 

Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) helpt u te beheren wie toegang heeft tot Azure-resources.
Met behulp van de Azure-portal kunt u controleren wie toegang heeft tot Azure-resources en wat hun machtigingen zijn. Met deze blauwdruk kunt u toegangsrechten beperken en beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen voor de controle van externe accounts met eigenaars- en/of lees-/schrijfrechten en accounts met eigenaars-, lees- en/of schrijfrechten waarvoor geen meervoudige verificatie is ingeschakeld.

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="10-identity-and-authentication"></a>10 Identiteit en verificatie

Met deze blauwdruk kunt u toegangsrechten beperken en beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen voor de controle van externe accounts met eigenaars- en/of lees-/schrijfrechten en accounts met eigenaars-, lees- en/of schrijfrechten waarvoor geen meervoudige verificatie is ingeschakeld.

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement

Met deze blauwdruk worden Azure Policy-definities toegewezen om het gebruik van Azure Active Directory-verificatie voor SQL-servers en-Service Fabric te controleren. Met behulp van Azure Active Directory-verificatie maakt vereenvoudigd beheer van machtigingen en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services mogelijk.

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie

Met deze blauwdruk worden ook Azure Policy-definities toegewezen voor het controleren van accounts die prioriteit moeten krijgen om te worden beoordeeld, waaronder afgeschreven accounts en externe accounts. Zo nodig kunnen accounts voor aanmelding worden geblokkeerd (of verwijderd), waardoor de toegangsrechten tot Azure-resources onmiddellijk worden verwijderd.
Deze blauwdruk wijst twee Azure Policy-definities toe voor het controleren van afgeschreven accounts die voor verwijdering moeten worden aangemerkt.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

Met deze blauwdruk worden ook Azure Policy-definities toegewezen waarmee wordt gecontroleerd of de machtigingen voor het wachtwoordbestand voor Linux-VM's een waarschuwing geven als deze niet juist zijn ingesteld. Met deze opzet kunt u corrigerende maatregelen treffen om te garanderen dat geen misbruik van authenticators wordt gemaakt.

- Controleresultaten weergeven van virtuele Linux-machines waarvoor de machtigingen van het passwd-bestand niet zijn ingesteld op 0644

Met deze blauwdruk kunt u sterke wachtwoorden afdwingen door Azure Policy-definities toe te wijzen waarmee Windows-VM's worden gecontroleerd die geen vereisten voor minimale sterkte en andere wachtwoordvereisten afdwingen. Wanneer u op de hoogte bent van VM's die in strijd zijn met het beleid voor wachtwoordsterkte, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle gebruikersaccounts van de VM's voldoen aan dit beleid.

- Controleresultaten weergeven van virtuele Windows-machines waarop de instelling voor wachtwoordcomplexiteit niet is ingeschakeld
- Controleresultaten weergeven van virtuele Windows-machines waarvoor geen maximale wachtwoordduur van 70 dagen is ingesteld
- Controleresultaten weergeven van virtuele Windows-machines waarvoor geen minimale wachtwoordduur van 1 dag is ingesteld
- Controleresultaten weergeven van virtuele Windows-machines waarvoor de minimale wachtwoordlengte niet wordt beperkt tot 14 tekens
- Controleresultaten weergeven van virtuele Windows-machines waarvoor de voorgaande 24 wachtwoorden opnieuw kunnen worden gebruikt

Met deze blauwdruk kunt u ook de toegang tot Azure-resources beheren door Azure Policy-definities toe te wijzen. Met deze beleidsregels wordt het gebruik van resourcetypen en -configuraties gecontroleerd, waardoor de toegang tot resources kan worden uitgebreid. Dankzij meer inzicht in resources die in strijd zijn met deze beleidsregels kunt u corrigerende maatregelen treffen om te garanderen dat toegang tot Azure-resources wordt beperkt tot gemachtigde gebruikers.

- Controleresultaten weergeven van virtuele Linux-machines met accounts zonder wachtwoorden
- Controleresultaten weergeven van virtuele Linux-machines waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- VM's controleren die niet gebruikmaken van beheerde schijven

## <a name="11-external-interface-protection"></a>11 Externe interfacebeveiliging

Behalve het gebruik van meer dan 25 beleidsregels voor het juiste beveiligde gebruikersbeheer, kunt u met deze blauwdruk service-interfaces beveiligen tegen onbevoegde toegang door een [Azure Policy](../../../policy/overview.md)-definitie toe te wijzen waarmee onbeperkte opslagaccounts worden bewaakt.
Opslagaccounts met onbeperkte toegang kunnen onbedoeld toegang verlenen tot gegevens in het informatiesysteem. Deze blauwdruk wijst ook een beleid toe dat adaptieve toepassingsregelaars op virtuele machines mogelijk maakt.

- Netwerktoegang tot opslagaccounts moet zijn beperkt
- Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten zijn ingeschakeld op uw computers
- Toegang via een eindpunt gericht op internet moet worden beperkt
- Aanbevelingen voor Adaptieve netwerkbeveiliging moeten worden toegepast op op internet gerichte virtuele machines
- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer
- Externe foutopsporing moet worden uitgeschakeld voor Function-apps
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen
- Externe foutopsporing moet worden uitgeschakeld voor API-apps

## <a name="13-audit-information-for-users"></a>13 Controle-informatie voor gebruikers

Met deze blauwdruk kunt u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)definities toe te wijzen die logboekinstellingen op Azure-resources controleren.
Een toegewezen beleid controleert ook of de virtuele machines geen logboeken verzenden naar een opgegeven logboekanalysewerkruimte.

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Diagnostische instelling voor controleren
- \[Preview\]: Log Analytics-agent voor virtuele Linux-machines implementeren
- \[Preview\]: Log Analytics-agent voor Windows-VM's implementeren
- Network Watcher implementeren wanneer er virtuele netwerken worden gemaakt


## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van beheeropties voor de UK OFFICIAL- en UK NHS-blauwdrukken hebt bekeken, raadpleegt u de volgende artikelen voor informatie over het overzicht en hoe u dit voorbeeld kunt implementeren:

> [!div class="nextstepaction"]
> [UK OFFICIAL- en UK NHS-blauwdrukken - overzicht](./index.md)
> [UK OFFICIAL- en UK NHS-blauwdrukken - implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
