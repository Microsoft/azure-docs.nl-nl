---
title: Beheer van het blauwdrukvoorbeeld voor Media
description: Toewijzing van beheeropties voor het blauwdrukvoorbeeld voor Media. Elke beheeroptie wordt toegewezen aan een of meer Azure Policy-definities die helpen bij de evaluatie.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: e1356acfd7b6b9693322000077b221781b58681f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98035033"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Toewijzing van beheeropties voor het blauwdrukvoorbeeld voor Media

Het volgende artikel bevat informatie over de manier waarop het Azure Blueprints Media-blauwdrukvoorbeeld wordt toegewezen aan de Media-beheeropties. Zie [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) voor meer informatie over de beheeropties.

De volgende toewijzingen zijn voor de **Media**-beheeropties. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens de **\[Preview\]: Media-beheeropties controleren** ingebouwd beleidsinitiatief.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Toegangsbeheer

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1: zorg dat er geen toegangssleutel voor de hoofdmap bestaat

- \[Preview\]: Vereisten implementeren om Windows VM's te controleren die niet de opgegeven certificaten in Vertrouwde basiscertificeringsinstanties bevatten

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2: wachtwoorden, pincodes en tokens moeten worden beveiligd

- \[Preview\]: Vereisten implementeren om virtuele Windows-machines te controleren waarvoor de minimale wachtwoordlengte niet wordt beperkt tot 14 tekens

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8: toegang tot gedeeld account is niet toegestaan

- Alle autorisatieregels met uitzondering van RootManageSharedAccessKey moeten worden verwijderd uit Service Bus-naamruimte

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9: het systeem moet de toegang tot gemachtigde gebruikers beperken.

- Netwerktoegang tot opslagaccounts moet zijn beperkt

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14: het systeem moet toegangsrechten afdwingen.

- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Toewijzing van gebruikersrechten’

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15: onbevoegde toegang tot informatie of functies die relevant zijn voor beveiliging voorkomen.

- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Systeeminstellingen’

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21: scheiding van taken moet worden afgedwongen via de juiste toewijzing van rollen.

- [Preview\]: Op rollen gebaseerd toegangsbeheer (RBAC) moet worden gebruikt voor Kubernetes Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40: zorg ervoor dat systemen niet tegelijkertijd verbinding maken met een vertrouwde en niet-vertrouwde netwerken.

- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Beveiligingsopties - Netwerktoegang’

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC-1.43: externe toegang voor niet-werknemers moet worden beperkt om alleen toegang te verlenen aan specifiek goedgekeurde informatiesystemen

- \[Preview\]: Controleresultaten weergeven van virtuele Linux-machines waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50: registreer beveiligingsgebeurtenissen voor alle onderdelen van het informatiesysteem.

- Diagnostische logboeken in Logic Apps moeten zijn ingeschakeld

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54: zorg dat meervoudige verificatie (MFA) is ingeschakeld voor alle cloudconsole-gebruikers.

- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- Schakel meervoudige verificatie (MFA) in voor alle abonnementsaccounts met schrijfmachtigingen om te voorkomen dat er inbreuk wordt gepleegd op accounts of resources.

## <a name="auditing--logging"></a>Controleren en registreren

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1: geslaagde en mislukte gebeurtenissen moeten worden geregistreerd.

- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2.16: netwerkapparaten/-exemplaren moeten alle gebeurtenissen registreren die zijn geclassificeerd als kritieke beveiligingsgebeurtenis door dat netwerkapparaat/-exemplaar (ELB’s, firewalls voor webtoepassingen, enzovoort).

- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Accounts’

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17: servers/exemplaren moeten alle gebeurtenissen registreren die zijn geclassificeerd als kritieke beveiligingsgebeurtenis door die server/exemplaar

- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Accounts’

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19: domeingebeurtenissen moeten alle gebeurtenissen registreren die zijn geclassificeerd als een kritieke of belangrijke beveiligingsgebeurtenis door de domeinbeheersoftware

- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Accounts’
- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Beveiligingsopties - Microsoft-netwerkclient’

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20: domeingebeurtenissen moeten alle gebeurtenissen registreren die zijn geclassificeerd als een kritieke beveiligingsgebeurtenis door de besturingselementen van de domeinbeveiliging

- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Accounts’

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21: domeingebeurtenissen moeten de toegang of wijzigingen aan het domeinlogboek registreren

- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Herstelconsole’

## <a name="cryptographic-controls"></a>Cryptografische besturingselementen

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2: toepassingen en systemen moeten actuele cryptografische oplossingen gebruiken voor het beveiligen van gegevens.

- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld
- Transparante gegevensversleuteling moet zijn ingeschakeld om data-at-rest te beveiligen en te voldoen aan de nalevingsvereisten

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5: digitale certificaten moeten worden ondertekend door een goedgekeurde certificeringsinstantie.

- \[Preview\]: Controleresultaten weergeven van Windows-VM's die certificaten bevatten die binnen het opgegeven aantal dagen verlopen

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6: digitale certificaten moeten uniek worden toegewezen aan een gebruiker of apparaat.

- \[Preview\]: Vereisten implementeren om Windows-VM's te controleren die certificaten bevatten die binnen het opgegeven aantal dagen verlopen

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7: cryptografisch materiaal moet worden opgeslagen om het ontsleutelen van de records mogelijk te maken gedurende de periode waarin de records worden bewaard.

- Schijfversleuteling moet worden toegepast op virtuele machines
- Virtuele machines zonder ingeschakelde schijfversleuteling worden als aanbevelingen bewaakt door Azure Security Center

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8: geheime en persoonlijke sleutels moeten veilig worden opgeslagen.

- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld
- Transparante gegevensversleuteling moet zijn ingeschakeld om data-at-rest te beveiligen en te voldoen aan de nalevingsvereisten

## <a name="change--config-management"></a>Beheer van wijzigingen en configuratie

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2: alleen geautoriseerde gebruikers kunnen goedgekeurde wijzigingen aan het systeem implementeren.

- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden als aanbevelingen bewaakt door Azure Security Center

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12: houd een up-to-date, volledige, accurate en gemakkelijk beschikbare basislijnconfiguratie van het informatiesysteem bij.

- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden als aanbevelingen bewaakt door Azure Security Center

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13: gebruik automatische hulpprogramma's voor het onderhouden van een basislijnconfiguratie van het informatiesysteem.

- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden als aanbevelingen bewaakt door Azure Security Center

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14: overbodige en/of niet-veilige functies, poorten, protocollen en services identificeren en uitschakelen.

- Doorsturen via IP moet in netwerkinterfaces worden uitgeschakeld
- \[Preview\]: Doorsturen via IP op uw virtuele machine moet zijn uitgeschakeld

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19: bewaak wijzigingen in de beveiligingsconfiguratie-instellingen.

- Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22: zorg ervoor dat alleen geautoriseerde software en updates worden geïnstalleerd op bedrijfssystemen.

- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden als aanbevelingen bewaakt door Azure Security Center

## <a name="identity--authentication"></a>Identiteit en verificatie

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1: gebruikersaccounts moeten uniek worden toegewezen aan individuen voor toegang tot informatie die niet openbaar is. Account-id's moeten worden samengesteld met behulp van een gestandaardiseerde logische indeling.

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement om onbewaakte toegang te voorkomen.

## <a name="network-security"></a>Netwerkbeveiliging

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2: toegang tot de beheerfunctionaliteit voor netwerkapparaten is beperkt tot gemachtigde gebruikers.

- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Beveiligingsopties - Netwerktoegang’

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3: alle netwerkapparaten moeten worden geconfigureerd met behulp van de veiligste configuraties.

- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Beveiligingsopties - Netwerktoegang’

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5: alle netwerkverbindingen met een systeem via een firewall moeten regelmatig worden goedgekeurd en gecontroleerd.

- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Eigenschappen van Windows Firewall’

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7: de juiste besturingselementen moeten aanwezig zijn op elke grens tussen een vertrouwd netwerk en een niet-vertrouwd of openbaar netwerk.

- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Eigenschappen van Windows Firewall’

## <a name="security-planning"></a>Beveiliging plannen

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3: bedreigingen moeten worden geïdentificeerd die een negatieve invloed kunnen hebben op de vertrouwelijkheid, integriteit of beschikbaarheid van bedrijfsgegevens en -inhoud, samen met de kans dat deze voorkomen.

- Advanced Threat Protection-typen moeten zijn ingesteld op Alles bij de geavanceerde instellingen voor gegevensbeveiliging van het beheerde SQL-exemplaar

### <a name="security-continuity"></a>Beveiligingscontinuïteit

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5: gegevens in langetermijnopslag moeten toegankelijk zijn tijdens de bewaartermijn en worden beschermd tegen prestatievermindering van media en wijzigingen aan technologie.

- SQL-servers moeten worden geconfigureerd met een bewaarperiode voor controleren van meer dan negentig dagen.
- Controle van SQL-servers die zijn geconfigureerd met een bewaarperiode voor controleren van minder dan 90 dagen.

## <a name="system-integrity"></a>Systeemintegriteit

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3: alleen geautoriseerd personeel mag netwerk- en gebruikersactiviteiten bewaken.

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- Bewaken van de scanresultaten en aanbevelingen van de beveiligingsevaluatie over het herstellen van beveiligingsproblemen in een database.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4: internetgerichte systemen moeten beschikken over inbraakdetectie.

- Detectie van bedreigingen implementeren op SQL-servers

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13: gestandaardiseerde, centraal beheerde antimalware-software moet worden geïmplementeerd in het hele bedrijf.

- Microsoft IaaSAntimalware-standaarduitbreiding voor Windows Server implementeren

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14: antimalware-software moet computers en media minimaal wekelijks scannen.

- Microsoft IaaSAntimalware-standaarduitbreiding voor Windows Server implementeren

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4: zorg ervoor dat toepassingen maandelijks worden gescand op beveiligingsproblemen.

- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Controleer op beveiligingsproblemen van besturingssystemen op uw virtuele-machineschaalsets om de schaalsets te beveiligen tegen aanvallen.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5: zorg ervoor dat beveiligingsproblemen worden geïdentificeerd, gekoppeld aan bedreigingen en worden beoordeeld op risico.

- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Controleer op beveiligingsproblemen van besturingssystemen op uw virtuele-machineschaalsets om de schaalsets te beveiligen tegen aanvallen.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6: zorg ervoor dat geïdentificeerde beveiligingsproblemen binnen een onderling overeengekomen tijdsbestek worden opgelost.

- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Controleer op beveiligingsproblemen van besturingssystemen op uw virtuele-machineschaalsets om de schaalsets te beveiligen tegen aanvallen.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7: toegang tot en gebruik van systemen voor beveiligingsbeheer moet worden beperkt tot geautoriseerd personeel.

- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Controleer op beveiligingsproblemen van besturingssystemen op uw virtuele-machineschaalsets om de schaalsets te beveiligen tegen aanvallen.

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan verschillen in Azure Government en andere nationale clouds. 

## <a name="next-steps"></a>Volgende stappen

U hebt de toewijzing van beheeropties van het Media-blauwdrukvoorbeeld gelezen. Lees nu de volgende artikelen voor meer informatie over het overzicht en het implementeren van dit voorbeeld:

> [!div class="next step action"]
> [Blauwdruk voor Media - Overzicht](./control-mapping.md)
> [Blauwdruk voor Media - Implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
