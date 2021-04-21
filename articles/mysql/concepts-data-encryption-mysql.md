---
title: Gegevensversleuteling met door de klant beheerde sleutel - Azure Database for MySQL
description: Azure Database for MySQL gegevensversleuteling met een door de klant beheerde sleutel kunt u Bring Your Own Key (BYOK) gebruiken voor data-at-rest-beveiliging. Daarnaast kunnen organisaties hiermee een scheiding van taken implementeren bij het beheer van sleutels en gegevens.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: bed0ccbc25c6fcc43d8fb0948182f229bce63edf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764707"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL gegevensversleuteling met een door de klant beheerde sleutel

Gegevensversleuteling met door de klant beheerde sleutels voor Azure Database for MySQL stelt u in staat om inactieve gegevens te beveiligen met BYOK (Bring Your Own Key). Daarnaast kunnen organisaties hiermee een scheiding van taken implementeren bij het beheer van sleutels en gegevens. Met door de klant beheerde versleuteling bent u verantwoordelijk voor, en hebt u het volledige beheer over, de levenscyclus van een sleutel, de machtigingen voor sleutelgebruik, en het controleren van de bewerkingen van sleutels.

Gegevensversleuteling met door de klant beheerde Azure Database for MySQL, wordt ingesteld op serverniveau. Voor een bepaalde server wordt een door de klant beheerde sleutel, de sleutelversleutelingssleutel (KEK) genoemd, gebruikt voor het versleutelen van de gegevensversleutelingssleutel (DEK) die door de service wordt gebruikt. De KEK is een asymmetrische sleutel die is opgeslagen in een exemplaar dat eigendom is van de klant en door de klant [Azure Key Vault](../key-vault/general/security-overview.md) beheerd. De Sleutelversleutelingssleutel (KEK) en de DEK (Data Encryption Key) worden verder in dit artikel gedetailleerder beschreven.

Key Vault is een extern sleutelbeheersysteem in de cloud. Het is zeer beschikbaar en biedt schaalbare, veilige opslag voor cryptografische RSA-sleutels, optioneel met ondersteuning van FIPS 140-2 Level 2-gevalideerde hardwarebeveiligingsmodules (HMS's). Het biedt geen directe toegang tot een opgeslagen sleutel, maar biedt wel services voor versleuteling en ontsleuteling voor geautoriseerde entiteiten. Key Vault kunt de sleutel genereren, importeren of deze laten overgedragen vanaf een [on-premises HSM-apparaat.](../key-vault/keys/hsm-protected-keys.md)

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for MySQL prijslagen 'Algemeen' en 'Geoptimaliseerd voor geheugen' ondersteunt. Raadpleeg de beperkingssectie voor [andere](concepts-data-encryption-mysql.md#limitations) beperkingen.

## <a name="benefits"></a>Voordelen

Gegevensversleuteling met door de klant beheerde Azure Database for MySQL biedt de volgende voordelen:

* Gegevenstoegang wordt volledig beheerd door de mogelijkheid om de sleutel te verwijderen en de database ontoegankelijk te maken 
* Volledige controle over de levenscyclus van sleutels, inclusief roulatie van de sleutel voor afstemming met bedrijfsbeleid
* Centraal beheer en de organisatie van sleutels in Azure Key Vault
* Mogelijkheid om scheiding van taken tussen beveiligingsmedewerkers en DBA en systeembeheerders te implementeren


## <a name="terminology-and-description"></a>Terminologie en beschrijving

**Gegevensversleutelingssleutel (DEK)**: een symmetrische AES256-sleutel die wordt gebruikt voor het versleutelen van een partitie of gegevensblok. Het versleutelen van elk gegevensblok met een andere sleutel maakt crypto-analyseaanvallen moeilijker. Toegang tot DEK's is vereist voor de resourceprovider of het toepassings exemplaar dat een specifiek blok versleutelt en ontsleutelt. Wanneer u een DEK vervangt door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.

**Sleutelversleutelingssleutel (KEK)**: een versleutelingssleutel die wordt gebruikt om de DEK's te versleutelen. Met een KEK die nooit Key Vault kunnen de DEK's zelf worden versleuteld en beheerd. De entiteit die toegang heeft tot de KEK kan anders zijn dan de entiteit die de DEK vereist. Omdat de KEK vereist is voor het ontsleutelen van de DEK's, is de KEK in de eerste plaats een enkel punt waarop DEK's effectief kunnen worden verwijderd door de KEK te verwijderen.

De DEK's, versleuteld met de KK's, worden afzonderlijk opgeslagen. Alleen een entiteit met toegang tot de KEK kan deze DEK's ontsleutelen. Zie Security in encryption at rest (Beveiliging [in versleuteling-at-rest) voor meer informatie.](../security/fundamentals/encryption-atrest.md)

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Hoe gegevensversleuteling met een door de klant beheerde sleutel werkt

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Diagram met een overzicht van Bring Your Own Key":::

Voor een MySQL-server die door de klant beheerde sleutels gebruikt die zijn opgeslagen in Key Vault voor versleuteling van de DEK, geeft een Key Vault-beheerder de volgende toegangsrechten voor de server:

* **get:** voor het ophalen van het openbare gedeelte en de eigenschappen van de sleutel in de sleutelkluis.
* **wrapKey:** om de DEK te kunnen versleutelen. De versleutelde DEK wordt opgeslagen in de Azure Database for MySQL.
* **unwrapKey:** om de DEK te kunnen ontsleutelen. Azure Database for MySQL de ontsleutelde DEK nodig om de gegevens te versleutelen/ontsleutelen

De sleutelkluisbeheerder kan ook [logboekregistratie van Key Vault auditgebeurtenissen](../azure-monitor/insights/key-vault-insights-overview.md)inschakelen, zodat deze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd voor het gebruik van de door de klant beheerde sleutel die is opgeslagen in de sleutelkluis, verzendt de server de DEK naar de sleutelkluis voor versleuteling. Key Vault retourneert de versleutelde DEK, die is opgeslagen in de gebruikersdatabase. Zo nodig verzendt de server ook de beveiligde DEK naar de sleutelkluis voor ontsleuteling. Auditors kunnen deze Azure Monitor om Key Vault auditgebeurtenislogboeken te controleren, als logboekregistratie is ingeschakeld.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Vereisten voor het configureren van gegevensversleuteling voor Azure Database for MySQL

Hier volgen de vereisten voor het configureren Key Vault:

* Key Vault en Azure Database for MySQL moeten deel uitmaken van dezelfde Azure Active Directory (Azure AD)-tenant. Interacties tussen Key Vault tenants en servers worden niet ondersteund. Als u Key Vault resource verplaatst, moet u de gegevensversleuteling opnieuw configureren.
* Schakel de [functie voor](../key-vault/general/soft-delete-overview.md) het verwijderen van gegevens in voor de sleutelkluis met een retentieperiode van **90** dagen om te beschermen tegen gegevensverlies als er een onbedoelde sleutel (of Key Vault) wordt verwijderd. Soft-deleted resources worden standaard 90 dagen bewaard, tenzij de retentieperiode expliciet is ingesteld op <=90 dagen. Aan de herstel- en opstingsacties zijn eigen machtigingen gekoppeld in een Key Vault-toegangsbeleid. De functie voor zacht verwijderen is standaard uitgeschakeld, maar u kunt deze inschakelen via PowerShell of de Azure CLI (u kunt deze functie niet inschakelen via de Azure Portal).
* Schakel de [functie Beveiliging opsluizen](../key-vault/general/soft-delete-overview.md#purge-protection) in voor de sleutelkluis met een retentieperiode die is ingesteld **op 90 dagen.** Beveiliging tegen opsisten kan alleen worden ingeschakeld zodra de functie voor het verwijderen van de functie voor het verwijderen van gegevens is ingeschakeld. Deze kan worden ingeschakeld via Azure CLI of PowerShell. Wanneer beveiliging tegen opsluizen is aangegaan, kan een kluis of een object met de verwijderde status pas worden verwijderd als de bewaarperiode is verstreken. Soft-leted kluizen en objecten kunnen nog steeds worden hersteld, zodat het bewaarbeleid wordt gevolgd. 
* Verleen de Azure Database for MySQL toegang tot de sleutelkluis met de machtigingen get, wrapKey en unwrapKey met behulp van de unieke beheerde identiteit. In de Azure Portal wordt de unieke service-identiteit automatisch gemaakt wanneer gegevensversleuteling is ingeschakeld op de MySQL. Zie Configure data encryption for MySQL (Gegevensversleuteling configureren voor [MySQL)](howto-data-encryption-portal.md) voor gedetailleerde, stapsgewijs instructies wanneer u de Azure Portal.

Hier volgen de vereisten voor het configureren van de door de klant beheerde sleutel:

* De door de klant beheerde sleutel die moet worden gebruikt voor het versleutelen van de DEK kan alleen asymmetrisch zijn, RSA 2048.
* De activeringsdatum van de sleutel (indien ingesteld) moet een datum en tijd in het verleden zijn. De vervaldatum is niet ingesteld.
* De sleutel moet de status *Ingeschakeld* hebben.
* Voor de sleutel moet [een zachte verwijderperiode](../key-vault/general/soft-delete-overview.md) zijn ingesteld op **90 dagen.** Hiermee wordt impliciet het vereiste sleutelkenmerk recoveryLevel: Herstelbaar. Als de retentie is ingesteld op < 90 dagen, wordt het recoveryLevel: 'CustomizedRecoverable' (Aangepast Herstelbaar) dat niet de vereiste is, dus zorg ervoor dat u de retentieperiode in te stellen is ingesteld op **90 dagen.**
* Op de sleutel moet [opstingsbeveiliging zijn ingeschakeld.](../key-vault/general/soft-delete-overview.md#purge-protection)
* Als u een [bestaande sleutel importeert](/rest/api/keyvault/ImportKey/ImportKey) in de sleutelkluis, moet u deze in de ondersteunde bestandsindelingen ( `.pfx` , , ) `.byok` `.backup` invoeren.

## <a name="recommendations"></a>Aanbevelingen

Wanneer u gegevensversleuteling gebruikt met behulp van een door de klant beheerde sleutel, vindt u hier aanbevelingen voor het configureren van Key Vault:

* Stel een resourcevergrendeling in Key Vault om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of niet-geautoriseerde verwijdering te voorkomen.
* Schakel controle en rapportage over alle versleutelingssleutels in. Key Vault logboeken die eenvoudig kunnen worden gebruikt in andere hulpprogramma's voor beveiligingsinformatie en gebeurtenisbeheer. Azure Monitor Log Analytics is een voorbeeld van een service die al is geïntegreerd.
* Zorg ervoor Key Vault en Azure Database for MySQL zich in dezelfde regio bevinden, zodat u sneller toegang hebt tot DEK-wraps en uitpakbewerkingen.
* Vergrendel De Azure KeyVault tot alleen **privé-eindpunten** en geselecteerde netwerken en sta alleen vertrouwde *Microsoft-services* toe om de resources te beveiligen.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Hier volgen aanbevelingen voor het configureren van een door de klant beheerde sleutel:

* Bewaar een kopie van de door de klant beheerde sleutel op een veilige plaats of escrow deze naar de escrow-service.

* Als Key Vault sleutel genereert, maakt u een back-up van de sleutel voordat u de sleutel voor de eerste keer gebruikt. U kunt de back-up alleen herstellen naar Key Vault. Zie [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey)voor meer informatie over de back-upopdracht.

## <a name="inaccessible-customer-managed-key-condition"></a>Niet-toegankelijke door de klant beheerde sleutelvoorwaarde

Wanneer u gegevensversleuteling configureert met een door de klant beheerde sleutel in Key Vault, is continue toegang tot deze sleutel vereist om de server online te houden. Als de server geen toegang meer heeft tot de door de klant beheerde sleutel in Key Vault, begint de server binnen tien minuten alle verbindingen te weigeren. De server geeft een bijbehorend foutbericht uit en wijzigt de status van de server *in Niet-toegankelijk.* Enkele van de redenen waarom de server deze status kan bereiken, zijn:

* Als we een server voor herstel naar een bepaald tijdstip maken voor uw Azure Database for MySQL, waarvoor gegevensversleuteling is ingeschakeld, heeft de zojuist gemaakte server de status *Niet toegankelijk.* U kunt dit oplossen via [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) of [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Als we een leesreplica voor uw Azure Database for MySQL, waarvoor gegevensversleuteling is ingeschakeld, heeft de replicaserver de status *Niet toegankelijk.* U kunt dit oplossen via [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) of [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Als u de KeyVault verwijdert, heeft Azure Database for MySQL geen toegang tot de sleutel en krijgt deze de status *Niet-toegankelijk.* Herstel de [Key Vault](../key-vault/general/key-vault-recovery.md) en bevalid de gegevensversleuteling opnieuw om de server beschikbaar *te maken.*
* Als we de sleutel uit keyVault verwijderen, heeft Azure Database for MySQL geen toegang tot de sleutel en krijgt deze de status *Niet-toegankelijk.* Herstel de [sleutel en](../key-vault/general/key-vault-recovery.md) bevalid de gegevensversleuteling opnieuw om de server beschikbaar *te maken.*
* Als de sleutel die is opgeslagen in de Azure KeyVault verloopt, wordt de sleutel ongeldig en Azure Database for MySQL over in de status *Niet-toegankelijk.* Breid de vervaldatum van de sleutel uit met [behulp van CLI](/cli/azure/keyvault/key#az_keyvault_key_set-attributes) en vervolgens de gegevensversleuteling opnieuw om de server beschikbaar te *maken.*

### <a name="accidental-key-access-revocation-from-key-vault"></a>Onopzettelijk intrekken van toegang tot sleutels Key Vault

Het kan gebeuren dat iemand met voldoende toegangsrechten om Key Vault servertoegang tot de sleutel per ongeluk uit te schakelen door:

* De machtigingen get, wrapKey en unwrapKey van de sleutelkluis van de server inroepen.
* De sleutel verwijderen.
* De sleutelkluis verwijderen.
* De firewallregels van de sleutelkluis wijzigen.
* De beheerde identiteit van de server in Azure AD verwijderen.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>De door de klant beheerde sleutel in Key Vault

Configureer de volgende Azure-functies om de databasetoestand te bewaken en waarschuwingen in te stellen voor het verlies van toegang tot de transparent data encryption-beveiliging:

* [Azure Resource Health:](../service-health/resource-health-overview.md)een niet-toegankelijke database die geen toegang meer heeft tot de klantsleutel wordt als 'Niet toegankelijk' beschouwd nadat de eerste verbinding met de database is geweigerd.
* [Activiteitenlogboek:](../service-health/alerts-activity-log-service-notifications-portal.md)wanneer de toegang tot de klantsleutel in de door de klant beheerde Key Vault mislukt, worden vermeldingen toegevoegd aan het activiteitenlogboek. U kunt de toegang zo snel mogelijk opnieuw instellen als u waarschuwingen voor deze gebeurtenissen maakt.

* [Actiegroepen:](../azure-monitor/alerts/action-groups.md)definieer deze groepen om u meldingen en waarschuwingen te sturen op basis van uw voorkeuren.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Herstellen en repliceren met de beheerde sleutel van een klant in Key Vault

Nadat Azure Database for MySQL is versleuteld met de beheerde sleutel van een klant die is opgeslagen in Key Vault, wordt elke nieuw gemaakte kopie van de server ook versleuteld. U kunt deze nieuwe kopie maken via een lokale of geo-herstelbewerking of via leesreplica's. De kopie kan echter worden gewijzigd om de beheerde sleutel van een nieuwe klant voor versleuteling weer te geven. Wanneer de door de klant beheerde sleutel wordt gewijzigd, gebruiken oude back-ups van de server de nieuwste sleutel.

Om problemen te voorkomen bij het instellen van door de klant beheerde gegevensversleuteling tijdens het herstellen of maken van leesreplica's, is het belangrijk dat u deze stappen volgt op de bron- en herstelde/replicaservers:

* Start het proces voor het maken van de herstel- of leesreplica vanuit de Azure Database for MySQL.
* Zorg ervoor dat de zojuist gemaakte server (hersteld/replica) niet toegankelijk is, omdat de unieke identiteit nog geen machtigingen heeft gekregen voor Key Vault.
* Op de herstelde/replicaserver moet u de door de klant beheerde sleutel opnieuwvalideren in de instellingen voor gegevensversleuteling om ervoor te zorgen dat de zojuist gemaakte server wrap- en unwrap-machtigingen krijgt voor de sleutel die is opgeslagen in Key Vault.

## <a name="limitations"></a>Beperkingen

Voor Azure Database for MySQL heeft de ondersteuning voor versleuteling van data-at-rest met behulp van een door klanten beheerde sleutel (CMK) enkele beperkingen:

* Ondersteuning voor deze functionaliteit is beperkt **tot** Algemeen prijscategorie en geoptimaliseerd **voor** geheugen.
* Deze functie wordt alleen ondersteund in regio's en servers die opslag ondersteunen tot 16 TB. Raadpleeg de sectie opslag in de documentatie hier voor een lijst met Azure-regio's die ondersteuning biedt voor opslag tot 16 [TB](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Alle nieuwe MySQL-servers die zijn gemaakt in de hierboven vermelde regio's, bieden ondersteuning voor versleuteling met sleutels voor **klantbeheer.** Herstel naar een bepaald tijdstip (PITR) of leesreplica komt niet in aanmerking, maar in theorie zijn ze 'nieuw'.
    > - Als u wilt controleren of uw inrichtende server ondersteuning biedt voor maximaal 16 TB, gaat u naar de blade Prijscategorie in de portal en bekijkt u de maximale opslaggrootte die wordt ondersteund door de inrichtende server. Als u de schuifregelaar naar 4 TB kunt verplaatsen, biedt uw server mogelijk geen ondersteuning voor versleuteling met door de klant beheerde sleutels. De gegevens worden echter te allen tijde versleuteld met behulp van door de service beheerde sleutels. Neem contact op met AskAzureDBforMySQL@service.microsoft.com als u vragen hebt.

* Versleuteling wordt alleen ondersteund met de cryptografische RSA 2048-sleutel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van gegevensversleuteling met een door de klant beheerde sleutel voor uw Azure-database voor MySQL met behulp van [de Azure Portal](howto-data-encryption-portal.md) en [Azure CLI.](howto-data-encryption-cli.md)
