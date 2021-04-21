---
title: Door de klant beheerde TDE (Transparent Data Encryption)
description: Bring Your Own Key (BYOK) voor Transparent Data Encryption (TDE) met Azure Key Vault voor SQL Database en Azure Synapse Analytics. TDE met BYOK-overzicht, voordelen, hoe het werkt, overwegingen en aanbevelingen.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/01/2021
ms.openlocfilehash: b3403558cbc07d152bbae7e901464a8aa4a8e4d2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812764"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL Transparent Data Encryption met door de klant beheerde sleutels
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Transparent Data Encryption [(TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) met door de klant beheerde sleutel maakt Bring Your Own Key(BYOK)-scenario voor data-at-rest-beveiliging mogelijk en kunnen organisaties scheiding van taken implementeren in het beheer van sleutels en gegevens. Met door de klant beheerde transparante gegevensversleuteling is de klant verantwoordelijk voor en heeft deze volledige controle over het levenscyclusbeheer van sleutels (het maken, uploaden, rouleren, verwijderen van sleutels), machtigingen voor sleutelgebruik en het controleren van bewerkingen op sleutels.

In dit scenario is de sleutel die wordt gebruikt voor versleuteling van de DEK (Database Encryption Key), TDE-beveiliging genoemd, een door de klant beheerde asymmetrische sleutel die is opgeslagen in een door de klant beheerd [Azure Key Vault (AKV),](../../key-vault/general/security-features.md)een extern sleutelbeheersysteem in de cloud. Key Vault is uiterst beschikbare en schaalbare beveiligde opslag voor cryptografische RSA-sleutels, optioneel met fips 140-2 Niveau 2-gevalideerde hardwarebeveiligingsmodules (HMS's). Het biedt geen directe toegang tot een opgeslagen sleutel, maar biedt services voor versleuteling/ontsleuteling met behulp van de sleutel voor de geautoriseerde entiteiten. De sleutel kan worden gegenereerd door de sleutelkluis, worden geïmporteerd of overgedragen naar de sleutelkluis vanaf een [on-prem HSM-apparaat.](../../key-vault/keys/hsm-protected-keys.md)

Voor Azure SQL Database en Azure Synapse Analytics wordt de TDE-beveiliging ingesteld op serverniveau en overgenomen door alle versleutelde databases die aan die server zijn gekoppeld. Voor Azure SQL Managed Instance wordt de TDE-beveiliging ingesteld op exemplaarniveau en overgenomen door alle versleutelde databases op dat exemplaar. De term *server* verwijst in dit document zowel naar een server in SQL Database en Azure Synapse als naar een beheerd exemplaar in SQL Managed Instance dit document, tenzij anders vermeld.

> [!IMPORTANT]
> Voor degenen die gebruikmaken van een door de service beheerde TDE die door de klant beheerde TDE willen gaan gebruiken, blijven gegevens versleuteld tijdens het overschakelen en is er geen downtime en geen herversleuteling van de databasebestanden. Voor het overschakelen van een door de service beheerde sleutel naar een door de klant beheerde sleutel is alleen opnieuw versleuteling van de DEK vereist. Dit is een snelle en online bewerking.

> [!NOTE]
> <a id="doubleencryption"></a> Om klanten Azure SQL twee lagen versleuteling van data-at-rest te bieden, wordt infrastructuurversleuteling (met behulp van het AES-256-versleutelingsalgoritme) met door het platform beheerde sleutels uitgerold. Dit biedt een extra versleutelingslaag in rust, samen met TDE met door de klant beheerde sleutels, die al beschikbaar is. Voor Azure SQL Database managed instance worden alle databases, met inbegrip van de hoofddatabase en andere systeemdatabases, versleuteld wanneer infrastructuurversleuteling is ingeschakeld. Op dit moment moeten klanten toegang tot deze mogelijkheid aanvragen. Als u geïnteresseerd bent in deze mogelijkheid, kunt u contact opnemen met AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Voordelen van de door de klant beheerde TDE

Door de klant beheerde TDE biedt de volgende voordelen voor de klant:

- Volledige en gedetailleerde controle over het gebruik en beheer van de TDE-beveiliging;

- Transparantie van het gebruik van TDE-beveiliging;

- Mogelijkheid om scheiding van taken in het beheer van sleutels en gegevens binnen de organisatie te implementeren;

- Key Vault kan machtigingen voor sleuteltoegang intrekken om de versleutelde database ontoegankelijk te maken;

- Centraal beheer van sleutels in AKV;

- Meer vertrouwen van uw eindklanten, omdat AKV zodanig is ontworpen dat Microsoft geen versleutelingssleutels kan zien of extraheren;

## <a name="how-customer-managed-tde-works"></a>Hoe door de klant beheerde TDE werkt

![De door de klant beheerde TDE instellen en functioneren](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

De sleutelkluisbeheerder moet de volgende toegangsrechten aan de server verlenen met behulp van de unieke Azure Active Directory-identiteit (Azure AD) om de TDE-beveiliging te kunnen gebruiken die is opgeslagen in AKV voor de versleuteling van de DEK:

- **get** : voor het ophalen van het openbare gedeelte en de eigenschappen van de sleutel in de Key Vault

- **wrapKey** : om de DEK te kunnen beveiligen (versleutelen)

- **unwrapKey** : als u de beveiliging van de DEK wilt opheffen (ontsleutelen)

Key Vault-beheerder kan ook [logboekregistratie van controlegebeurtenissen van key vault](../../azure-monitor/insights/key-vault-insights-overview.md)inschakelen, zodat deze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd voor het gebruik van een TDE-beveiliging van AKV, verzendt de server de DEK van elke database met TDE-beveiliging naar de sleutelkluis voor versleuteling. Key Vault retourneert de versleutelde DEK, die vervolgens wordt opgeslagen in de gebruikersdatabase.

Indien nodig verzendt de server beveiligde DEK naar de sleutelkluis voor ontsleuteling.

Auditors kunnen Azure Monitor om auditevent-logboeken van de sleutelkluis te controleren als logboekregistratie is ingeschakeld.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Vereisten voor het configureren van door de klant beheerde TDE

### <a name="requirements-for-configuring-akv"></a>Vereisten voor het configureren van AKV

- Key Vault en SQL Database/beheerd exemplaar moeten deel uitmaken van dezelfde Azure Active Directory tenant. Sleutelkluis- en serverinteracties tussen tenants worden niet ondersteund. Als u later resources wilt verplaatsen, moet TDE met AKV opnieuw worden geconfigureerd. Meer informatie over [het verplaatsen van resources.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

- [De functie Voor het verwijderen](../../key-vault/general/soft-delete-overview.md) van gegevens moet zijn ingeschakeld op de sleutelkluis om te beschermen tegen het onbedoeld verwijderen van een sleutel (of sleutelkluis). Soft-leted resources worden 90 dagen bewaard, tenzij ze in de tussentijd door de klant zijn hersteld of verwijderd. Aan de acties *herstellen* en *opschonen* zijn aparte machtigingen gekoppeld in het toegangsbeleid van een sleutelkluis. De functie Voor het verwijderen van de functie is standaard uitgeschakeld en kan worden ingeschakeld via [PowerShell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) of [de CLI.](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli) Het kan niet worden ingeschakeld via de Azure Portal.  

- Verleen de server of het beheerde exemplaar toegang tot de sleutelkluis (get, wrapKey, unwrapKey) met behulp van Azure Active Directory identiteit. Wanneer u de Azure Portal, wordt de Azure AD-identiteit automatisch gemaakt. Wanneer u PowerShell of de CLI gebruikt, moet de Azure AD-identiteit expliciet worden gemaakt en moet de voltooiing worden geverifieerd. Zie [Configure TDE with BYOK (TDE](transparent-data-encryption-byok-configure.md) configureren met BYOK) en Configure [TDE with BYOK (TDE](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) configureren met BYOK) voor SQL Managed Instance stapsgewijs instructies bij het gebruik van PowerShell.

- Wanneer u een firewall met AKV gebruikt, moet u de optie Vertrouwde Microsoft-services om de *firewall te omzeilen inschakelen.*

### <a name="requirements-for-configuring-tde-protector"></a>Vereisten voor het configureren van TDE-beveiliging

- TDE-beveiliging kan alleen een asymmetrische, RSA- of RSA HSM-sleutel zijn. De ondersteunde sleutellengten zijn 2048 bytes en 3072 bytes.

- De activeringsdatum van de sleutel (indien ingesteld) moet een datum en tijd in het verleden zijn. De vervaldatum (indien ingesteld) moet een toekomstige datum en tijd zijn.

- De sleutel moet de status *Ingeschakeld* hebben.

- Als u een bestaande sleutel importeert in de sleutelkluis, moet u deze in de ondersteunde bestandsindelingen ( `.pfx` `.byok` , of ) `.backup` invoeren.

> [!NOTE]
> Azure SQL ondersteunt nu het gebruik van een RSA-sleutel die als TDE-beveiliging is opgeslagen in een beheerde HSM. Deze functie is beschikbaar als **openbare preview.** Azure Key Vault Managed HSM is een volledig beheerde, zeer beschikbare cloudservice met één tenant die voldoet aan de standaarden waarmee u cryptografische sleutels voor uw cloudtoepassingen kunt beveiligen met behulp van met FIPS 140-2 Level 3 gevalideerde HSM's. Meer informatie over [beheerde HMS's.](../../key-vault/managed-hsm/index.yml)


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Aanbevelingen bij het configureren van door de klant beheerde TDE

### <a name="recommendations-when-configuring-akv"></a>Aanbevelingen bij het configureren van AKV

- Koppel ten hoogste 500 Algemeen of 200 Bedrijfskritiek databases in totaal aan een sleutelkluis in één abonnement om hoge beschikbaarheid te garanderen wanneer de server toegang heeft tot de TDE-beveiliging in de sleutelkluis. Deze cijfers zijn gebaseerd op de ervaring en worden beschreven in de [servicelimieten van de sleutelkluis.](../../key-vault/general/service-limits.md) De bedoeling is om problemen na een server-failover te voorkomen, omdat hiermee net zoveel sleutelbewerkingen voor de kluis worden uitgevoerd als er databases op die server zijn.

- Stel een resourcevergrendeling in op de sleutelkluis om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of niet-geautoriseerde verwijdering te voorkomen. Meer informatie over [resourcevergrendelingen.](../../azure-resource-manager/management/lock-resources.md)

- Controle en rapportage van alle versleutelingssleutels inschakelen: Key Vault biedt logboeken die eenvoudig kunnen worden gebruikt in andere hulpprogramma's voor beveiligingsinformatie en gebeurtenisbeheer. Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) is een voorbeeld van een service die al is geïntegreerd.

- Koppel elke server aan twee sleutelkluizen die zich in verschillende regio's bevinden en hetzelfde sleutelmateriaal bevatten, om hoge beschikbaarheid van versleutelde databases te garanderen. Markeer alleen de sleutel uit de sleutelkluis in dezelfde regio als een TDE-beveiliging. Het systeem schakelt automatisch over naar de sleutelkluis in de externe regio als er een storing is die van invloed is op de sleutelkluis in dezelfde regio.

### <a name="recommendations-when-configuring-tde-protector"></a>Aanbevelingen bij het configureren van TDE-beveiliging

- Bewaar een kopie van de TDE-beveiliging op een veilige plaats of escrow deze naar de escrow-service.

- Als de sleutel wordt gegenereerd in de sleutelkluis, maakt u een back-up van de sleutel voordat u de sleutel voor het eerst in AKV gebruikt. De back-up kan alleen worden Azure Key Vault hersteld. Meer informatie over de [opdracht Backup-AzKeyVaultKey.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Maak een nieuwe back-up wanneer er wijzigingen worden aangebracht in de sleutel (bijvoorbeeld sleutelkenmerken, tags, ACL's).

- **Bewaar eerdere versies van de** sleutel in de sleutelkluis bij het roteren van sleutels, zodat oudere databaseback-ups kunnen worden hersteld. Wanneer de TDE-beveiliging wordt gewijzigd voor een database, worden oude back-ups van de **database** niet bijgewerkt om de meest recente TDE-beveiliging te gebruiken. Tijdens het herstellen heeft elke back-up de TDE-beveiliging nodig die tijdens het maken is versleuteld. Sleutelrotaties kunnen worden uitgevoerd volgens de instructies in [De Transparent Data Encryption-beveiliging roteren met behulp van PowerShell.](transparent-data-encryption-byok-key-rotation.md)

- Bewaar alle eerder gebruikte sleutels in AKV, zelfs na het overschakelen naar door de service beheerde sleutels. Het zorgt ervoor dat databaseback-ups kunnen worden hersteld met de TDE-beveiligingen die zijn opgeslagen in AKV.  TDE-beveiligingen die zijn gemaakt Azure Key Vault moeten worden onderhouden totdat alle resterende opgeslagen back-ups zijn gemaakt met door de service beheerde sleutels. Maak herstelbare back-ups van deze sleutels met [backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Als u een mogelijk aangetaste sleutel wilt verwijderen tijdens een beveiligingsincident zonder het risico op gegevensverlies, volgt u de stappen in [Remove a potentially compromised key](transparent-data-encryption-byok-remove-tde-protector.md)(Een mogelijk aangetaste sleutel verwijderen).

## <a name="inaccessible-tde-protector"></a>Niet-toegankelijke TDE-beveiliging

Wanneer transparante gegevensversleuteling is geconfigureerd voor het gebruik van een door de klant beheerde sleutel, is continue toegang tot de TDE-beveiliging vereist om de database online te houden. Als de server geen toegang meer heeft tot de door de klant beheerde TDE-beveiliging in AKV, worden in maximaal tien minuten alle verbindingen met het bijbehorende foutbericht door een database weigert en wordt de status ervan gewijzigd in *Niet* toegankelijk. De enige actie die is toegestaan voor een database met de status Niet toegankelijk, is het verwijderen ervan.

> [!NOTE]
> Als de database niet toegankelijk is vanwege een onregelmatige netwerkstoring, is er geen actie vereist en komen de databases automatisch weer online.

Nadat de toegang tot de sleutel is hersteld, zijn extra tijd en stappen nodig om de database weer online te zetten. Dit kan variëren afhankelijk van de tijd die is verstreken zonder toegang tot de sleutel en de grootte van de gegevens in de database:

- Als de toegang tot de sleutel binnen acht uur wordt hersteld, wordt de database binnen het volgende uur automatisch hervat.

- Als de sleuteltoegang na meer dan 8 uur wordt hersteld, is automatischheal niet mogelijk en vereist het terughalen van de database extra stappen in de portal en kan dit een aanzienlijke hoeveelheid tijd duren, afhankelijk van de grootte van de database. Zodra de database weer online is, gaan eerder geconfigureerde instellingen op serverniveau, zoals [failovergroepconfiguratie,](auto-failover-group-overview.md) geschiedenis van herstel naar een bepaald tijdstip en tags, **verloren.** Daarom is het raadzaam om een meldingssysteem te implementeren waarmee u de onderliggende problemen met sleuteltoegang binnen acht uur kunt identificeren en oplossen.

Hieronder vindt u een overzicht van de extra stappen die in de portal zijn vereist om een ontoegankelijke database weer online te brengen.

![TDE BYOK Niet-toegankelijke database](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Onopzettelijk intrekken van toegang door TDE-beveiliging

Het kan gebeuren dat iemand met voldoende toegangsrechten voor de sleutelkluis per ongeluk servertoegang tot de sleutel uitsluist door:

- de machtigingen *get,* *wrapKey* en *unwrapKey* van de sleutelkluis van de server inroepen

- de sleutel verwijderen

- de sleutelkluis verwijderen

- de firewallregels van de sleutelkluis wijzigen

- de beheerde identiteit van de server in de Azure Active Directory

Meer informatie over [de veelvoorkomende oorzaken voor database ontoegankelijk worden.](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible)

## <a name="monitoring-of-the-customer-managed-tde"></a>Bewaking van de door de klant beheerde TDE

Als u de databasetoestand wilt bewaken en waarschuwingen wilt inschakelen voor verlies van toegang tot TDE-beveiliging, configureert u de volgende Azure-functies:

- [Azure Resource Health](../../service-health/resource-health-overview.md). Een niet-toegankelijke database die geen toegang meer heeft tot de TDE-beveiliging wordt als Niet beschikbaar beschouwd nadat de eerste verbinding met de database is geweigerd.
- [Activiteitenlogboek](../../service-health/alerts-activity-log-service-notifications-portal.md) wanneer toegang tot de TDE-beveiliging in de door de klant beheerde sleutelkluis mislukt, worden vermeldingen toegevoegd aan het activiteitenlogboek.  Als u waarschuwingen voor deze gebeurtenissen maakt, kunt u de toegang zo snel mogelijk opnieuw instellen.
- [Actiegroepen](../../azure-monitor/alerts/action-groups.md) kunnen worden gedefinieerd om u meldingen en waarschuwingen te sturen op basis van uw voorkeuren, bijvoorbeeld e-mail/sms/push/spraak, logische app, webhook, ITSM of Automation-runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Back-up en herstel van de database met door de klant beheerde TDE

Zodra een database is versleuteld met TDE met behulp van een sleutel van Key Vault, worden nieuwe back-ups ook versleuteld met dezelfde TDE-beveiliging. Wanneer de TDE-beveiliging wordt gewijzigd, worden oude back-ups van de **database** niet bijgewerkt om de meest recente TDE-beveiliging te gebruiken.

Als u een back-up wilt herstellen die is versleuteld met een TDE-beveiliging van Key Vault, moet u ervoor zorgen dat het sleutelmateriaal beschikbaar is voor de doelserver. Daarom wordt u aangeraden alle oude versies van de TDE-protector in de sleutelkluis te bewaren, zodat back-ups van de database kunnen worden hersteld.

> [!IMPORTANT]
> Op elk moment kan er niet meer dan één TDE-beveiliging zijn ingesteld voor een server. Dit is de sleutel die is gemarkeerd met 'Maak van de sleutel de standaard TDE-beveiliging' op de Azure Portal blade. Er kunnen echter meerdere extra sleutels worden gekoppeld aan een server zonder ze te markeren als een TDE-beveiliging. Deze sleutels worden niet gebruikt voor het beveiligen van DEK, maar kunnen worden gebruikt tijdens het herstellen vanuit een back-up als het back-upbestand is versleuteld met de sleutel met de bijbehorende vingerafdruk.

Als de sleutel die nodig is voor het herstellen van een back-up niet meer beschikbaar is voor de doelserver, wordt het volgende foutbericht geretourneerd bij het herstellen: 'Doelserver heeft geen toegang tot alle AKV-URI's die zijn gemaakt tussen `<Servername>` \<Timestamp #1> en \<Timestamp #2> . Opnieuw proberen na het herstellen van alle AKV-URI's.

Voer de cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) uit voor de doelserver of [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) voor het beheerde doelin exemplaar om de lijst met beschikbare sleutels te retourneren en de ontbrekende sleutels te identificeren. Om ervoor te zorgen dat alle back-ups kunnen worden hersteld, moet u ervoor zorgen dat de doelserver voor het herstellen toegang heeft tot alle benodigde sleutels. Deze sleutels hoeven niet te worden gemarkeerd als TDE-beveiliging.

Zie Een database herstellen in SQL Database voor meer informatie over het herstellen van [back SQL Database.](recovery-using-backups.md) Zie Recover a dedicated SQL pool (Een toegewezen SQL-pool herstellen) voor meer Azure Synapse Analytics over back-upherstel voor toegewezen [SQL-pool](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md)in Azure Synapse Analytics. Voor SQL Server systeemeigen back-up/herstel met SQL Managed Instance, zie [Quickstart: Een database](../managed-instance/restore-sample-database-quickstart.md) herstellen naar SQL Managed Instance

Aanvullende overweging voor logboekbestanden: back-up van logboekbestanden blijven versleuteld met de oorspronkelijke TDE-beveiliging, zelfs als deze is geroteerd en de database nu een nieuwe TDE-beveiliging gebruikt.  Tijdens het herstellen zijn beide sleutels nodig om de database te herstellen.  Als het logboekbestand een TDE-beveiliging gebruikt die is opgeslagen in Azure Key Vault, is deze sleutel nodig tijdens het herstellen, zelfs als de database in de tussentijd is gewijzigd om door de service beheerde TDE te gebruiken.

## <a name="high-availability-with-customer-managed-tde"></a>Hoge beschikbaarheid met door de klant beheerde TDE

Zelfs in gevallen waarin er geen geconfigureerde geo-redundantie voor de server is, wordt het ten zeerste aanbevolen om de server te configureren voor het gebruik van twee verschillende sleutelkluizen in twee verschillende regio's met hetzelfde sleutelmateriaal. De sleutel in de secundaire sleutelkluis in de andere regio mag niet worden gemarkeerd als TDE-beveiliging en is zelfs niet toegestaan. Als er een storing is die van invloed is op de primaire sleutelkluis en alleen dan, schakelt het systeem automatisch over naar de andere gekoppelde sleutel met dezelfde vingerafdruk in de secundaire sleutelkluis, als deze bestaat. Houd er rekening mee dat de switch niet wordt uitgevoerd als TDE-beveiliging niet toegankelijk is vanwege ingetrokken toegangsrechten of omdat de sleutel of sleutelkluis wordt verwijderd, omdat dit kan aangeven dat de klant opzettelijk wil voorkomen dat de server toegang heeft tot de sleutel. Het leveren van hetzelfde sleutelmateriaal aan twee sleutelkluizen in verschillende regio's kan worden gedaan door de sleutel buiten de sleutelkluis te maken en deze in beide sleutelkluizen te importeren. 

U kunt dit ook doen door een sleutel te genereren met behulp van de primaire sleutelkluis die zich in dezelfde regio als de server heeft opgeslagen en de sleutel te klonen in een sleutelkluis in een andere Azure-regio. Gebruik de cmdlet [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) om de sleutel in versleutelde indeling op te halen uit de primaire sleutelkluis. Gebruik vervolgens de cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) en geef een sleutelkluis op in de tweede regio om de sleutel te klonen. U kunt ook de Azure Portal om een back-up te maken van de sleutel en deze te herstellen. Back-up-/herstelbewerkingen van sleutels zijn alleen toegestaan tussen sleutelkluizen binnen hetzelfde Azure-abonnement en [de Geografie van Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)  

![Single-Server ha](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR en door de klant beheerde TDE

In [scenario's met](active-geo-replication-overview.md) zowel actieve geo-replicatie als [failovergroepen](auto-failover-group-overview.md) is voor elke betrokken server een afzonderlijke sleutelkluis vereist die samen met de server in dezelfde Azure-regio moet worden toegewezen. De klant is verantwoordelijk voor het consistent houden van het sleutelmateriaal in de sleutelkluizen, zodat geo-secundaire gegevens gesynchroniseerd zijn en dezelfde sleutel uit de lokale sleutelkluis kunnen overnemen als de primaire sleutel ontoegankelijk wordt vanwege een storing in de regio en er een failover wordt geactiveerd. Er kunnen maximaal vier secondaries worden geconfigureerd en chaining (secondaries van secondaries) wordt niet ondersteund.

Als u problemen wilt voorkomen tijdens het maken of tijdens geo-replicatie vanwege onvolledig sleutelmateriaal, is het belangrijk om deze regels te volgen bij het configureren van door de klant beheerde TDE:

- Alle betrokken sleutelkluizen moeten dezelfde eigenschappen en dezelfde toegangsrechten hebben voor de respectieve servers.

- Alle betrokken sleutelkluizen moeten identiek sleutelmateriaal bevatten. Het is niet alleen van toepassing op de huidige TDE-beveiliging, maar op alle eerdere TDE-beveiligingen die kunnen worden gebruikt in de back-upbestanden.

- Zowel de eerste installatie als rotatie van de TDE-beveiliging moet eerst worden uitgevoerd op de secundaire en vervolgens op de primaire.

![Failovergroepen en geo-dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Volg de stappen in Active geo-replication overview (Overzicht van actieve [geo-replicatie)](active-geo-replication-overview.md)om een failover te testen. Failover testen moet regelmatig worden uitgevoerd om te controleren of SQL Database toegangsrechten heeft voor beide sleutelkluizen.

## <a name="next-steps"></a>Volgende stappen

U kunt ook de volgende PowerShell-voorbeeldscripts controleren op de algemene bewerkingen met door de klant beheerde TDE:

- [De beveiliging van Transparent Data Encryption draaien voor SQL Database powershell](transparent-data-encryption-byok-key-rotation.md)

- [Een TDE Transparent Data Encryption beveiliging verwijderen voor SQL Database met behulp van PowerShell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Uw Transparent Data Encryption in SQL Managed Instance beheren met uw eigen sleutel met behulp van PowerShell](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)