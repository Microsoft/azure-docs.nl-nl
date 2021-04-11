---
title: Besturingselementen in het voorbeeld van het ISM-BEVEILIGING-blauwdrukvoorbeeld voor de Australische overheid
description: Toewijzing van beheeropties van het blauwdrukvoorbeeld van de ISM-BEVEILIGING van de Australische overheid. Elke beheeroptie wordt toegewezen aan een of meer Azure Policy-definities die helpen bij de evaluatie.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: ecb6a912d0b1f319022f6b7deff68e1495dccbca
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386446"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Toewijzing van beheeropties van het blauwdrukvoorbeeld van de ISM-BEVEILIGING van de Australische overheid

In het volgende artikel vindt u informatie over de manier waarop de Azure-blauwdrukken van de ISM-BEVEILIGING van de Australische overheid blauwdrukvoorbeelden worden toegewezen aan de beheeropties van ISM-BEVEILIGING. Zie [ISM-BEVEILIGD](https://www.cyber.gov.au/ism) voor meer informatie over de beheeropties.

De volgende toewijzingen zijn voor de beheeropties van **ISM-BEVEILIGD**. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens de **\[Preview\]: Beheeropties van ISM-BEVEILIGD van de Australische overheid controleren en specifieke VM-extensies implementeren ter ondersteuning van de controlevereisten** van het ingebouwd beleidsinitiatief.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Locatiebeperkingen

Met deze blauwdruk kunt u de locatie voor de implementatie van alle resources en resourcegroepen tot 'Australië - centraal', 'Australië - centraal2', 'Australië - oost' en 'Australië - zuidoost' door de volgende Azure-beleidsdefinities toe te wijzen:

- Toegestane locaties (is vastgelegd in 'Australië - centraal', 'Australië - centraal2', 'Australië - oost', en 'Australië - zuidoost')
- Toegestane locaties voor resourcegroepen (is vastgelegd in 'Australië - centraal', 'Australië - centraal2', 'Australië - oost', en 'Australië - zuidoost')

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Richtlijnen voor beveiliging medewerkers: toegang tot systemen en bijbehorende resources

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 Medewerkers met toegang tot een systeem en bijbehorende resources zijn uniek identificeerbaar

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 Standaardtoegang tot systemen, toepassingen en gegevensopslag is beperkt tot de vereisten die medewerkers nodig hebben om hun taken uit te voeren

- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 Bevoorrechte toegang tot systemen, toepassingen en gegevensopslag wordt gevalideerd bij de eerste aanvraag en wordt jaarlijks of frequenter opnieuw gevalideerd

- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 Bevoorrechte toegang tot systemen, toepassingen en gegevensopslag is beperkt tot de vereisten die medewerkers nodig hebben om hun taken uit te voeren

- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat
- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 Het gebruik van gedeelde gebruikersaccounts wordt strikt gecontroleerd en medewerkers die dergelijke accounts gebruiken, zijn uniek identificeerbaar

- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 Bevoorrechte gebruikers krijgen een speciaal bevoorrecht account toegewezen dat alleen wordt gebruikt voor taken die bevoorrechte toegang vereisen

- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 Toegang tot systemen, toepassingen en gegevensopslag wordt verwijderd of onderbroken op dezelfde dag dat medewerkers niet langer een rechtmatige vereiste hebben voor toegang

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 Wanneer medewerkers tijdelijke toegang tot een systeem krijgen, worden geldende beveiligingsmaatregelen ingesteld om hun toegang te beperken tot alleen de informatie die ze nodig hebben om hun taken uit te voeren

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Richtlijnen voor systeembeveiliging: beveiliging van het besturingssysteem

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 De nieuwste versie (N) of N-1-versie van een besturingssysteem wordt gebruikt voor standaardbesturingsomgevingen (SOE's)

- Er moeten systeemupdates op uw computers worden geïnstalleerd
- Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 Onnodige besturingssysteemaccounts, software, onderdelen, services en functionaliteit worden verwijderd of uitgeschakeld

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 er is een wittelijstoplossing voor toepassingen geïmplementeerd op alle servers om de uitvoering van uitvoerbare bestanden, softwarebibliotheken, scripts en installatieprogramma's te beperken tot een goedgekeurde set

- Besturingselementen voor adaptieve toepassingen om veilige toepassingen in de toegestane lijst op te nemen, moeten worden ingeschakeld op uw computers

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 Antivirussoftware is geïmplementeerd op werkstations en servers en geconfigureerd met: ingeschakelde detectie op basis van handtekeningen op hoog niveau, ingeschakelde heuristische detectie op hoog niveau, detectiehandtekeningen worden gecontroleerd op actualiteit en minstens dagelijks bijgewerkt, automatische en regelmatige scans zijn geconfigureerd voor alle vaste schijven en verwisselbare media

- Microsoft IaaSAntimalware-uitbreiding moet zijn geïmplementeerd op Windows-servers
- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Ontbrekende Endpoint Protection bewaken in Azure Security Center

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Richtlijnen voor systeembeveiliging: beveiliging van verificatie

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 Gebruikers worden geverifieerd voordat ze toegang krijgen tot een systeem en de bijbehorende resources

- Netwerktoegang tot opslagaccounts moet zijn beperkt
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie
- Controleresultaten weergeven van virtuele Linux-machines waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan
- Vereisten implementeren om virtuele Linux-machines te controleren waarvoor externe verbindingen met accounts zonder wachtwoorden zijn toegestaan
- Controleresultaten weergeven van virtuele Linux-machines met accounts zonder wachtwoorden
- Vereisten implementeren om Linux-VM's met accounts zonder wachtwoorden te controleren

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 Meervoudige verificatie wordt gebruikt voor verificatie van standaardgebruikers

- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 Meervoudige verificatie wordt gebruikt voor verificatie van alle bevoorrechte gebruikers en andere vertrouwensposities

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 Wachtwoordzinnen voor enkelvoudige verificatie bestaan uit minimaal 14 tekens met complexiteit, idealiter met vier willekeurige woorden

- Controleresultaten van configuraties voor Windows-VM's weergeven in 'Beveiligingsopties: Accountbeleid'
- Vereisten implementeren om configuraties voor Windows-VM's te controleren in 'Beveiligingsinstellingen: Accountbeleid'

## <a name="guidelines-for-system-management---system-administration"></a>Richtlijnen voor systeembeheer: systeembeheer

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 Meervoudige verificatie wordt gebruikt voor verificatie van gebruikers wanneer ze bevoorrechte acties uitvoeren

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 Beheerverkeer mag alleen afkomstig zijn uit netwerkzones die worden gebruikt om systemen en toepassingen te beheren

- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer
- Externe foutopsporing moet worden uitgeschakeld voor API-apps
- Externe foutopsporing moet worden uitgeschakeld voor Function-apps
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen

## <a name="guidelines-for-system-management---system-patching"></a>Richtlijnen voor systeembeheer: systeempatches

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 Kwetsbaarheden in de beveiliging van toepassingen en stuurprogramma's die zijn beoordeeld als extreme risico's worden gepatcht, bijgewerkt of verholpen binnen 48 uur na identificatie ervan door leveranciers, onafhankelijke derden, systeembeheerders of gebruikers

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op machines
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- De instellingen voor evaluatie van beveiligingsproblemen voor SQL Server moeten een e-mailadres bevatten voor het ontvangen van scanrapporten

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 Kwetsbaarheden in de beveiliging van toepassingen en stuurprogramma's die zijn beoordeeld als hoge risico's worden gepatcht, bijgewerkt of verholpen binnen twee weken na identificatie ervan door leveranciers, onafhankelijke derden, systeembeheerders of gebruikers

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- De instellingen voor evaluatie van beveiligingsproblemen voor SQL Server moeten een e-mailadres bevatten voor het ontvangen van scanrapporten

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 Kwetsbaarheden in de beveiliging van toepassingen en stuurprogramma's die zijn beoordeeld als gemiddelde of lage risico's worden gepatcht, bijgewerkt of verholpen binnen één maand na identificatie ervan door leveranciers, onafhankelijke derden, systeembeheerders of gebruikers

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- De instellingen voor evaluatie van beveiligingsproblemen voor SQL Server moeten een e-mailadres bevatten voor het ontvangen van scanrapporten

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 Kwetsbaarheden in de beveiliging van besturingssystemen en firmware die zijn beoordeeld als extreme risico's worden gepatcht, bijgewerkt of verholpen binnen 48 uur na identificatie ervan door leveranciers, onafhankelijke derden, systeembeheerders of gebruikers

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- De instellingen voor evaluatie van beveiligingsproblemen voor SQL Server moeten een e-mailadres bevatten voor het ontvangen van scanrapporten

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 Kwetsbaarheden in de beveiliging van besturingssystemen en firmware die zijn beoordeeld als hoge risico's worden gepatcht, bijgewerkt of verholpen binnen twee weken na identificatie ervan door leveranciers, onafhankelijke derden, systeembeheerders of gebruikers

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- De instellingen voor evaluatie van beveiligingsproblemen voor SQL Server moeten een e-mailadres bevatten voor het ontvangen van scanrapporten

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 Kwetsbaarheden in de beveiliging van besturingssystemen en firmware die zijn beoordeeld als gemiddelde of lage risico's worden gepatcht, bijgewerkt of verholpen binnen één maand na identificatie ervan door leveranciers, onafhankelijke derden, systeembeheerders of gebruikers

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- De instellingen voor evaluatie van beveiligingsproblemen voor SQL Server moeten een e-mailadres bevatten voor het ontvangen van scanrapporten

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Richtlijnen voor systeembeheer: gegevensback-up en -herstel

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 Back-ups van belangrijke gegevens, software en configuratie-instellingen worden minimaal dagelijks uitgevoerd

- Virtuele machines controleren waarop geen noodherstel is geconfigureerd

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Richtlijnen voor systeembewaking: logboekregistratie en controle

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 er wordt een centrale faciliteit voor logboekregistratie geïmplementeerd en systemen worden geconfigureerd om gebeurtenislogboeken zo snel mogelijk na de gebeurtenis op te slaan in de centrale faciliteit voor logboekregistratie

- Azure-abonnementen moeten een logboekprofiel voor het activiteitenlogboek hebben

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 De volgende gebeurtenissen worden vastgelegd voor besturingssystemen: toegang tot belangrijke gegevens en processen, vastlopen van toepassingen en eventuele foutberichten, pogingen tot gebruik van speciale bevoegdheden, wijzigingen in accounts, wijzigingen in beveiligingsbeleid, wijzigingen in systeemconfiguraties, DNS- (Domeinnaamsystemen) en HTTP-aanvragen (Hypertext Transfer Protocol), mislukte pogingen om toegang te krijgen tot gegevens en systeemresources, servicefouten en het opnieuw opstarten van services, starten en stoppen van systemen, overdracht van gegevens naar externe media, gebruikers- of groepsbeheer, gebruik van speciale bevoegdheden

- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- Implementatie van de Log Analytics-agent controleren in VMSS - VM-installatiekopie (besturingssysteem) onvermeld
- Log Analytics-werkruimte voor VM controleren - niet-overeenkomende items rapporteren
- Diagnostische instelling voor controleren

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 De volgende gebeurtenissen worden vastgelegd voor databases: toegang tot bijzonder belangrijke gegevens, toevoeging van nieuwe gebruikers, specifiek voor bevoorrechte gebruikers, query's met opmerkingen, query's met meerdere ingesloten query's, waarschuwingen of fouten van query's of databases, pogingen tot het uitbreiden van bevoegdheden, toegang die lukt of mislukt, wijzigingen in de databasestructuur, acties van de databasebeheerder, aan- en afmeldingen bij de database, wijzigingen van gegevens, gebruik van uitvoerbare opdrachten

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Diagnostische instelling voor controleren
- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Richtlijnen voor systeembewaking: beheer van beveiligingsproblemen

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 Voordat een systeem wordt geïmplementeerd, na een belangrijke wijziging in het systeem en ten minste jaarlijks of zoals aangegeven door de systeemeigenaar worden door voldoende bekwame medewerkers evaluaties van beveiligingsproblemen en penetratietesten uitgevoerd

- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen

## <a name="guidelines-for-database-systems-management---database-servers"></a>Richtlijnen voor beheer van databasesystemen: databaseservers

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 Harde schijven van databaseservers worden versleuteld met volledige schijfversleuteling

- Schijfversleuteling moet worden toegepast op virtuele machines
- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 Gegevens die worden gecommuniceerd tussen databaseservers en webtoepassingen worden versleuteld

- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Controleresultaten weergeven van Windows-webservers die geen veilige communicatieprotocollen gebruiken
- Vereisten implementeren om Windows-webservers te controleren waarvoor geen veilige communicatieprotocollen worden gebruikt

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Richtlijnen voor beheer van databasesystemen: software voor databasebeheersystemen

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 Standaardbeheerdersaccounts worden uitgeschakeld, hernoemd of de wachtwoordzinnen ervan worden gewijzigd

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 Databasebeheerders hebben unieke en identificeerbare accounts

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 Databasebeheerderaccounts worden niet gedeeld in verschillende databases

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 Databasebeheerderaccounts worden alleen gebruikt voor beheertaken en standaarddatabaseaccounts worden gebruikt voor interacties met de database voor algemene doeleinden

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 De toegang van een databasebeheerder wordt beperkt tot gedefinieerde rollen in plaats van accounts met standaardbeheermachtigingen of alle machtigingen

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Richtlijnen voor het gebruik van cryptografie: cryptografische basisbewerkingen

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 Versleutelingssoftware die wordt gebruikt voor inactieve gegevens implementeert volledige schijfversleuteling of gedeeltelijke versleuteling waarbij toegangsbeheer alleen schrijfbewerkingen naar de versleutelde partitie toestaat

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Richtlijnen voor het gebruik van cryptografie: beveiliging van de transportlaag

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 Alleen de nieuwste versie van TLS wordt gebruikt

- De nieuwste TLS-versie moet worden gebruikt in uw API-app
- De nieuwste TLS-versie moet worden gebruikt in uw web-app
- De nieuwste TLS-versie moet worden gebruikt in uw functie-app
- Vereisten implementeren om Windows-webservers te controleren waarvoor geen veilige communicatieprotocollen worden gebruikt
- Controleresultaten weergeven van Windows-webservers die geen veilige communicatieprotocollen gebruiken

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Richtlijnen voor gegevensoverdracht en het filteren van inhoud: het filteren van inhoud

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 Op alle inhoud worden antivirusscans met verschillende scanners uitgevoerd

- Microsoft IaaSAntimalware-uitbreiding moet zijn geïmplementeerd op Windows-servers
- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Ontbrekende Endpoint Protection bewaken in Azure Security Center

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Richtlijnen voor gegevensoverdracht en het filteren van inhoud: ontwikkeling van webtoepassingen

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 Inhoud van webtoepassingen wordt alleen aangeboden met HTTPS

- Functie-app mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 Er worden beveiligingsbesturingselementen op basis van webbrowsers geïmplementeerd voor webtoepassingen om zowel de webtoepassingen als hun gebruikers te beveiligen

- Gebruik van CORS mag er niet toe leiden dat elke resource toegang heeft tot uw webtoepassingen

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Richtlijnen voor netwerkbeheer: netwerkontwerp en -configuratie

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 Er worden besturingselementen voor netwerktoegang geïmplementeerd op netwerken om verbinding door ongeautoriseerde netwerkapparaten te voorkomen

- Netwerktoegang tot opslagaccounts moet zijn beperkt

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 Er wordt netwerwerktoegangsbeheer geïmplementeerd om verkeer tussen en binnen netwerksegmenten te beperken tot alleen die segmenten die vereist zijn voor zakelijke doeleinden

- Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen
- Netwerktoegang tot opslagaccounts moet zijn beperkt
- Aanbevelingen voor Adaptieve netwerkbeveiliging moeten worden toegepast op op internet gerichte virtuele machines

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Richtlijnen voor netwerkbeheer: continuïteit van services voor onlineservices

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 Beleid voor het voorkomen en herstellen van Denial of service-aanvallen wordt met serviceproviders besproken, in het bijzonder: hun capaciteit om Denial of serviceaanvallen te voorkomen, eventuele kosten voor klanten als gevolg van Denial of service-aanvallen, drempelwaarden voor het informeren van klanten of het uitschakelen van hun onlineservices tijdens Denial of service-aanvallen, vooraf goedgekeurde acties die kunnen worden ondernomen tijdens Denial of service-aanvallen, afspraken over Denial of service-aanvallen met upstream-providers om schadelijk verkeer zo snel mogelijk te blokkeren

- De DDoS Protection-standaard moet zijn ingeschakeld


> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan verschillen in Azure Government en andere nationale clouds. 

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

> [!div class="nextstepaction"]
> [ISM BEVEILIGING-blauwdruk - Overzicht](./index.md)
> [ISM BEVEILIGING-blauwdruk - Implementatiestappen](./deploy.md)

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
