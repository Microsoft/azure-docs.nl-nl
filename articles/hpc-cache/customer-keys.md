---
title: Klant-beheerd-sleutels gebruiken voor het versleutelen van gegevens in de Azure HPC-cache
description: Azure Key Vault met behulp van HPC cache van Azure gebruiken om de toegang tot de versleutelings sleutel te beheren in plaats van de standaard door micro soft beheerde versleutelings sleutels te gebruiken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 90af33a01450002c7d36a4ab4cf4a3da647068c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444569"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Door de klant beheerde versleutelings sleutels gebruiken voor Azure HPC-cache

U kunt Azure Key Vault gebruiken om de eigendom te bepalen van de sleutels die worden gebruikt voor het versleutelen van uw gegevens in de Azure HPC-cache. In dit artikel wordt uitgelegd hoe u door de klant beheerde sleutels gebruikt voor het versleutelen van cache gegevens.

> [!NOTE]
> Alle gegevens die zijn opgeslagen in azure, met inbegrip van de cache schijven, worden standaard versleuteld met behulp van door micro soft beheerde sleutels. U hoeft alleen de stappen in dit artikel uit te voeren als u de sleutels wilt beheren die worden gebruikt voor het versleutelen van uw gegevens.

De HPC-cache van Azure wordt ook beveiligd door de [VM-host-versleuteling](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) op de beheerde schijven die uw gegevens in de cache bevatten, zelfs als u een klant sleutel toevoegt voor de cache schijven. Het toevoegen van een door de klant beheerde sleutel voor dubbele versleuteling biedt een extra beveiligings niveau voor klanten met hoge beveiligings behoeften. Lees de [versleuteling aan de server zijde van Azure Disk Storage](../virtual-machines/disk-encryption.md) voor meer informatie.

Deze functie is alleen beschikbaar in een aantal Azure-regio's waar Azure HPC cache beschikbaar is. Raadpleeg de lijst met [Beschik baarheid van regio's](hpc-cache-overview.md#region-availability) voor meer informatie.

Er zijn drie stappen om door de klant beheerde sleutel versleuteling in te scha kelen voor Azure HPC-cache:

1. Stel een Azure Key Vault in om de sleutels op te slaan.
1. Wanneer u de Azure HPC-cache maakt, kiest u door de klant beheerde sleutel versleuteling en geeft u de sleutel kluis en de sleutel op die u wilt gebruiken.
1. Nadat de cache is gemaakt, machtigt u deze voor toegang tot de sleutel kluis.

Versleuteling is pas volledig ingesteld nadat u dit hebt geautoriseerd vanuit de zojuist gemaakte cache (stap 3). Dit komt doordat de identiteit van de cache moet worden door gegeven aan de sleutel kluis om deze een geautoriseerde gebruiker te maken. U kunt dit niet doen voordat u de cache maakt, omdat de identiteit niet bestaat totdat de cache is gemaakt.

Nadat u de cache hebt gemaakt, kunt u niet meer wisselen tussen door de klant beheerde sleutels en door micro soft beheerde sleutels. Als uw cache echter gebruikmaakt van door de klant beheerde sleutels, kunt u de versleutelings sleutel, de sleutel versie en de sleutel kluis zo nodig [wijzigen](#update-key-settings) .

## <a name="understand-key-vault-and-key-requirements"></a>Informatie over sleutel kluizen en belangrijkste vereisten

De sleutel kluis en-sleutel moeten voldoen aan deze vereisten voor gebruik met de Azure HPC-cache.

Eigenschappen van sleutel kluis:

* **Abonnement** : gebruik hetzelfde abonnement dat wordt gebruikt voor de cache.
* **Regio** -de sleutel kluis moet zich in dezelfde regio bevinden als de Azure HPC-cache.
* **Prijs categorie** -de Standard-laag is voldoende voor gebruik met de Azure HPC-cache.
* **Voorlopig verwijderen** : in azure HPC-cache is het mogelijk om de optie zacht verwijderen in te scha kelen als deze nog niet is geconfigureerd op de sleutel kluis.
* **Beveiliging opschonen** -beveiliging opschonen moet zijn ingeschakeld.
* **Toegangs beleid** : de standaard instellingen zijn voldoende.
* **Netwerk connectiviteit** : Azure HPC-cache moet toegang kunnen hebben tot de sleutel kluis, ongeacht de eindpunt instellingen die u kiest.

Sleutel eigenschappen:

* **Sleutel type** -RSA
* **RSA-sleutel grootte** -2048
* **Ingeschakeld** : Ja

Toegangs machtigingen voor de sleutel kluis:

* De gebruiker die de Azure HPC-cache maakt, moet machtigingen hebben die gelijk zijn aan de [rol van Key Vault Inzender](../role-based-access-control/built-in-roles.md#key-vault-contributor). U hebt dezelfde machtigingen nodig om Azure Key Vault in te stellen en te beheren.

  Lees [beveiligde toegang tot een sleutel kluis](../key-vault/general/secure-your-key-vault.md) voor meer informatie.

## <a name="1-set-up-azure-key-vault"></a>1. Azure Key Vault instellen

U kunt een sleutel kluis en sleutel instellen voordat u de cache maakt, of als onderdeel van het maken van de cache. Zorg ervoor dat deze resources voldoen aan de [bovenstaande](#understand-key-vault-and-key-requirements)vereisten.

Bij het maken van de cache moet u een kluis, sleutel en sleutel versie opgeven die moeten worden gebruikt voor de versleuteling van de cache.

Lees de [Azure Key Vault-documentatie](../key-vault/general/overview.md) voor meer informatie.

> [!NOTE]
> De Azure Key Vault moeten hetzelfde abonnement gebruiken en moeten zich in dezelfde regio bevinden als de Azure HPC-cache. Zorg ervoor dat de regio die u kiest [ondersteuning biedt voor de functie door de klant beheerde sleutels](hpc-cache-overview.md#region-availability).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. de cache maken met door de klant beheerde sleutels ingeschakeld

U moet de bron van de versleutelings sleutel opgeven wanneer u uw Azure HPC-cache maakt. Volg de instructies in [Create a Azure HPC cache](hpc-cache-create.md)en geef de sleutel kluis en de sleutel op op de pagina **schijf versleutelings sleutels** . U kunt een nieuwe sleutel kluis en sleutel maken tijdens het maken van de cache.

> [!TIP]
> Als de pagina **schijf versleutelings sleutels** niet wordt weer gegeven, controleert u of de cache zich in een van de ondersteunde regio's bevindt.

De gebruiker die de cache maakt, moet bevoegdheden hebben die gelijk zijn aan de [rol van Key Vault Inzender](../role-based-access-control/built-in-roles.md#key-vault-contributor) of hoger.

1. Klik op de knop om privé beheerde sleutels in te scha kelen. Nadat u deze instelling hebt gewijzigd, worden de instellingen voor de sleutel kluis weer gegeven.

1. Klik op **Selecteer een sleutel kluis** om de pagina sleutel selectie te openen. Kies of maak de sleutel kluis en de sleutel voor het versleutelen van gegevens op de schijven van deze cache.

   Als uw Azure Key Vault niet in de lijst wordt weer gegeven, raadpleegt u deze vereisten:

   * Bevindt de cache zich in hetzelfde abonnement als de sleutel kluis?
   * Bevindt de cache zich in dezelfde regio als de sleutel kluis?
   * Is er netwerk verbinding tussen de Azure Portal en de sleutel kluis?

1. Nadat u een kluis hebt geselecteerd, selecteert u de individuele sleutel in de beschik bare opties of maakt u een nieuwe sleutel. De sleutel moet een 2048-bits RSA-sleutel zijn.

1. Geef de versie voor de geselecteerde sleutel op. Meer informatie over versie beheer vindt u in de [documentatie van Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Ga verder met de rest van de specificaties en maak de cache zoals beschreven in [een Azure HPC-cache maken](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Azure Key Vault versleuteling machtigen vanuit de cache
<!-- header is linked from create article, update if changed -->

Na een paar minuten wordt de nieuwe Azure HPC-cache weer gegeven in uw Azure Portal. Ga naar de **overzichts** pagina om deze te machtigen om toegang te krijgen tot uw Azure Key Vault en door de klant beheerde sleutel versleuteling in te scha kelen.

> [!TIP]
> De cache kan worden weer gegeven in de lijst met resources voordat de berichten over de implementatie worden gewist. Controleer de lijst met resources na een minuut of twee in plaats van te wachten op een geslaagde melding.

Dit proces in twee stappen is nood zakelijk omdat de identiteit van het Azure HPC-cache-exemplaar moet worden door gegeven aan de Azure Key Vault voor autorisatie. De cache-identiteit bestaat pas nadat de eerste stappen voor het maken zijn voltooid.

> [!NOTE]
> U moet de versleuteling binnen 90 minuten machtigen nadat u de cache hebt gemaakt. Als u deze stap niet uitvoert, treedt er een time-out op in de cache. Een mislukte cache moet opnieuw worden gemaakt. deze kan niet worden hersteld.

De cache toont de status die **op de sleutel wacht**. Klik boven aan de pagina op de knop **versleuteling inschakelen** om de cache te autoriseren voor toegang tot de opgegeven sleutel kluis.

![scherm afbeelding van de pagina met de cache-overzicht in de portal, met markeren op de knop versleuteling inschakelen (bovenste rij) en status: wachten op sleutel](media/waiting-for-key.png)

Klik op **versleuteling inschakelen** en klik vervolgens op de knop **Ja** om de cache te autoriseren voor het gebruik van de versleutelings sleutel. Deze actie maakt ook de beveiliging van zacht verwijderen en leegmaken (als deze nog niet is ingeschakeld) op de sleutel kluis.

![scherm afbeelding van de pagina met cache-overzicht in de portal, met een banner bericht aan de bovenkant waarin de gebruiker wordt gevraagd om versleuteling in te scha kelen door op Ja te klikken](media/enable-keyvault.png)

Nadat de cache toegang tot de sleutel kluis heeft aangevraagd, kan de schijf die in de cache opgeslagen gegevens opslaat, worden gemaakt en versleuteld.

Nadat u versleuteling hebt geautoriseerd, wordt door Azure HPC cache enkele minuten van Setup uitgevoerd om de versleutelde schijven en gerelateerde infra structuur te maken.

## <a name="update-key-settings"></a>Sleutel instellingen bijwerken

U kunt de sleutel kluis, de sleutel of de sleutel versie voor uw cache wijzigen via de Azure Portal. Klik op de koppeling naar de **versleutelings** instellingen van de cache om de pagina **instellingen voor klant sleutels** te openen.

U kunt geen cache wijzigen tussen door de klant beheerde sleutels en sleutels die door het systeem worden beheerd.

![scherm opname van de pagina instellingen voor klant sleutels, bereikt door te klikken op instellingen > versleuteling van de pagina cache in de Azure Portal](media/change-key-click.png)

Klik op de koppeling **sleutel wijzigen** en klik vervolgens op **Wijzig de sleutel kluis, de sleutel of de versie** om de sleutel kiezer te openen.

![scherm opname van de pagina ' Selecteer de sleutel op de Azure Key Vault ' met drie vervolg keuzelijsten om de sleutel kluis, sleutel en versie te kiezen](media/select-new-key.png)

Sleutel kluizen in hetzelfde abonnement en dezelfde regio als deze cache worden weer gegeven in de lijst.

Nadat u de nieuwe waarden voor de versleutelings sleutel hebt gekozen, klikt u op **selecteren**. Er wordt een bevestigings pagina met de nieuwe waarden weer gegeven. Klik op **Opslaan** om de selectie te volt ooien.

![scherm afbeelding van de bevestigings pagina met de knop Opslaan linksboven](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Meer informatie over door de klant beheerde sleutels in azure

In deze artikelen wordt uitgelegd hoe u Azure Key Vault en door de klant beheerde sleutels gebruikt voor het versleutelen van gegevens in Azure:

* [Overzicht van Azure Storage-versleuteling](../storage/common/storage-service-encryption.md)
* [Schijf versleuteling met door de klant beheerde sleutels](../virtual-machines/disk-encryption.md#customer-managed-keys) : Documentatie voor het gebruik van Azure Key Vault met Managed disks, een soortgelijk scenario voor de HPC-cache van Azure

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure HPC-cache en geautoriseerde versleuteling op basis van Key Vault hebt gemaakt, kunt u door gaan met het instellen van uw cache door deze toegang te geven tot uw gegevens bronnen.

* [Opslagdoelen toevoegen](hpc-cache-add-storage.md)