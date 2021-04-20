---
title: Door de klant mand sleutels gebruiken voor het versleutelen van gegevens in Azure HPC Cache
description: Toegang tot versleutelingssleutels Azure Key Vault met Azure HPC Cache in plaats van de standaard door Microsoft beheerde versleutelingssleutels te gebruiken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 36ce494c7fd51a1341834d5c231e32e60c5a32b9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751990"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Door de klant beheerde versleutelingssleutels gebruiken voor Azure HPC Cache

U kunt de Azure Key Vault om het eigendom te bepalen van de sleutels die worden gebruikt voor het versleutelen van uw gegevens in Azure HPC Cache. In dit artikel wordt uitgelegd hoe u door de klant beheerde sleutels gebruikt voor het versleutelen van cachegegevens.

> [!NOTE]
> Alle gegevens die zijn opgeslagen in Azure, inclusief op de cacheschijven, worden standaard versleuteld met door Microsoft beheerde sleutels. U hoeft alleen de stappen in dit artikel te volgen als u de sleutels wilt beheren die worden gebruikt om uw gegevens te versleutelen.

Azure HPC Cache wordt ook beveiligd door [VM-hostversleuteling](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) op de beheerde schijven die uw gegevens in de cache bevatten, zelfs als u een klantsleutel voor de cacheschijven toevoegt. Het toevoegen van een door de klant beheerde sleutel voor dubbele versleuteling biedt een extra beveiligingsniveau voor klanten met hoge beveiligingsbehoeften. Lees [Versleuteling aan serverzijde van Azure Disk Storage](../virtual-machines/disk-encryption.md) voor meer informatie.

Er zijn drie stappen voor het inschakelen van door de klant beheerde sleutelversleuteling voor Azure HPC Cache:

1. Stel een Azure Key Vault voor het opslaan van de sleutels.
1. Wanneer u de Azure HPC Cache, kiest u door de klant beheerde sleutelversleuteling en geeft u de sleutelkluis en sleutel op die u wilt gebruiken.
1. Nadat de cache is gemaakt, machtigt u deze voor toegang tot de sleutelkluis.

Versleuteling wordt pas volledig ingesteld nadat u deze hebt geautoriseerd vanuit de zojuist gemaakte cache (stap 3). Dit komt doordat u de identiteit van de cache moet doorgeven aan de sleutelkluis om er een geautoriseerde gebruiker van te maken. U kunt dit niet doen voordat u de cache maakt, omdat de identiteit pas bestaat als de cache is gemaakt.

Nadat u de cache hebt gemaakt, kunt u niet meer wisselen tussen door de klant beheerde sleutels en door Microsoft beheerde sleutels. Als uw cache echter door de [](#update-key-settings) klant beheerde sleutels gebruikt, kunt u de versleutelingssleutel, de sleutelversie en de sleutelkluis indien nodig wijzigen.

## <a name="understand-key-vault-and-key-requirements"></a>Inzicht in sleutelkluis- en sleutelvereisten

De sleutelkluis en sleutel moeten voldoen aan deze vereisten om te kunnen werken met Azure HPC Cache.

Eigenschappen van de sleutelkluis:

* **Abonnement:** gebruik hetzelfde abonnement dat wordt gebruikt voor de cache.
* **Regio:** de sleutelkluis moet zich in dezelfde regio als de Azure HPC Cache.
* **Prijscategorie:** de Standard-laag is voldoende voor gebruik met Azure HPC Cache.
* **Zacht verwijderen:** Azure HPC Cache wordt verwijderen inschakelen als deze nog niet is geconfigureerd in de sleutelkluis.
* **Beveiliging tegen ops** manier ops manier: beveiliging tegen opsisten moet zijn ingeschakeld.
* **Toegangsbeleid:** de standaardinstellingen zijn voldoende.
* **Netwerkconnectiviteit:** Azure HPC Cache moeten toegang hebben tot de sleutelkluis, ongeacht de eindpuntinstellingen die u kiest.

Sleuteleigenschappen:

* **Sleuteltype** - RSA
* **RSA-sleutelgrootte** - 2048
* **Ingeschakeld -** Ja

Toegangsmachtigingen voor Key Vault:

* De gebruiker die de Azure HPC Cache, moet machtigingen hebben die gelijk zijn aan de [Key Vault rol inzender](../role-based-access-control/built-in-roles.md#key-vault-contributor). Dezelfde machtigingen zijn nodig voor het instellen en beheren van Azure Key Vault.

  Lees [Beveiligde toegang tot een sleutelkluis](../key-vault/general/security-overview.md) voor meer informatie.

## <a name="1-set-up-azure-key-vault"></a>1. Een Azure Key Vault

U kunt een sleutelkluis en sleutel instellen voordat u de cache maakt of dit doen als onderdeel van het maken van de cache. Zorg ervoor dat deze resources voldoen aan de vereisten die hierboven [worden beschreven.](#understand-key-vault-and-key-requirements)

Tijdens het maken van de cache moet u een kluis, sleutel en sleutelversie opgeven die moeten worden gebruikt voor de versleuteling van de cache.

Lees de [Azure Key Vault voor](../key-vault/general/overview.md) meer informatie.

> [!NOTE]
> De Azure Key Vault moet hetzelfde abonnement gebruiken en zich in dezelfde regio als de Azure HPC Cache. Zorg ervoor dat de regio die u kiest [beide producten ondersteunt.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. De cache maken met door de klant beheerde sleutels ingeschakeld

U moet de versleutelingssleutelbron opgeven wanneer u uw Azure HPC Cache. Volg de instructies in [Een Azure HPC Cache](hpc-cache-create.md)en geef de sleutelkluis en sleutel op de pagina **Schijfversleutelingssleutels** op. U kunt een nieuwe sleutelkluis en sleutel maken tijdens het maken van de cache.

> [!TIP]
> Als de **pagina Schijfversleutelingssleutels** niet wordt weergegeven, moet u ervoor zorgen dat uw cache zich in een van de [ondersteunde regio's .](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

De gebruiker die de cache maakt, moet over bevoegdheden beschikken die gelijk zijn aan de [rol Key Vault inzender](../role-based-access-control/built-in-roles.md#key-vault-contributor) of hoger.

1. Klik op de knop om persoonlijk beheerde sleutels in teschakelen. Nadat u deze instelling hebt gewijzigd, worden de instellingen van de sleutelkluis weergegeven.

1. Klik **op Een sleutelkluis selecteren** om de pagina voor sleutelselectie te openen. Kies of maak de sleutelkluis en sleutel voor het versleutelen van gegevens op de schijven van deze cache.

   Als uw Azure Key Vault niet wordt weergegeven in de lijst, controleert u deze vereisten:

   * Maakt de cache zich in hetzelfde abonnement als de sleutelkluis?
   * Is de cache in dezelfde regio als de sleutelkluis?
   * Is er netwerkverbinding tussen de Azure Portal en de sleutelkluis?

1. Nadat u een kluis hebt geselecteerd, selecteert u de afzonderlijke sleutel uit de beschikbare opties of maakt u een nieuwe sleutel. De sleutel moet een 2048-bits RSA-sleutel zijn.

1. Geef de versie voor de geselecteerde sleutel op. Meer informatie over versieversies in de [Azure Key Vault documentatie](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Ga door met de rest van de specificaties en maak de cache zoals beschreven in [Een Azure HPC Cache.](hpc-cache-create.md)

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Versleuteling Azure Key Vault de cache toestaan
<!-- header is linked from create article, update if changed -->

Na enkele minuten wordt de nieuwe Azure HPC Cache weergegeven in uw Azure Portal. Ga naar de pagina **Overzicht om** deze toegang te verlenen tot uw Azure Key Vault door de klant beheerde sleutelversleuteling in teschakelen.

> [!TIP]
> De cache wordt mogelijk weergegeven in de lijst met resources voordat de berichten over de implementatie worden geweed. Controleer de lijst met resources na een paar minuten in plaats van te wachten op een melding dat de melding is geslaagd.

Dit proces in twee stappen is nodig omdat het Azure HPC Cache een identiteit nodig heeft om door te geven aan de Azure Key Vault voor autorisatie. De cache-identiteit bestaat pas nadat de eerste stappen voor het maken zijn voltooid.

> [!NOTE]
> U moet versleuteling binnen 90 minuten na het maken van de cache autor mailen. Als u deze stap niet voltooit, t mogelijk een time-out voor de cache en mislukt deze. Een mislukte cache moet opnieuw worden gemaakt. Dit kan niet worden opgelost.

In de cache wordt de status **Wachten op sleutel weergeven.** Klik op **de knop Versleuteling** inschakelen boven aan de pagina om de cache toegang te verlenen tot de opgegeven sleutelkluis.

![schermopname van de overzichtspagina van de cache in de portal, met markeringen op de knop Versleuteling inschakelen (bovenste rij) en Status: Wachten op sleutel](media/waiting-for-key.png)

Klik **op Versleuteling** inschakelen en klik vervolgens op **de knop Ja** om de cache toestemming te geven de versleutelingssleutel te gebruiken. Met deze actie wordt ook de beveiliging voor het verwijderen en opsluizen van de sleutelkluis ingeschakeld (indien nog niet ingeschakeld).

![schermopname van de overzichtspagina van de cache in de portal, met bovenaan een bannerbericht waarin de gebruiker wordt gevraagd om versleuteling in teschakelen door op Ja te klikken](media/enable-keyvault.png)

Nadat de cache toegang tot de sleutelkluis heeft gevraagd, kan deze de schijven maken en versleutelen die gegevens in de cache opslaan.

Nadat u versleuteling hebt geautoriseerd, Azure HPC Cache nog enkele minuten van de installatie voor het maken van de versleutelde schijven en de bijbehorende infrastructuur.

## <a name="update-key-settings"></a>Sleutelinstellingen bijwerken

U kunt de sleutelkluis, sleutel of sleutelversie voor uw cache wijzigen vanuit de Azure Portal. Klik op de koppeling **Versleutelingsinstellingen** van de cache om de pagina **Klantsleutelinstellingen te** openen.

U kunt een cache niet wijzigen tussen door de klant beheerde sleutels en door het systeem beheerde sleutels.

![schermopname van de pagina 'Instellingen voor klantsleutels', bereikt door te klikken op Instellingen > Versleuteling vanaf de cachepagina in de Azure Portal](media/change-key-click.png)

Klik op **de koppeling Sleutel** wijzigen en klik vervolgens op De **sleutelkluis, sleutel** of versie wijzigen om de sleutel selector te openen.

![schermopname van de pagina 'sleutel selecteren Azure Key Vault' met drie vervolgkeuzetoetsen om de sleutelkluis, sleutel en versie te kiezen](media/select-new-key.png)

Sleutelkluizen in hetzelfde abonnement en dezelfde regio als deze cache worden weergegeven in de lijst.

Nadat u de nieuwe waarden voor de versleutelingssleutel hebt gekozen, klikt u op **Selecteren.** Er wordt een bevestigingspagina weergegeven met de nieuwe waarden. Klik **op Opslaan** om de selectie te maken.

![schermopname van de bevestigingspagina met de knop Opslaan linksboven](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Meer informatie over door de klant beheerde sleutels in Azure

In deze artikelen wordt meer uitgelegd over het Azure Key Vault en door de klant beheerde sleutels voor het versleutelen van gegevens in Azure:

* [Overzicht van Azure Storage-versleuteling](../storage/common/storage-service-encryption.md)
* [Schijfversleuteling met door](../virtual-machines/disk-encryption.md#customer-managed-keys) de klant beheerde sleutels: documentatie voor het gebruik van Azure Key Vault met beheerde schijven. Dit is een vergelijkbaar scenario als Azure HPC Cache

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure HPC Cache en geautoriseerde Key Vault-gebaseerde versleuteling hebt gemaakt, kunt u uw cache blijven instellen door deze toegang te geven tot uw gegevensbronnen.

* [Opslagdoelen toevoegen](hpc-cache-add-storage.md)
