---
title: Gegevensversleuteling met door de klant beheerde sleutel - Azure Database for PostgreSQL - Enkele server
description: Azure Database for PostgreSQL single server data encryption with a customer-managed key (Gegevensversleuteling met één server met een door de klant beheerde sleutel) kunt Bring Your Own Key (BYOK) gebruiken voor data-at-rest-beveiliging. Daarnaast kunnen organisaties hiermee een scheiding van taken implementeren bij het beheer van sleutels en gegevens.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 8edb5e44fc0a8e7aa67c4edd69971c35c6866d82
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752458"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database for PostgreSQL single server data encryption with a customer-managed key (Gegevensversleuteling met één server met een door de klant beheerde sleutel)

Azure PostgreSQL maakt gebruik [van Azure Storage](../storage/common/storage-service-encryption.md) versleuteling om gegevens in rust standaard te versleutelen met behulp van door Microsoft beheerde sleutels. Voor Azure PostgreSQL-gebruikers is het vergelijkbaar met Transparent Data Encryption (TDE) in andere databases, zoals SQL Server. Veel organisaties hebben volledige controle nodig over de toegang tot de gegevens met behulp van een door de klant beheerde sleutel. Met gegevensversleuteling met door de klant beheerde sleutels voor Azure Database for PostgreSQL Single Server kunt u BYOK (Bring Your Own Key) gebruiken voor data-at-rest-beveiliging. Daarnaast kunnen organisaties hiermee een scheiding van taken implementeren bij het beheer van sleutels en gegevens. Met door de klant beheerde versleuteling bent u verantwoordelijk voor, en hebt u het volledige beheer over, de levenscyclus van een sleutel, de machtigingen voor sleutelgebruik, en het controleren van de bewerkingen van sleutels.

Gegevensversleuteling met door de klant beheerde sleutels voor Azure Database for PostgreSQL Enkele server wordt ingesteld op serverniveau. Voor een bepaalde server wordt een door de klant beheerde sleutel, de sleutelversleutelingssleutel (KEK), gebruikt voor het versleutelen van de gegevensversleutelingssleutel (DEK) die door de service wordt gebruikt. De KEK is een asymmetrische sleutel die is opgeslagen in een exemplaar dat eigendom is van de klant en door de klant [Azure Key Vault](../key-vault/general/security-overview.md) beheerd. De Sleutelversleutelingssleutel (KEK) en gegevensversleutelingssleutel (DEK) worden verder in dit artikel gedetailleerder beschreven.

Key Vault is een extern sleutelbeheersysteem in de cloud. Het is zeer beschikbaar en biedt schaalbare, veilige opslag voor cryptografische RSA-sleutels, eventueel met ondersteuning van FIPS 140-2 Level 2-gevalideerde hardwarebeveiligingsmodules (HMS's). Het biedt geen directe toegang tot een opgeslagen sleutel, maar biedt wel services voor versleuteling en ontsleuteling voor geautoriseerde entiteiten. Key Vault kunt de sleutel genereren, importeren of deze laten overgedragen van een [on-premises HSM-apparaat.](../key-vault/keys/hsm-protected-keys.md)

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for PostgreSQL Enkele server ondersteuning biedt voor de prijscategorie 'Algemeen' en 'Geoptimaliseerd voor geheugen'. Raadpleeg de beperkingssectie voor [andere](concepts-data-encryption-postgresql.md#limitations) beperkingen.

## <a name="benefits"></a>Voordelen

Gegevensversleuteling met door de klant beheerde sleutels Azure Database for PostgreSQL Enkele server biedt de volgende voordelen:

* Gegevenstoegang wordt volledig beheerd door de mogelijkheid om de sleutel te verwijderen en de database ontoegankelijk te maken 
*    Volledige controle over de levenscyclus van sleutels, inclusief roulatie van de sleutel om deze af te stemmen op het bedrijfsbeleid
*    Centraal beheer en de organisatie van sleutels in Azure Key Vault
*    Mogelijkheid om scheiding van taken tussen beveiligingsmedewerkers en DBA en systeembeheerders te implementeren

## <a name="terminology-and-description"></a>Terminologie en beschrijving

**Gegevensversleutelingssleutel (DEK)**: een symmetrische AES256-sleutel die wordt gebruikt voor het versleutelen van een partitie of gegevensblok. Het versleutelen van elk gegevensblok met een andere sleutel maakt crypto-analyseaanvallen moeilijker. Toegang tot DEK's is vereist voor de resourceprovider of het toepassings exemplaar dat een specifiek blok versleutelt en ontsleutelt. Wanneer u een DEK vervangt door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.

**Sleutelversleutelingssleutel (KEK)**: een versleutelingssleutel die wordt gebruikt om de DEK's te versleutelen. Met een KEK die nooit Key Vault kunnen de DEK's zelf worden versleuteld en beheerd. De entiteit die toegang heeft tot de KEK kan anders zijn dan de entiteit die de DEK vereist. Omdat de KEK is vereist voor het ontsleutelen van de DEK's, is de KEK in de eerste plaats een enkel punt waarop DEK's effectief kunnen worden verwijderd door de KEK te verwijderen.

De DEK's, versleuteld met de KK's, worden afzonderlijk opgeslagen. Alleen een entiteit met toegang tot de KEK kan deze DEK's ontsleutelen. Zie Security in encryption at rest (Beveiliging [in versleuteling-at-rest) voor meer informatie.](../security/fundamentals/encryption-atrest.md)

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Hoe gegevensversleuteling met een door de klant beheerde sleutel werkt

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="Diagram met een overzicht van Bring Your Own Key":::

Voor een PostgreSQL-server die door de klant beheerde sleutels gebruikt die zijn opgeslagen in Key Vault voor versleuteling van de DEK, geeft een Key Vault-beheerder de volgende toegangsrechten voor de server:

* **get:** voor het ophalen van het openbare gedeelte en de eigenschappen van de sleutel in de sleutelkluis.
* **wrapKey:** om de DEK te kunnen versleutelen. De versleutelde DEK wordt opgeslagen in de Azure Database for PostgreSQL.
* **unwrapKey:** om de DEK te kunnen ontsleutelen. Azure Database for PostgreSQL de ontsleutelde DEK nodig om de gegevens te versleutelen/ontsleutelen

De sleutelkluisbeheerder kan ook [logboekregistratie van Key Vault auditgebeurtenissen](../azure-monitor/insights/key-vault-insights-overview.md)inschakelen, zodat deze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd voor het gebruik van de door de klant beheerde sleutel die is opgeslagen in de sleutelkluis, verzendt de server de DEK naar de sleutelkluis voor versleuteling. Key Vault retourneert de versleutelde DEK, die is opgeslagen in de gebruikersdatabase. Zo nodig verzendt de server ook de beveiligde DEK naar de sleutelkluis voor ontsleuteling. Auditors kunnen deze Azure Monitor om Key Vault auditgebeurtenislogboeken te controleren als logboekregistratie is ingeschakeld.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Vereisten voor het configureren van gegevensversleuteling voor Azure Database for PostgreSQL enkele server

Hier volgen de vereisten voor het configureren van Key Vault:

* Key Vault en Azure Database for PostgreSQL Enkele server moeten deel uitmaken van dezelfde Azure Active Directory (Azure AD)-tenant. Interacties tussen Key Vault tenants en servers worden niet ondersteund. Als u Key Vault resource daarna wilt verplaatsen, moet u de gegevensversleuteling opnieuw configureren.
* De sleutelkluis moet worden ingesteld met 90 dagen voor 'Dagen om verwijderde kluizen te behouden'. Als de bestaande sleutelkluis is geconfigureerd met een lager nummer, moet u een nieuwe sleutelkluis maken omdat deze niet kan worden gewijzigd nadat deze is gemaakt.
* Schakel de functie voor het verwijderen van de sleutelkluis in om u te beschermen tegen gegevensverlies als er per ongeluk een sleutel (of Key Vault) wordt verwijderd. Soft-deleted resources worden 90 dagen bewaard, tenzij de gebruiker ze in de tussentijd herstelt of opsist. De herstel- en ops purge-acties hebben hun eigen machtigingen die zijn gekoppeld aan een Key Vault toegangsbeleid. De functie voor het verwijderen van de functie voor zacht verwijderen is standaard uitgeschakeld, maar u kunt deze inschakelen via PowerShell of de Azure CLI (u kunt deze functie niet inschakelen via de Azure Portal). 
* Beveiliging tegen leeg maken inschakelen om een verplichte bewaarperiode af te dwingen voor verwijderde kluizen en kluisobjecten
* Verleen de Azure Database for PostgreSQL Enkele server toegang tot de sleutelkluis met de machtigingen get, wrapKey en unwrapKey met behulp van de unieke beheerde identiteit. In de Azure Portal wordt de unieke service-identiteit automatisch gemaakt wanneer gegevensversleuteling is ingeschakeld op de PostgreSQL Single-server. Zie [Gegevensversleuteling voor Azure Database for PostgreSQL Enkele server](howto-data-encryption-portal.md) met behulp van de Azure Portal voor gedetailleerde, stapsgewijs instructies wanneer u de Azure Portal.

Hier volgen de vereisten voor het configureren van de door de klant beheerde sleutel:

* De door de klant beheerde sleutel die moet worden gebruikt voor het versleutelen van de DEK kan alleen asymmetrisch zijn, RSA 2048.
* De activeringsdatum van de sleutel (indien ingesteld) moet een datum en tijd in het verleden zijn. De vervaldatum (indien ingesteld) moet een toekomstige datum en tijd zijn.
* De sleutel moet de status *Ingeschakeld* hebben.
* Als u een [bestaande sleutel importeert](/rest/api/keyvault/ImportKey/ImportKey) in de sleutelkluis, moet u deze in de ondersteunde bestandsindelingen `.pfx` (, , , ) `.byok` `.backup` invoeren.

## <a name="recommendations"></a>Aanbevelingen

Wanneer u gegevensversleuteling gebruikt met behulp van een door de klant beheerde sleutel, vindt u hier aanbevelingen voor het configureren van Key Vault:

* Stel een resourcevergrendeling in Key Vault om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of niet-geautoriseerde verwijdering te voorkomen.
* Schakel controle en rapportage over alle versleutelingssleutels in. Key Vault logboeken die eenvoudig kunnen worden gebruikt in andere hulpprogramma's voor beveiligingsinformatie en gebeurtenisbeheer. Azure Monitor Log Analytics is een voorbeeld van een service die al is geïntegreerd.
* Zorg ervoor dat Key Vault en Azure Database for PostgreSQL Enkele server zich in dezelfde regio bevinden, zodat u sneller toegang hebt tot DEK-wraps en uitpakbewerkingen.
* Vergrendel de Azure KeyVault tot alleen **privé-eindpunt** en geselecteerde netwerken en sta alleen vertrouwde *Microsoft-services* toe om de resources te beveiligen.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Hier volgen aanbevelingen voor het configureren van een door de klant beheerde sleutel:

* Bewaar een kopie van de door de klant beheerde sleutel op een veilige plaats of escrow deze naar de escrow-service.

* Als Key Vault sleutel genereert, maakt u een back-up van de sleutel voordat u de sleutel voor de eerste keer gebruikt. U kunt de back-up alleen herstellen naar Key Vault. Zie [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey)voor meer informatie over de back-upopdracht.

## <a name="inaccessible-customer-managed-key-condition"></a>Niet-toegankelijke door de klant beheerde sleutelvoorwaarde

Wanneer u gegevensversleuteling configureert met een door de klant beheerde sleutel in Key Vault, is continue toegang tot deze sleutel vereist om de server online te houden. Als de server geen toegang meer heeft tot de door de klant beheerde sleutel in Key Vault, begint de server binnen tien minuten alle verbindingen te weigeren. De server geeft een bijbehorend foutbericht uit en wijzigt de status van de server *in Niet-toegankelijk.* Enkele van de redenen waarom de server deze status kan bereiken, zijn:

* Als we een server voor herstel naar een bepaald tijdstip maken voor uw Azure Database for PostgreSQL Single-server, waarvoor gegevensversleuteling is ingeschakeld, heeft de zojuist gemaakte server de status *Niet toegankelijk.* U kunt de status van de server herstellen [via Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) of [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Als we een leesreplica maken voor uw Azure Database for PostgreSQL Enkele server, waarvoor gegevensversleuteling is ingeschakeld, heeft de replicaserver de status *Niet toegankelijk.* U kunt de status van de server herstellen [via Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) of [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Als u KeyVault verwijdert, heeft Azure Database for PostgreSQL server geen toegang tot de sleutel en krijgt deze de status *Niet-toegankelijk.* Herstel de [Key Vault](../key-vault/general/key-vault-recovery.md) en de gegevensversleuteling opnieuw om de server beschikbaar *te maken.*
* Als we de sleutel uit keyVault verwijderen, heeft de Azure Database for PostgreSQL Enkele server geen toegang tot de sleutel en krijgt deze de status *Niet-toegankelijk.* Herstel de [sleutel en](../key-vault/general/key-vault-recovery.md) bevalid de gegevensversleuteling opnieuw om de server beschikbaar *te maken.*
* Als de sleutel die is opgeslagen in De Azure KeyVault verloopt, wordt de sleutel ongeldig en wordt de Azure Database for PostgreSQL Enkele server overge zetten naar de status Niet *toegankelijk.* Breid de vervaldatum van de sleutel uit met [behulp van CLI](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) en vervolgens de gegevensversleuteling opnieuw om de server beschikbaar te *maken.*

### <a name="accidental-key-access-revocation-from-key-vault"></a>Onopzettelijk intrekken van toegang tot sleutels Key Vault

Het kan gebeuren dat iemand met voldoende toegangsrechten om Key Vault servertoegang tot de sleutel per ongeluk uit te schakelen door:

* De machtigingen get, wrapKey en unwrapKey van de sleutelkluis van de server inroepen.
* De sleutel verwijderen.
* De sleutelkluis verwijderen.
* De firewallregels van de sleutelkluis wijzigen.

* De beheerde identiteit van de server in Azure AD verwijderen.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>De door de klant beheerde sleutel in Key Vault

Configureer de volgende Azure-functies om de databasetoestand te bewaken en waarschuwingen in te stellen voor het verlies van toegang tot transparent data encryption-beveiliging:

* [Azure Resource Health:](../service-health/resource-health-overview.md)een niet-toegankelijke database die geen toegang meer heeft tot de klantsleutel wordt als 'Niet toegankelijk' beschouwd nadat de eerste verbinding met de database is geweigerd.
* [Activiteitenlogboek:](../service-health/alerts-activity-log-service-notifications-portal.md)wanneer de toegang tot de klantsleutel in de door de klant beheerde Key Vault mislukt, worden vermeldingen toegevoegd aan het activiteitenlogboek. U kunt de toegang zo snel mogelijk opnieuw instellen als u waarschuwingen voor deze gebeurtenissen maakt.

* [Actiegroepen:](../azure-monitor/alerts/action-groups.md)definieer deze groepen om u meldingen en waarschuwingen te sturen op basis van uw voorkeuren.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Herstellen en repliceren met de beheerde sleutel van een klant in Key Vault

Nadat Azure Database for PostgreSQL Single-server is versleuteld met de beheerde sleutel van een klant die is opgeslagen in Key Vault, wordt elke nieuwe kopie van de server ook versleuteld. U kunt deze nieuwe kopie maken via een lokale of geo-herstelbewerking of via leesreplica's. De kopie kan echter worden gewijzigd om de beheerde sleutel van een nieuwe klant voor versleuteling weer te geven. Wanneer de door de klant beheerde sleutel wordt gewijzigd, gebruiken oude back-ups van de server de nieuwste sleutel.

Om problemen te voorkomen tijdens het instellen van door de klant beheerde gegevensversleuteling tijdens het herstellen of maken van leesreplica's, is het belangrijk dat u deze stappen volgt op de primaire en herstelde/replicaservers:

* Start het proces voor het herstellen of lezen van replica's vanaf de primaire Azure Database for PostgreSQL enkele server.
* Houd de zojuist gemaakte server (hersteld/replica) in een niet-toegankelijke status, omdat de unieke identiteit nog niet is Key Vault.
* Op de herstelde/replicaserver moet u de door de klant beheerde sleutel opnieuwvalideren in de instellingen voor gegevensversleuteling. Dit zorgt ervoor dat de zojuist gemaakte server wrap- en unwrap-machtigingen krijgt voor de sleutel die is opgeslagen in Key Vault.

## <a name="limitations"></a>Beperkingen

Voor Azure Database for PostgreSQL heeft de ondersteuning voor versleuteling van data-at-rest met behulp van een door klanten beheerde sleutel (CMK) enkele beperkingen:

* Ondersteuning voor deze functionaliteit is beperkt tot **Algemeen** **prijscategorie** en geoptimaliseerd voor geheugen.
* Deze functie wordt alleen ondersteund in regio's en servers die opslag ondersteunen tot 16 TB. Raadpleeg de sectie Opslag in de documentatie hier voor een lijst met Azure-regio's die ondersteuning biedt voor opslag tot 16 [TB](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Alle nieuwe PostgreSQL-servers die zijn gemaakt in de hierboven vermelde regio's, bieden ondersteuning voor versleuteling met sleutels voor **klantbeheer.** Herstel naar een bepaald tijdstip (PITR) of leesreplica komt niet in aanmerking, maar in theorie zijn ze 'nieuw'.
    > - Als u wilt controleren of uw inrichtende server ondersteuning biedt voor maximaal 16 TB, gaat u naar de blade Prijscategorie in de portal en bekijkt u de maximale opslaggrootte die wordt ondersteund door de inrichtende server. Als u de schuifregelaar naar 4 TB kunt verplaatsen, biedt uw server mogelijk geen ondersteuning voor versleuteling met door de klant beheerde sleutels. De gegevens worden echter te allen tijde versleuteld met behulp van door de service beheerde sleutels. Neem contact op met AskAzureDBforPostgreSQL@service.microsoft.com als u vragen hebt.

* Versleuteling wordt alleen ondersteund met de cryptografische RSA 2048-sleutel.

## <a name="next-steps"></a>Volgende stappen

Informatie over het instellen van gegevensversleuteling met een door de klant beheerde sleutel voor uw [Azure Database for PostgreSQL Single-server](howto-data-encryption-portal.md)met behulp van de Azure Portal .
