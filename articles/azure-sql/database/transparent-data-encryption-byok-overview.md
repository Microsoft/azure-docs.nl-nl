---
title: Door de klant beheerde transparante gegevens versleuteling (TDE)
description: Bring Your Own Key-ondersteuning (BYOK) voor Transparent Data Encryption (TDE) met Azure Key Vault voor SQL Database en Azure Synapse Analytics. TDE met BYOK overview, voor delen, hoe het werkt, overwegingen en aanbevelingen.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 2a7d77579eaebd3ee951d0184e25937783420806
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325193"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL Transparent Data Encryption met door de klant beheerde sleutels
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL [transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) met door de klant beheerde sleutel maakt het Bring your own Key-scenario (BYOK) mogelijk voor gegevens bescherming in rust en stelt organisaties in staat om schei ding van taken in het beheer van sleutels en gegevens te implementeren. Met door de klant beheerde transparante gegevens versleuteling is de klant verantwoordelijk voor en in een volledig beheer van een belang rijke levenscyclus beheer (het maken van sleutels, uploaden, draaien, verwijderen), machtigingen voor sleutel gebruik en het controleren van bewerkingen op sleutels.

In dit scenario is de sleutel die wordt gebruikt voor het versleutelen van de database versleutelings sleutel (DEK), de zogeheten TDE-Protector, een door de klant beheerde asymmetrische sleutel die is opgeslagen in een door de klant en door de klant beheerde [Azure Key Vault (Azure)](../../key-vault/general/secure-your-key-vault.md), een op de cloud gebaseerd extern sleutel beheersysteem. Key Vault is een Maxi maal beschik bare en schaal bare beveiligde opslag voor RSA cryptografische sleutels, optioneel ondersteund door FIPS 140-2 level 2 gevalideerde hardware security modules (Hsm's). Het biedt geen directe toegang tot een opgeslagen sleutel, maar voorziet in Services voor het versleutelen/ontsleutelen met behulp van de sleutel voor de gemachtigde entiteiten. De sleutel kan worden gegenereerd door de sleutel kluis, geïmporteerd of [overgedragen naar de sleutel kluis van een on-premises HSM-apparaat](../../key-vault/keys/hsm-protected-keys.md).

Voor Azure SQL Database en Azure Synapse Analytics wordt de TDE-Protector ingesteld op server niveau en overgenomen door alle versleutelde data bases die aan die server zijn gekoppeld. Voor Azure SQL Managed instance wordt de TDE-Protector ingesteld op het niveau van de instantie en overgenomen door alle versleutelde data bases op die instantie. De term *Server* verwijst naar een server in SQL database en Azure Synapse en naar een beheerd exemplaar in een SQL Managed instance in het hele document, tenzij anders aangegeven.

> [!IMPORTANT]
> Voor degenen die gebruikmaken van service-beheerde TDE die willen beginnen met door de klant beheerde TDE, blijft de gegevens versleuteld tijdens het proces van overschakeling en wordt er geen downtime of hercodering van de database bestanden weer gegeven. Wanneer u overschakelt van een door een service beheerde sleutel naar een door de klant beheerde sleutel, hoeft u de DEK alleen opnieuw te versleutelen, wat een snelle en online bewerking is.

> [!NOTE]
> <a id="doubleencryption"></a> Om Azure SQL-klanten te voorzien van twee lagen versleuteling van gegevens in rust, wordt infrastructuur versleuteling (met AES-256-versleutelings algoritme) met door het platform beheerde sleutels geïmplementeerd. Dit biedt een toevoeging van de versleutelings laag en de TDE met door de klant beheerde sleutels, die al beschikbaar zijn. Voor Azure SQL Database en het beheerde exemplaar worden alle data bases, met inbegrip van de hoofd database en andere systeem databases, versleuteld wanneer infrastructuur versleuteling is ingeschakeld. Klanten moeten op dit moment toegang vragen tot deze mogelijkheid. Als u geïnteresseerd bent in deze mogelijkheid, neemt u contact op met AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Voor delen van de door de klant beheerde TDE

Door de klant beheerde TDE biedt de volgende voor delen voor de klant:

- Volledige en nauw keurige controle over het gebruik en het beheer van de TDE-Protector;

- Transparantie van het gebruik van TDE-Protector;

- Mogelijkheid om schei ding van taken in het beheer van sleutels en gegevens binnen de organisatie te implementeren;

- Key Vault beheerder kan belang rijke toegangs machtigingen intrekken om de versleutelde data base ontoegankelijk te maken.

- Centraal beheer van sleutels in azure;

- Meer vertrouwen van uw eind klanten, omdat Azure is ontworpen zodat micro soft geen versleutelings sleutels kan zien of extra heren.

## <a name="how-customer-managed-tde-works"></a>Hoe door de klant beheerde TDE werkt

![Instellen en werken van de door de klant beheerde TDE](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Om de server in staat te stellen TDE-beveiliging te gebruiken die is opgeslagen in azure voor versleuteling van de DEK, moet de sleutel kluis beheerder de volgende toegangs rechten verlenen aan de server met behulp van de unieke Azure Active Directory (Azure AD)-identiteit:

- **Get** -voor het ophalen van het open bare onderdeel en de eigenschappen van de sleutel in de Key Vault

- **wrapKey** : om te kunnen beveiligen (versleutelen) dek

- **sleutel uitpakken** : voor het opheffen van de beveiliging (ontsleutelen) dek

De sleutel kluis beheerder kan ook [logboek registratie inschakelen voor gebeurtenissen van sleutel kluis controle](../../azure-monitor/insights/key-vault-insights-overview.md), zodat ze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd voor het gebruik van een TDE-Protector van Azure, stuurt de server de DEK van elke TDE-Data Base naar de sleutel kluis voor versleuteling. Sleutel kluis retourneert de versleutelde DEK, die vervolgens wordt opgeslagen in de gebruikers database.

Wanneer dit nodig is, verzendt de server beveiligde DEK naar de sleutel kluis voor ontsleuteling.

Audi tors kunnen Azure Monitor gebruiken om de sleutel kluis audit event-logboeken te bekijken als logboek registratie is ingeschakeld.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Vereisten voor het configureren van door de klant beheerde TDE

### <a name="requirements-for-configuring-akv"></a>Vereisten voor het configureren van Azure

- De sleutel kluis en het SQL Database/beheerde exemplaar moeten deel uitmaken van dezelfde Azure Active Directory Tenant. De cross-Tenant sleutel kluis en server interacties worden niet ondersteund. Als u later resources wilt verplaatsen, moet TDE met Azure opnieuw worden geconfigureerd. Meer informatie over het [verplaatsen van resources](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

- De functie voor [voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md) moet zijn ingeschakeld op de sleutel kluis om te beschermen tegen onbedoelde sleutel (of sleutel kluis) voor het verwijderen van gegevens verlies. Voorlopig verwijderde bronnen worden 90 dagen bewaard, tenzij de klant in de tussen tijd is hersteld of verwijderd. Aan de acties *herstellen* en *opschonen* zijn aparte machtigingen gekoppeld in het toegangsbeleid van een sleutelkluis. De functie voor voorlopig verwijderen is standaard uitgeschakeld en kan worden ingeschakeld via [Power shell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) of [de CLI](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli). Deze kan niet worden ingeschakeld via de Azure Portal.  

- Verleen de server of het beheerde exemplaar toegang tot de sleutel kluis (Get, wrapKey, sleutel uitpakken) met behulp van de Azure Active Directory identiteit. Wanneer u de Azure Portal gebruikt, wordt de Azure AD-identiteit automatisch gemaakt. Wanneer u Power shell of de CLI gebruikt, moet de Azure AD-identiteit expliciet worden gemaakt en moet de voltooiing worden gecontroleerd. Zie [Configure TDe with BYOK](transparent-data-encryption-byok-configure.md) en [Configure TDe with BYOK for SQL Managed instance](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) voor gedetailleerde stapsgewijze instructies voor het gebruik van Power shell.

- Wanneer u Firewall met Azure gebruikt, moet u *de optie vertrouwde micro soft-Services toestaan om de firewall te omzeilen*, in te scha kelen.

### <a name="requirements-for-configuring-tde-protector"></a>Vereisten voor het configureren van TDE-beveiliging

- TDE-Protector kan alleen asymmetrische, RSA-of RSA HSM-sleutel zijn. De ondersteunde sleutel lengten zijn 2048 en 3072 bytes.

- De datum en tijd waarop de sleutel wordt geactiveerd, moeten in het verleden liggen. De verval datum (indien ingesteld) moet een datum en tijd in de toekomst zijn.

- De sleutel moet de *ingeschakelde* status hebben.

- Als u een bestaande sleutel in de sleutel kluis importeert, moet u deze opgeven in de ondersteunde bestands indelingen (. pfx,. byok of. back-up).

> [!NOTE]
> Azure SQL ondersteunt nu het gebruik van een RSA-sleutel die is opgeslagen in een beheerde HSM als TDE-Protector. Deze functie is beschikbaar als **open bare preview**. Azure Key Vault Managed HSM is een volledig beheerde, Maxi maal beschik bare, door standaarden compatibele Cloud service met één Tenant waarmee u cryptografische sleutels voor uw Cloud toepassingen kunt beveiligen met behulp van FIPS 140-2 level 3 gevalideerd Hsm's. Meer informatie over [beheerde hsm's](../../key-vault/managed-hsm/index.yml).


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Aanbevelingen voor het configureren van door de klant beheerde TDE

### <a name="recommendations-when-configuring-akv"></a>Aanbevelingen voor het configureren van Azure

- U kunt in totaal Maxi maal 500 Algemeen of 200 Bedrijfskritiek data bases koppelen aan een sleutel kluis in één abonnement om hoge Beschik baarheid te garanderen wanneer de server toegang heeft tot de TDE-beveiliging in de sleutel kluis. Deze cijfers zijn gebaseerd op de ervaring en gedocumenteerd in de [sleutel kluis-service limieten](../../key-vault/general/service-limits.md). Dit is om te voor komen dat u problemen ondervindt na een failover van de server, omdat deze wordt geactiveerd als veel belang rijke bewerkingen voor de kluis als er data bases op die server zijn.

- Stel een resource vergrendeling in voor de sleutel kluis om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of ongeoorloofde verwijdering te voor komen. Meer informatie over [resource vergrendelingen](../../azure-resource-manager/management/lock-resources.md).

- Controle en rapportage inschakelen voor alle versleutelings sleutels: sleutel kluis biedt logboeken die eenvoudig kunnen worden geïnjecteerd in andere hulpprogram ma's voor beveiligings informatie en beheer van gebeurtenissen. Operations Management Suite [log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) is een voor beeld van een service die al is geïntegreerd.

- Koppel elke server aan twee sleutel kluizen die zich in verschillende regio's bevinden, en bewaar hetzelfde sleutel materiaal om hoge Beschik baarheid van versleutelde data bases te garanderen. Markeer alleen de sleutel van de sleutel kluis in dezelfde regio als een TDE-Protector. Het systeem wordt automatisch overgeschakeld naar de sleutel kluis in de externe regio als er een storing optreedt in de sleutel kluis in dezelfde regio.

### <a name="recommendations-when-configuring-tde-protector"></a>Aanbevelingen voor het configureren van TDE-beveiliging

- Bewaar een kopie van de TDE-protector op een veilige plaats of borg aan de borg service.

- Als de sleutel wordt gegenereerd in de sleutel kluis, maakt u een sleutel back-up voordat u de sleutel voor de eerste keer gebruikt in Azure. U kunt back-ups alleen herstellen naar een Azure Key Vault. Meer informatie over de [back-up-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey) -opdracht.

- Maak een nieuwe back-up wanneer er wijzigingen worden aangebracht in de sleutel (bijvoorbeeld sleutel kenmerken, tags, Acl's).

- **Bewaar vorige versies** van de sleutel in de sleutel kluis bij het draaien van sleutels, zodat oudere database back-ups kunnen worden hersteld. Wanneer de TDE-Protector voor een Data Base wordt gewijzigd, worden de oude back-ups van de Data Base **niet bijgewerkt** voor gebruik van de meest recente TDe-Protector. Op het moment van terugzetten heeft elke back-up de TDE-Protector nodig die is versleuteld met tijdens het maken. U kunt de volgende stappen uitvoeren om de [transparent Data Encryption beveiliging te draaien met behulp van Power shell](transparent-data-encryption-byok-key-rotation.md).

- Bewaar alle eerder gebruikte sleutels in azure, zelfs na het overschakelen naar door service beheerde sleutels. Het zorgt ervoor dat database back-ups kunnen worden hersteld met de TDE-beveiligingen die zijn opgeslagen in Azure.  TDE-beveiligingen die zijn gemaakt met Azure Key Vault moeten worden behouden totdat alle resterende opgeslagen back-ups zijn gemaakt met door service beheerde sleutels. Herstel bare back-ups van deze sleutels maken met [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Als u een mogelijk gemanipuleerde sleutel tijdens een beveiligings incident wilt verwijderen zonder het risico van gegevens verlies, volgt u de stappen in de [sleutel remove a potentieel aangetast](transparent-data-encryption-byok-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Ontoegankelijke TDE-beveiliging

Wanneer transparante gegevens versleuteling is geconfigureerd voor het gebruik van een door de klant beheerde sleutel, is voortdurende toegang tot de TDE-Protector vereist voor de data base om online te blijven. Als de server geen toegang meer heeft tot de door de klant beheerde TDE-Protector in azure, wordt in Maxi maal tien minuten een Data Base gestart met het weigeren van alle verbindingen met het bijbehorende fout bericht en wordt de status gewijzigd in niet *toegankelijk*. De enige actie die is toegestaan voor een Data Base met de status unaccessible, wordt deze verwijderd.

> [!NOTE]
> Als de data base niet toegankelijk is vanwege een onregelmatige netwerk storing, is er geen actie vereist en worden de data bases automatisch weer online gezet.

Nadat de toegang tot de sleutel is hersteld, is voor de Data Base weer online extra tijd en stappen vereist, afhankelijk van de tijd die is verstreken zonder toegang tot de sleutel en de grootte van de gegevens in de Data Base:

- Als de sleutel toegang binnen acht uur wordt hersteld, wordt de data base binnen het volgende uur automatisch opnieuw geactiveerd.

- Als de sleuteltoegang na meer dan acht uur is hersteld, is automatisch herstellen niet mogelijk en zijn er voor aanvullende stappen in de portal nodig om de database weer actief te krijgen. Dit kan afhankelijk van de grootte van de database vrij lang duren. Zodra de Data Base weer online is, eerder geconfigureerde instellingen op server niveau, zoals [failover-groeps](auto-failover-group-overview.md) configuratie, punt-in-time-herstel geschiedenis en tags **gaan verloren**. Daarom is het raadzaam om een meldings systeem te implementeren waarmee u de onderliggende sleutel toegangs problemen binnen acht uur kunt identificeren en oplossen.

Hieronder ziet u een overzicht van de extra stappen die vereist zijn op de portal om een ontoegankelijke Data Base weer online te zetten.

![TDE BYOK niet-toegankelijke data base](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Onopzettelijke toegang tot TDE-Protector

Het kan gebeuren dat iemand met voldoende toegangs rechten voor de sleutel kluis per ongeluk server toegang tot de sleutel uitschakelt door:

- de *Get*-, *wrapKey*-, *sleutel uitpakken* -machtigingen van de sleutel kluis intrekken van de server

- de sleutel verwijderen

- de sleutel kluis verwijderen

- de firewall regels van de sleutel kluis wijzigen

- de beheerde identiteit van de server in Azure Active Directory verwijderen

Meer informatie over [de algemene oorzaken voor het ontoegankelijk maken van de data base](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Bewaking van de door de klant beheerde TDE

Als u de status van de Data Base wilt bewaken en waarschuwingen wilt inschakelen voor het verlies van toegang tot TDE-Protector, configureert u de volgende Azure-functies:

- [Azure resource Health](../../service-health/resource-health-overview.md). Een ontoegankelijke data base die de toegang tot de TDE-Protector heeft verloren, wordt weer gegeven als ' niet beschikbaar ' nadat de eerste verbinding met de data base is geweigerd.
- [Activiteiten logboek](../../service-health/alerts-activity-log-service-notifications-portal.md) wanneer toegang tot de TDe-Protector in de door de klant beheerde sleutel kluis mislukt, worden de vermeldingen toegevoegd aan het activiteiten logboek.  Als u waarschuwingen voor deze gebeurtenissen maakt, kunt u zo snel mogelijk toegang herstellen.
- [Actie groepen](../../azure-monitor/platform/action-groups.md) kunnen worden gedefinieerd om u meldingen en waarschuwingen te sturen op basis van uw voor keuren, bijvoorbeeld E-mail/SMS/push/Voice, Logic app, webhook, ITSM of Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Back-up en herstel van de data base met door de klant beheerde TDE

Wanneer een Data Base is versleuteld met TDE met behulp van een sleutel uit Key Vault, worden alle nieuw gegenereerde back-ups ook versleuteld met dezelfde TDE-Protector. Wanneer de TDE-beveiliging wordt gewijzigd, worden oude back-ups van de Data Base **niet bijgewerkt** voor gebruik van de meest recente TDe-Protector.

Als u een back-up wilt herstellen die is versleuteld met een TDE-Protector van Key Vault, moet u ervoor zorgen dat het sleutel materiaal beschikbaar is voor de doel server. Daarom is het raadzaam dat u alle oude versies van de TDE-Protector in de sleutel kluis bewaart, zodat database back-ups kunnen worden hersteld.

> [!IMPORTANT]
> Er kan op elk moment niet meer dan één TDE-protector worden ingesteld voor een server. De sleutel is gemarkeerd met de sleutel de standaard TDE-Protector maken op de Blade Azure Portal. Meerdere extra sleutels kunnen echter aan een server worden gekoppeld zonder dat ze als een TDE-protector worden gemarkeerd. Deze sleutels worden niet gebruikt voor het beveiligen van DEK, maar kunnen worden gebruikt tijdens het terugzetten vanuit een back-up, als het back-upbestand is versleuteld met de sleutel met de bijbehorende vinger afdruk.

Als de sleutel die nodig is voor het herstellen van een back-up niet langer beschikbaar is voor de doel server, wordt het volgende fout bericht weer gegeven op de herstel poging: "doel server heeft `<Servername>` geen toegang tot alle Azure-uri's die tussen en zijn gemaakt \<Timestamp #1> \<Timestamp #2> . Voer de bewerking opnieuw uit nadat alle Azure-Uri's zijn teruggezet.

Om het te beperken, voert u de cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) uit voor de doel server of [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) voor het beheerde exemplaar van het doel om de lijst met beschik bare sleutels te retour neren en de ontbrekende waarden te identificeren. Om ervoor te zorgen dat alle back-ups kunnen worden hersteld, moet u ervoor zorgen dat de doel server voor het terugzetten toegang heeft tot alle benodigde sleutels. Deze sleutels hoeven niet als TDE-Protector te worden gemarkeerd.

Zie [een Data Base herstellen in SQL database](recovery-using-backups.md)voor meer informatie over het herstel van back-ups voor SQL database. Zie [een toegewezen SQL-groep herstellen](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md)voor meer informatie over back-upherstel voor een toegewezen SQL-groep in azure Synapse Analytics. Zie voor het systeem eigen back-up/herstel van de SQL Server met SQL Managed instance [Quick Start: een Data Base herstellen naar een SQL-beheerd exemplaar](../managed-instance/restore-sample-database-quickstart.md)

Aanvullende overwegingen voor logboek bestanden: back-ups van logboek bestanden blijven versleuteld met de oorspronkelijke TDE-Protector, zelfs als deze is gedraaid en de data base nu een nieuwe TDE-Protector gebruikt.  Op het moment van terugzetten zijn beide sleutels nodig om de data base te herstellen.  Als het logboek bestand gebruikmaakt van een TDE-Protector die is opgeslagen in Azure Key Vault, is deze sleutel vereist tijdens de herstel tijd, zelfs als de data base is gewijzigd voor het gebruik van door de service beheerde TDE.

## <a name="high-availability-with-customer-managed-tde"></a>Hoge Beschik baarheid met door de klant beheerde TDE

Zelfs als er geen geo-redundantie is geconfigureerd voor de server, wordt het ten zeerste aanbevolen om de server te configureren voor het gebruik van twee verschillende sleutel kluizen in twee verschillende regio's met hetzelfde sleutel materiaal. U kunt dit doen door een TDE-Protector te maken met behulp van de primaire sleutel kluis die zich in dezelfde regio bevindt als de-server en de sleutel te klonen in een sleutel kluis in een andere Azure-regio, zodat de server toegang heeft tot een tweede sleutel kluis als de primaire sleutel kluis een storing ondervindt terwijl de data base actief is.

Gebruik de cmdlet Backup-AzKeyVaultKey om de sleutel in een versleutelde indeling op te halen uit de primaire sleutel kluis en gebruik vervolgens de Restore-AzKeyVaultKey cmdlet en geef een sleutel kluis in de tweede regio op om de sleutel te klonen. U kunt ook de Azure Portal gebruiken om een back-up te maken van de sleutel en deze te herstellen. De sleutel in de secundaire sleutel kluis in de andere regio mag niet worden gemarkeerd als TDE-Protector en is niet zelfs toegestaan.

Als er een storing optreedt in de primaire sleutel kluis en alleen dan wordt het systeem automatisch overgeschakeld naar de andere gekoppelde sleutel met dezelfde vinger afdruk in de secundaire sleutel kluis als deze bestaat. Houd er rekening mee dat de switch niet wordt uitgevoerd als TDE-Protector niet toegankelijk is vanwege ingetrokken toegangs rechten, of omdat de sleutel of sleutel kluis is verwijderd, omdat dit kan erop wijzen dat de klant opzettelijk de toegang tot de sleutel wil beperken.

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR en door de klant beheerde TDE

In de scenario's voor zowel [actieve geo-replicatie](active-geo-replication-overview.md) als voor [failover-groepen](auto-failover-group-overview.md) is voor elke server die betrokken is een afzonderlijke sleutel kluis vereist. deze moet zich op dezelfde locatie bevinden als de server in dezelfde Azure-regio. De klant is verantwoordelijk voor het bewaren van het belangrijkste materiaal in de sleutel kluizen, zodat geo-secundair synchroon is en dezelfde sleutel kan overnemen uit de lokale sleutel kluis als de primaire ontoegankelijk is vanwege een storing in de regio en een failover wordt geactiveerd. Er kunnen Maxi maal vier secundairen worden geconfigureerd en koppelen (secundaire zones van de secundaire zones) wordt niet ondersteund.

Als u problemen wilt voor komen tijdens het maken of tijdens het tot stand brengen van geo-replicatie vanwege onvolledige sleutel materiaal, is het belang rijk om deze regels te volgen bij het configureren van door de klant beheerde TDE:

- Alle betrokken sleutel kluizen moeten dezelfde eigenschappen hebben en dezelfde toegangs rechten voor de betreffende servers.

- Alle betrokken sleutel kluizen moeten identieke sleutel materialen bevatten. Het is niet alleen van toepassing op de huidige TDE-Protector, maar op alle vorige TDE-beveiligingen die kunnen worden gebruikt in de back-upbestanden.

- De initiële installatie en rotatie van de TDE-Protector moeten eerst worden uitgevoerd op het secundaire en vervolgens op de primaire.

![Failover-groepen en geo-Dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Als u een failover wilt testen, volgt u de stappen in het [overzicht van actieve geo-replicatie](active-geo-replication-overview.md). Het testen van failover moet regel matig worden uitgevoerd om te controleren of SQL Database de toegangs machtiging voor zowel sleutel kluizen heeft behouden.

## <a name="next-steps"></a>Volgende stappen

U kunt ook de volgende Power shell-voorbeeld scripts controleren voor algemene bewerkingen met door de klant beheerde TDE:

- [De Transparent Data Encryption-Protector voor SQL Database draaien met behulp van Power shell](transparent-data-encryption-byok-key-rotation.md)

- [Een Transparent Data Encryption-Protector (TDE) voor SQL Database verwijderen met behulp van Power shell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Transparent Data Encryption beheren in een door SQL beheerd exemplaar met uw eigen sleutel met behulp van Power shell](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)