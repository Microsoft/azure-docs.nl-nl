---
title: Beheeropties van het blauwdrukvoorbeeld voor PCI-DSS v3.2.1
description: Beheer de toewijzing van het blauwdrukvoorbeeld voor de Payment Card Industry Data Security Standard v3.2.1 aan Azure Policy en Azure RBAC.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 9b1cf7702b627ae073f0172dde4694060004cb3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98045152"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Toewijzing van het blauwdrukvoorbeeld voor PCI-DSS v3.2.1 beheren

Het volgende artikel bevat informatie over de manier waarop het Azure Blueprints PCI-DSS v3.2.1-blauwdrukvoorbeeld wordt toegewezen aan de PCI-DSS v3.2.1-beheeropties. Zie [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf) voor meer informatie over de beheeropties.

De volgende toewijzingen zijn voor de **PCI-DSS v3.2.1:2018**-beheeropties. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Selecteer het ingebouwde beleidsinitiatief **PCI v3.2.1:2018**.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 en 1.3.4 Boundary Protection

Met deze blauwdruk kunt u netwerken beheren door een [Azure Policy](../../../policy/overview.md)-definitie toe te wijzen waarmee netwerkbeveiligingsgroepen met te ruime regels worden bewaakt. Regels die te ruim zijn, kunnen de oorzaak zijn van onbedoelde netwerktoegang en moeten worden gecontroleerd. Met deze blauwdruk worden ook Azure Policy-definities toegewezen waarmee onbeveiligde eindpunten, toepassingen en opslagaccounts worden bewaakt. Eindpunten en toepassingen die niet zijn beveiligd door een firewall, en opslagaccounts met onbeperkte toegang, kunnen onbedoeld toegang verlenen tot gegevens in het informatiesysteem.

- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Toegang via een eindpunt gericht op internet moet worden beperkt

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h en 6.5.3 cryptografische beveiliging

Met deze blauwdruk kunt u uw beleid afdwingen met het gebruik van cryptografische beheeropties door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die specifieke cryptografische beheeropties afdwingen en het gebruik van zwakke cryptografische instellingen controleren. Wanneer u weet waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat resources worden geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. Met name de beleidsregels die door deze blauwdrukken worden toegewezen, vereisen transparante gegevensversleuteling voor SQL-databases, controleren op ontbrekende versleuteling voor opslagaccounts, en accountvariabelen voor automatisering. Er zijn ook beleidsregels voor het controleren van onbeveiligde verbindingen met opslagaccounts, functie-apps, WebApp, API Apps en Redis Cache en het controleren van niet-versleutelde Service Fabric-communicatie.

- Functie-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS
- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld
- Schijfversleuteling moet worden toegepast op virtuele machines
- Automation-accountvariabelen moeten worden versleuteld
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Voor Service Fabric-clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign
- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld
- Transparante gegevensversleuteling in SQL DB implementeren

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 en 11.2.1 scannen op kwetsbaarheden en systeemupdates

Met deze blauwdruk kunt u beveiligingsproblemen in informatiesystemen beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op ontbrekende systeemupdates, beveiligingsproblemen met het besturingssysteem, SQL en virtuele machines in Azure Security Center. Azure Security Center biedt rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources.

- Ontbrekende Endpoint Protection bewaken in Azure Security Center
- Microsoft IaaSAntimalware-standaarduitbreiding voor Windows Server implementeren
- Detectie van bedreigingen implementeren op SQL-servers
- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 en 7.1.3 Separation of Duties

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er daarentegen te veel eigenaars van Azure-abonnementen zijn, is er een grotere kans op schendingen via een account van een gecompromitteerde eigenaar. Met deze blauwdruk kunt u het juiste aantal eigenaars van Azure-abonnementen bijhouden door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die het aantal eigenaren van Azure-abonnementen controleren. Met het beheer van eigenaarsmachtigingen voor abonnementen kunt u een juiste scheiding van taken implementeren.

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a en 8.3.1.b Beheer van bevoegde toegangsrechten

Met deze blauwdruk kunt u rechten voor bevoegde toegang beperken en beheren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen voor de controle van externe accounts met eigenaars-, schrijf en/of leesrechten en werknemersaccounts met eigenaars- en/of schrijfrechten waarvoor geen meervoudige verificatie is ingeschakeld. In Azure wordt op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) geïmplementeerd om te beheren wie toegang heeft tot Azure-resources. Wanneer u weet waar aangepaste Azure RBAC-regels zijn geïmplementeerd, kan dit u helpen om te controleren of deze nodig zijn en of deze correct zijn geïmplementeerd, omdat aangepaste Azure RBAC-regels foutgevoelig zijn. Met deze blauwdruk wordt ook [Azure Policy](../../../policy/overview.md)-definities toegewezen om het gebruik van Azure Active Directory-verificatie voor SQL-servers en te controleren. Met behulp van Azure Active Directory-verificatie maakt vereenvoudigd beheer van machtigingen en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services mogelijk.  
cloudservices moet worden gebruikt.
 
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement
- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers
- Het gebruik van aangepaste RBAC-regels controleren

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 en 8.1.5 Least Privilege en controle van gebruikerstoegangsrechten

Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) helpt u te beheren wie toegang heeft tot Azure-resources. Met behulp van de Azure-portal kunt u controleren wie toegang heeft tot Azure-resources en wat hun machtigingen zijn. Met deze blauwdruk worden [Azure Policy](../../../policy/overview.md)-definities toegewezen voor het controleren van accounts die prioriteit moeten krijgen om te worden beoordeeld, waaronder afgeschreven accounts en externe accounts met verhoogde rechten.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Verwijdering of aanpassing van toegangsrechten

Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) helpt u te beheren wie toegang heeft tot Azure-resources. Met behulp van Azure Active Directory en Azure RBAC kunt u gebruikersrollen bijwerken om wijzigingen in de organisatie weer te geven. Zo nodig kunnen accounts voor aanmelding worden geblokkeerd (of verwijderd), waardoor de toegangsrechten tot Azure-resources onmiddellijk worden verwijderd. Deze blauwdruk wijst [Azure Policy](../../../policy/overview.md)-definities toe voor het controleren van afgeschreven accounts die voor verwijdering moeten worden aangemerkt.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b en 8.2.5 Verificatie op basis van een wachtwoord

Met deze blauwdruk kunt u sterke wachtwoorden afdwingen door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee Windows-VM's worden gecontroleerd die geen vereisten voor minimale sterkte en andere wachtwoordvereisten afdwingen. Wanneer u op de hoogte bent van VM's die in strijd zijn met het beleid voor wachtwoordsterkte, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle gebruikersaccounts van de VM's voldoen aan dit beleid.

- \[Preview\]: Windows-VM's controleren waarvoor geen maximale wachtwoordduur van 70 dagen is ingesteld
- \[Preview\]: Vereisten implementeren om Windows-VM's te controleren waarvoor geen maximale wachtwoordduur van 70 dagen is ingesteld
- \[Preview\]: Windows-VM's controleren waarvoor de minimale wachtwoordlengte niet wordt beperkt tot 14 tekens
- \[Preview\]: Vereisten implementeren om Windows-VM's te controleren waarvoor de minimale wachtwoordlengte niet wordt beperkt tot 14 tekens
- \[Preview\]: Windows-VM's controleren waarvoor de voorgaande 24 wachtwoorden opnieuw kunnen worden gebruikt
- \[Preview\]: Vereisten implementeren om Windows-VM's te controleren waarvoor de voorgaande 24 wachtwoorden opnieuw kunnen worden gebruikt

## <a name="103-and-1054-audit-generation"></a>10.3 en 10.5.4 Controlegeneratie

Met deze blauwdruk kunt u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee u logboekinstellingen op Azure-resources kunt controleren.
Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd in Azure-resources. Azure-logboeken zijn afhankelijk van gesynchroniseerde interne klokken voor het maken van een tijdgebonden record met gebeurtenissen in resources.

- De controle moet zijn ingeschakeld in de instellingen voor geavanceerde gegevensbeveiliging in SQL Server
- Diagnostische instelling voor controleren
- Instellingen voor controle op SQL-serverniveau controleren
- Controle op SQL-servers implementeren
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources

## <a name="1236-and-1237-information-security"></a>12.3.6 en 12.3.7 Informatiebeveiliging

Met deze blauwdruk kunt u uw netwerk beheren en controleren door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee de geaccepteerde netwerklocaties en de goedgekeurde bedrijfsproducten worden gecontroleerd die voor de omgeving zijn toegestaan. Deze kunnen door elk bedrijf worden aangepast via de beleidsparameters in elk van deze beleidsregels.

- Toegestane locaties
- Toegestane locaties voor resourcegroepen

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van beheeropties van PCI-DSS v3.2.1-blauwdruk hebt bekeken, kunt u naar de volgende artikelen gaan voor informatie over het overzicht en de implementatie van dit voorbeeld:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1-blauwdruk - Overzicht](./index.md)
> [PCI-DSS v3.2.1-blauwdruk - Stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).