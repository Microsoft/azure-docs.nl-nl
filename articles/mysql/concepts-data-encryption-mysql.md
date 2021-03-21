---
title: Gegevens versleuteling met door de klant beheerde sleutel-Azure Database for MySQL
description: Azure Database for MySQL gegevens versleuteling met een door de klant beheerde sleutel, kunt u Bring Your Own Key (BYOK) voor gegevens bescherming in rust. Daarnaast kunnen organisaties hiermee een scheiding van taken implementeren bij het beheer van sleutels en gegevens.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 4c8f4b490c46ed8061201ba6362999f0e426ecb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596330"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Gegevens versleuteling Azure Database for MySQL met een door de klant beheerde sleutel

Gegevensversleuteling met door de klant beheerde sleutels voor Azure Database for MySQL stelt u in staat om inactieve gegevens te beveiligen met BYOK (Bring Your Own Key). Daarnaast kunnen organisaties hiermee een scheiding van taken implementeren bij het beheer van sleutels en gegevens. Met door de klant beheerde versleuteling bent u verantwoordelijk voor, en hebt u het volledige beheer over, de levenscyclus van een sleutel, de machtigingen voor sleutelgebruik, en het controleren van de bewerkingen van sleutels.

Gegevens versleuteling met door de klant beheerde sleutels voor Azure Database for MySQL is ingesteld op server niveau. Voor een bepaalde server wordt een door de klant beheerde sleutel, de sleutel versleutelings sleutel (KEK), gebruikt om de gegevens versleutelings sleutel (DEK) te versleutelen die door de service wordt gebruikt. De KEK is een asymmetrische sleutel die is opgeslagen in een [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) exemplaar van een klant en door de klant wordt beheerd. De sleutel versleutelings sleutel (KEK) en de gegevens versleutelings sleutel (DEK) wordt verderop in dit artikel uitvoeriger beschreven.

Key Vault is een op de cloud gebaseerd extern systeem voor sleutel beheer. Het is Maxi maal beschikbaar en biedt schaal bare, veilige opslag voor RSA cryptografische sleutels, optioneel ondersteund door FIPS 140-2 level 2 gevalideerde hardware security modules (Hsm's). Het biedt geen directe toegang tot een opgeslagen sleutel, maar biedt ook services voor versleuteling en ontsleuteling naar gemachtigde entiteiten. Key Vault kunt de sleutel genereren, importeren of [laten overzetten van een on-premises HSM-apparaat](../key-vault/keys/hsm-protected-keys.md).

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for MySQL de prijs categorieën ' Algemeen ' en ' geoptimaliseerd voor geheugen ' ondersteunt. Raadpleeg de sectie [beperking](concepts-data-encryption-mysql.md#limitations) voor andere beperkingen.

## <a name="benefits"></a>Voordelen

Gegevens versleuteling met door de klant beheerde sleutels voor Azure Database for MySQL biedt de volgende voor delen:

* Gegevens toegang wordt volledig beheerd door u door de mogelijkheid om de sleutel te verwijderen en de data base ontoegankelijk te maken. 
* Volledige controle over de levens cyclus van de sleutel, inclusief het draaien van de sleutel die moet worden uitgelijnd met bedrijfs beleid
* Centraal beheer en organisatie van sleutels in Azure Key Vault
* Mogelijkheid om schei ding van taken te implementeren tussen beveiligings ambtenaren en DBA en systeem beheerders


## <a name="terminology-and-description"></a>Terminologie en beschrijving

**Gegevens versleutelings sleutel (dek)**: een symmetrische AES256-sleutel die wordt gebruikt om een partitie of gegevens blok te versleutelen. Het versleutelen van elk gegevens blok met een andere sleutel maakt crypto-analyse aanvallen lastiger. Toegang tot DEKs is vereist voor de resource provider of het toepassings exemplaar dat een specifiek blok versleutelt en ontsleutelt. Wanneer u een DEK vervangt door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.

Sleutel versleutelings **sleutel (KEK)**: een versleutelings sleutel die wordt gebruikt om de DEKs te versleutelen. Een KEK die nooit verlaat Key Vault laat de DEKs zelf versleutelen en beheren. De entiteit die toegang heeft tot de KEK, kan afwijken van de entiteit waarvoor de DEK is vereist. Omdat de KEK is vereist voor het ontsleutelen van de DEKs, is de KEK een specifiek punt waarmee DEKs effectief kan worden verwijderd door het verwijderen van de KEK.

De DEKs, versleuteld met de KEKs, worden afzonderlijk opgeslagen. Alleen een entiteit met toegang tot de KEK kan deze DEKs ontsleutelen. Zie [beveiliging in versleuteling op rest](../security/fundamentals/encryption-atrest.md)voor meer informatie.

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Hoe gegevens versleuteling met een door de klant beheerde sleutel werkt

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Diagram waarin een overzicht van Bring Your Own Key wordt weer gegeven":::

Voor een MySQL-server voor het gebruik van door de klant beheerde sleutels die zijn opgeslagen in Key Vault voor versleuteling van de DEK, geeft een Key Vault beheerder de volgende toegangs rechten voor de server:

* **ophalen**: voor het ophalen van het open bare onderdeel en de eigenschappen van de sleutel in de sleutel kluis.
* **wrapKey**: de dek kan worden versleuteld. De versleutelde DEK wordt opgeslagen in de Azure Database for MySQL.
* **sleutel uitpakken**: de dek kan worden ontsleuteld. Azure Database for MySQL moet de ontsleutelde DEK hebben om de gegevens te versleutelen/ontsleutelen

De sleutel kluis beheerder kan [logboek registratie van Key Vault controle gebeurtenissen ook inschakelen](../azure-monitor/insights/key-vault-insights-overview.md), zodat ze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd voor het gebruik van de door de klant beheerde sleutel die is opgeslagen in de sleutel kluis, verzendt de server de DEK naar de sleutel kluis voor versleuteling. Key Vault retourneert de versleutelde DEK, die wordt opgeslagen in de gebruikers database. Zo nodig verzendt de server de beveiligde DEK naar de sleutel kluis voor ontsleuteling. Audi tors kunnen Azure Monitor gebruiken om Key Vault controle gebeurtenis logboeken te controleren, als logboek registratie is ingeschakeld.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Vereisten voor het configureren van gegevens versleuteling voor Azure Database for MySQL

Hier volgen de vereisten voor het configureren van Key Vault:

* Key Vault en Azure Database for MySQL moeten deel uitmaken van dezelfde Azure Active Directory (Azure AD)-Tenant. Cross-Tenant Key Vault en server interacties worden niet ondersteund. Als u de Key Vault resource later wilt verplaatsen, moet u de gegevens versleuteling opnieuw configureren.
* Schakel de functie voor [voorlopig verwijderen](../key-vault/general/soft-delete-overview.md) in op de sleutel kluis waarbij de Bewaar periode is ingesteld op **90 dagen** om te beschermen tegen gegevens verlies als een onbedoelde sleutel (of Key Vault) wordt verwijderd. Voorlopig verwijderde resources worden standaard 90 dagen bewaard, tenzij de Bewaar periode expliciet is ingesteld op <= 90 dagen. De herstel-en opschoon acties hebben hun eigen machtigingen die zijn gekoppeld aan een Key Vault toegangs beleid. De functie voor voorlopig verwijderen is standaard uitgeschakeld, maar u kunt deze inschakelen via Power shell of de Azure CLI (Houd er rekening mee dat u deze niet via de Azure Portal hoeft in te scha kelen).
* Schakel de functie [beveiliging opschonen](../key-vault/general/soft-delete-overview.md#purge-protection) in op de sleutel kluis waarbij de Bewaar periode is ingesteld op **90 dagen**. Het opschonen van de beveiliging kan alleen worden ingeschakeld wanneer het voorlopig verwijderen is ingeschakeld. Deze kan worden ingeschakeld via Azure CLI of Power shell. Wanneer het leegmaken van de beveiliging is ingeschakeld, kan een kluis of een object in de verwijderde status pas worden verwijderd nadat de Bewaar periode is verstreken. Voorlopig verwijderde kluizen en objecten kunnen nog steeds worden hersteld, zodat het Bewaar beleid wordt gevolgd. 
* Verleen de Azure Database for MySQL toegang tot de sleutel kluis met de machtigingen Get, wrapKey en sleutel uitpakken met behulp van de unieke beheerde identiteit. In de Azure Portal wordt de unieke service-identiteit automatisch gemaakt wanneer gegevens versleuteling is ingeschakeld op de MySQL. Zie [Configure Data Encryption for MySQL](howto-data-encryption-portal.md) voor gedetailleerde stapsgewijze instructies voor het gebruik van de Azure Portal.

Hieronder vindt u de vereisten voor het configureren van de door de klant beheerde sleutel:

* De door de klant beheerde sleutel die moet worden gebruikt voor het versleutelen van de DEK kan alleen asymmetrisch zijn: RSA 2048.
* De datum en tijd waarop de sleutel wordt geactiveerd, moeten in het verleden liggen. De verval datum is niet ingesteld.
* De sleutel moet de *ingeschakelde* status hebben.
* Voor de sleutel moet [zacht verwijderen](../key-vault/general/soft-delete-overview.md) met de Bewaar periode zijn ingesteld op **90 dagen**. Hiermee wordt impliciet het vereiste sleutel kenmerk recoveryLevel: ' Recoverable ' ingesteld. Als de retentie is ingesteld op < 90 dagen, recoveryLevel: "CustomizedRecoverable", wat niet de vereiste is, dus zorg ervoor dat de Bewaar periode is ingesteld op **90 dagen**.
* Voor de sleutel moet de [beveiliging leegmaken zijn ingeschakeld](../key-vault/general/soft-delete-overview.md#purge-protection).
* Als u [een bestaande sleutel](/rest/api/keyvault/ImportKey/ImportKey) in de sleutel kluis importeert, moet u deze opgeven in de ondersteunde bestands indelingen ( `.pfx` , `.byok` , `.backup` ).

## <a name="recommendations"></a>Aanbevelingen

Wanneer u gegevens versleuteling gebruikt door gebruik te maken van een door de klant beheerde sleutel, zijn hier aanbevelingen voor het configureren van Key Vault:

* Stel een resource vergrendeling in op Key Vault om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of niet-geautoriseerde verwijdering te voor komen.
* Schakel controle en rapportage in voor alle versleutelings sleutels. Key Vault biedt logboeken die eenvoudig kunnen worden ingevoegd in andere hulpprogram ma's voor beveiligings informatie en beheer van gebeurtenissen. Azure Monitor Log Analytics is een voor beeld van een service die al is geïntegreerd.
* Zorg ervoor dat Key Vault en Azure Database for MySQL zich in dezelfde regio bevinden, zodat u snel toegang hebt tot DEK-terugloop en uitpakkende bewerkingen.
* Vergrendel de Azure-sleutel kluis alleen op **privé-eind punten en geselecteerde netwerken** en sta alleen *vertrouwde micro soft* -Services toe om de bronnen te beveiligen.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="Trusted-service-with-Azure":::

Hier vindt u aanbevelingen voor het configureren van een door de klant beheerde sleutel:

* Bewaar een kopie van de door de klant beheerde sleutel op een veilige plaats of borg aan de borg service.

* Als Key Vault de sleutel genereert, maakt u een sleutel back-up voordat u de sleutel voor de eerste keer gebruikt. U kunt de back-up alleen herstellen naar Key Vault. Zie [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey)voor meer informatie over de back-upopdracht.

## <a name="inaccessible-customer-managed-key-condition"></a>Niet-toegankelijke door de klant beheerde sleutel voorwaarde

Wanneer u gegevens versleuteling configureert met een door de klant beheerde sleutel in Key Vault, is continue toegang tot deze sleutel vereist voor de server om online te blijven. Als de server geen toegang meer heeft tot de door de klant beheerde sleutel in Key Vault, begint de server binnen 10 minuten alle verbindingen te weigeren. De server geeft een bijbehorend fout bericht en wijzigt de status van de server in niet- *toegankelijk*. Een van de redenen waarom de server deze status kan bereiken, is:

* Als er een herstel server voor een punt in de tijd wordt gemaakt voor uw Azure Database for MySQL waarvoor gegevens versleuteling is ingeschakeld, heeft de zojuist gemaakte server de status niet *toegankelijk* . U kunt dit oplossen via [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) of [cli](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Als we een lees replica maken voor uw Azure Database for MySQL waarvoor gegevens versleuteling is ingeschakeld, heeft de replica server de status niet *toegankelijk* . U kunt dit oplossen via [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) of [cli](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Als u de sleutel kluis verwijdert, is de Azure Database for MySQL niet in staat om toegang te krijgen tot de code en wordt deze verplaatst naar een niet- *toegankelijke* status. Herstel de [Key Vault](../key-vault/general/key-vault-recovery.md) en valideer de gegevens versleuteling om de server *beschikbaar* te maken.
* Als we de sleutel verwijderen uit de Azure Database for MySQL, kan de-sleutel geen toegang krijgen tot de code en wordt deze verplaatst naar een niet- *toegankelijke* status. Herstel de [sleutel](../key-vault/general/key-vault-recovery.md) en valideer de gegevens versleuteling om de server *beschikbaar* te maken.
* Als de sleutel die is opgeslagen in de Azure-hoofd kluis verloopt, wordt de sleutel ongeldig en wordt de Azure Database for MySQL overgezet naar de status *unaccessible* . Breid de verval datum van de sleutel uit met [cli](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) en valideer vervolgens de gegevens versleuteling om de server *beschikbaar* te maken.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Het intrekken van onbedoelde sleutel toegang van Key Vault

Het kan gebeuren dat iemand met voldoende toegangs rechten heeft Key Vault per ongeluk server toegang tot de sleutel uitschakelt door:

* De Get-, wrapKey-en sleutel uitpakken-machtigingen van de sleutel kluis intrekken van de server.
* De sleutel wordt verwijderd.
* De sleutel kluis verwijderen.
* De firewall regels van de sleutel kluis wijzigen.
* Het verwijderen van de beheerde identiteit van de server in azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>De door de klant beheerde sleutel in Key Vault bewaken

Configureer de volgende Azure-functies om de status van de data base te controleren en waarschuwingen in te scha kelen voor het verlies van transparante gegevens versleuteling beveiliging:

* [Azure resource Health](../service-health/resource-health-overview.md): een ontoegankelijke data base die de toegang tot de klant sleutel heeft verloren, wordt weer gegeven als ' niet toegankelijk ' nadat de eerste verbinding met de data base is geweigerd.
* [Activiteiten logboek](../service-health/alerts-activity-log-service-notifications-portal.md): wanneer de toegang tot de klant sleutel in de door de klant beheerde Key Vault mislukt, worden de gegevens toegevoegd aan het activiteiten logboek. U kunt zo snel mogelijk toegang herstellen als u waarschuwingen voor deze gebeurtenissen maakt.

* [Actie groepen](../azure-monitor/alerts/action-groups.md): Definieer deze groepen voor het verzenden van meldingen en waarschuwingen op basis van uw voor keuren.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Herstellen en repliceren met de beheerde sleutel van een klant in Key Vault

Nadat Azure Database for MySQL is versleuteld met een door de klant beheerde sleutel die is opgeslagen in Key Vault, wordt een nieuw gemaakt exemplaar van de server ook versleuteld. U kunt deze nieuwe kopie maken via een lokale of geo-herstel bewerking of via het lezen van replica's. De kopie kan echter worden gewijzigd in overeenstemming met de beheerde sleutel van een nieuwe klant voor versleuteling. Wanneer de door de klant beheerde sleutel wordt gewijzigd, beginnen oude back-ups van de server met de meest recente sleutel.

Als u problemen wilt voor komen tijdens het instellen van door de klant beheerde gegevens versleuteling tijdens het terugzetten of het maken van een replica, is het belang rijk dat u deze stappen volgt op de bron-en herstelde/replica-servers:

* Start het herstel-of het maken van de replica van de bron Azure Database for MySQL.
* Behoud de zojuist gemaakte server (hersteld/replica) in een niet-toegankelijke status, omdat de unieke identiteit nog geen machtigingen heeft gekregen om Key Vault.
* Valideer op de herstelde/replica-server de door de klant beheerde sleutel opnieuw in de instellingen voor gegevens versleuteling om ervoor te zorgen dat de zojuist gemaakte server terugloopt en de machtigingen voor het uitpakken van de sleutel in Key Vault.

## <a name="limitations"></a>Beperkingen

Voor Azure Database for MySQL is de ondersteuning voor het versleutelen van gegevens op rest met behulp van door de klant beheerde sleutel (CMK) beperkt-

* Ondersteuning voor deze functionaliteit is beperkt tot de prijs categorie **Algemeen** en **geoptimaliseerd voor geheugen** .
* Deze functie wordt alleen ondersteund in regio's en servers die opslag ondersteunen tot 16 TB. Raadpleeg de sectie opslag [hier](concepts-pricing-tiers.md#storage) in de documentatie voor de lijst met Azure-regio's die opslag tot 16TB ondersteunen.

    > [!NOTE]
    > - Alle nieuwe MySQL-servers die zijn gemaakt in de hierboven vermelde regio's, ondersteuning voor versleuteling met klant Manager-sleutels is **beschikbaar**. De PITR-server (Point-in-time) is niet in aanmerking komende, of de Lees replica is niet in theorie ' nieuw '.
    > - Als u wilt valideren of uw ingerichte server Maxi maal 16TB ondersteunt, gaat u naar de Blade prijs categorie in de portal en ziet u de maximale opslag grootte die wordt ondersteund door uw ingerichte server. Als u de schuif regelaar omhoog kunt verplaatsen naar 4 TB, ondersteunt uw server mogelijk geen versleuteling met door de klant beheerde sleutels. De gegevens worden echter te allen tijde versleuteld met behulp van service beheerde sleutels. Neem contact op met AskAzureDBforMySQL@service.microsoft.com Als u vragen hebt.

* Versleuteling wordt alleen ondersteund met de cryptografische sleutel RSA 2048.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van gegevens versleuteling met een door de klant beheerde sleutel voor uw Azure-Data Base voor MySQL met behulp van de [Azure Portal](howto-data-encryption-portal.md) en [Azure cli](howto-data-encryption-cli.md).
