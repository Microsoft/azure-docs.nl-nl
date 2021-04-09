---
title: Een account maken in de Azure Portal
description: Informatie over het maken van een Azure Batch-account in Azure Portal voor het uitvoeren van grootschalige parallelle workloads in de cloud
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703661"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Een Batch-account maken met behulp van Azure Portal

In dit onderwerp wordt beschreven hoe u een [Azure batch-account](accounts.md) maakt in de [Azure Portal](https://portal.azure.com)en hoe u de account Eigenschappen kiest die passen bij uw reken scenario. U leert ook waar u belang rijke account eigenschappen kunt vinden, zoals toegangs sleutels en account-Url's.

Zie [batch-service werk stroom en resources](batch-service-workflow-features.md)voor achtergrond informatie over batch-accounts en-scenario's.

## <a name="create-a-batch-account"></a>Batch-account maken

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Op de start pagina selecteert u **een resource maken**.

1. Voer in het zoekvak batch- **service** in. Selecteer **batch-service** in de resultaten en selecteer vervolgens **maken**.

1. Voer de volgende gegevens in.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Scherm afbeelding van het venster voor het nieuwe batch-account.":::

    a. **Abonnement**: het abonnement waarin het Batch-account moet worden gemaakt. Als u slechts één abonnement hebt, is het standaard geselecteerd.

    b. **Resourcegroep**: Selecteer een bestaande resourcegroep voor het nieuwe Batch-account. U kunt er ook voor kiezen om een nieuwe resourcegroep te maken.

    c. **Accountnaam**: de naam die u kiest, moet uniek zijn in de Azure-regio waarin het account wordt gemaakt (zie **Locatie** hieronder). De accountnaam mag alleen kleine letters of cijfers bevatten en moet 3 tot 24 tekens lang zijn.

    d. **Locatie**: de Azure-regio om het Batch-account in te maken. Alleen de regio's die door uw abonnement en resourcegroep worden ondersteund, worden als opties weergegeven.

    e. **Opslag account**: een optioneel [Azure Storage account](accounts.md#azure-storage-accounts) dat u aan uw batch-account koppelt. U kunt een bestaand opslag account selecteren of een nieuwe maken. Een v2-opslagaccount voor algemeen gebruik wordt aanbevolen voor de beste prestaties.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Scherm afbeelding van de opties bij het maken van een opslag account.":::

1. Selecteer zo nodig **Geavanceerd** om het **identiteits type**, de **open bare netwerk toegang** of de **groeps toewijzings modus** op te geven. Voor de meeste scenario's zijn de standaard opties prima.

1. Selecteer **controleren + maken** en selecteer vervolgens **maken** om het account te maken.

## <a name="view-batch-account-properties"></a>Eigenschappen van Batch-account weergeven

Zodra het account is gemaakt, selecteert u het account om naar de instellingen en eigenschappen te gaan. U hebt toegang tot alle eigenschappen en accountinstellingen via het linkermenu.

> [!NOTE]
> De naam van het batch-account is de ID en kan niet worden gewijzigd. Als u de naam van een batch-account wilt wijzigen, moet u het account verwijderen en een nieuwe maken met de gewenste naam.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Scherm afbeelding van de pagina met het batch-account in de Azure Portal.":::

Wanneer u een toepassing ontwikkelt met de [Batch-API's](batch-apis-tools.md#azure-accounts-for-batch-development), hebt u een account-URL en -sleutel nodig voor toegang tot de Batch-resources. (Batch ondersteunt ook Azure Active Directory-verificatie.) Selecteer **sleutels** om de toegangs gegevens van het batch-account weer te geven.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Scherm opname van batch-account sleutels in de Azure Portal.":::

Als u de naam en sleutels van het opslagaccount wilt zien dat is gekoppeld aan uw Batch-account, selecteert u **Opslagaccount**.

Als u de [resource quota's](batch-quota-limit.md) wilt weer geven die van toepassing zijn op het batch-account, selecteert u **quota's**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Aanvullende configuratie voor de modus Gebruikersabonnement

Als u ervoor kiest een Batch-account te maken in de modus Gebruikersabonnement, moet u de volgende aanvullende stappen uitvoeren voordat u het account gaat maken.

> [!IMPORTANT]
> De gebruiker die het batch-account maakt in de modus gebruikers abonnement moet een rol voor Inzender of eigenaar hebben voor het abonnement waarin het batch-account wordt gemaakt.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch toegang geven tot het abonnement (eenmalige bewerking)

Wanneer u uw eerste Batch-account maakt in de modus Gebruikersabonnement, moet u de volgende stappen uitvoeren om uw abonnement te registreren bij Batch. (Als u dit al hebt gedaan, gaat u naar de volgende sectie.)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **alle services**  >  **abonnementen** en selecteer het abonnement dat u wilt gebruiken voor het batch-account.

1. Selecteer op de pagina **Abonnement** de optie **Resourceproviders** en zoek naar **Microsoft.Batch**. Controleer of de resourceprovider **Microsoft.Batch** is geregistreerd in het abonnement. Als dat niet het geval is, selecteert u de koppeling **registreren** aan de bovenkant van het scherm.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Scherm opname van de resource provider Microsoft.Batch.":::

1. Ga terug naar de pagina **abonnement** en selecteer **toegangs beheer (IAM)**  >  **roltoewijzingen**  >  **toevoegen**  >  **roltoewijzing toevoegen**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Scherm afbeelding van de pagina roltoewijzingen voor een abonnement.":::

1. Selecteer op de pagina **roltoewijzing toevoegen** de rol **Inzender** of **eigenaar** en zoek vervolgens naar de batch-API. Zoek naar **Microsoft Azure batch** -of **MICROSOFTAZUREBATCH** om de API te vinden. (**ddbf3205-c6bd-46ae-8127-60eb93363864** is de toepassings-id voor de batch-API.)

1. Als u de Batch-API hebt gevonden, selecteert u deze en selecteert u **Opslaan**.

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

In de modus gebruikers abonnement is een [Azure Key Vault](../key-vault/general/overview.md) vereist. De Key Vault moeten zich in hetzelfde abonnement en dezelfde regio bevinden als het batch-account dat moet worden gemaakt.

1. Op de start pagina van de [Azure Portal](https://portal.azure.com)selecteert u **een resource maken**.
1. Typ **Sleutelkluis** in het zoekvak. Selecteer **Key Vault** in de resultaten en selecteer vervolgens **maken**.
1. Voer op de pagina **sleutel kluis maken** een naam in voor de Key Vault en maak een nieuwe resource groep in de gewenste regio voor uw batch-account. Laat de overige instellingen op de standaardwaarden staan en selecteer **Maken**.

Wanneer u het batch-account maakt in de modus gebruikers abonnement, geeft u **gebruikers abonnement** op als de pool toewijzings modus, selecteert u de Key Vault die u hebt gemaakt en schakelt u het selectie vakje Azure batch toegang tot de Key Vault toe.

Als u liever hand matig toegang wilt verlenen aan de Key Vault, gaat u naar de sectie **toegangs beleid** van de Key Vault en selecteert u **toegangs beleid toevoegen**. Selecteer de koppeling naast **Principal selecteren** en zoek naar **Microsoft Azure batch** (toepassings-id **ddbf3205-c6bd-46ae-8127-60eb93363864**). Selecteer die principal en configureer vervolgens de **geheime machtigingen** via de vervolg keuzelijst. Aan Azure Batch moet mini maal de machtigingen **Get**, **List**, **set** en **Delete** worden opgegeven. Voor [sleutel kluizen waarvoor zacht verwijderen is ingeschakeld](../key-vault/general/soft-delete-overview.md), moet Azure batch ook de machtiging **herstellen** hebben.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Scherm afbeelding van de selecties van geheime machtigingen voor Azure Batch":::

Selecteer **toevoegen** en zorg ervoor dat de selectie vakjes **Azure virtual machines voor implementatie** en **Azure Resource Manager voor implementatie van sjabloon** zijn geselecteerd voor de gekoppelde **Key Vault** resource. Selecteer **Opslaan** om uw wijzigingen door te voeren.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Scherm afbeelding van het venster toegangs beleid.":::

### <a name="configure-subscription-quotas"></a>Abonnementquota configureren

Voor gebruikers abonnements batch-accounts moeten [kern quota](batch-quota-limit.md) hand matig worden ingesteld. Standard-batch kern quota gelden niet voor accounts in de modus gebruikers abonnement en de [quota's in uw abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md) voor regionale reken kernen, reken kernen per serie en andere bronnen worden gebruikt en afgedwongen.

1. Selecteer in de [Azure-portal](https://portal.azure.com) uw Batch-account in de modus voor gebruikersabonnementen om de instellingen en eigenschappen ervan weer te geven.
1. Selecteer **Quota** in het linkermenu om de kerngeheugenquota die aan uw Batch-account zijn gekoppeld, te bekijken en configureren.

## <a name="other-batch-account-management-options"></a>Andere beheeropties voor uw Batch-account

Naast het gebruik van Azure Portal kunt u met de volgende hulpprogramma's Batch-accounts maken en beheren:

- [Batch-PowerShell-cmdlets](batch-powershell-cmdlets-get-started.md)
- [Azure-CLI](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Lees de basisbeginselen van het ontwikkelen van een voor Batch geschikte toepassing met behulp van de [clientbibliotheek Batch .NET](quick-run-dotnet.md) of [Python](quick-run-python.md). Deze Quick starts begeleiden u bij een voorbeeld toepassing die gebruikmaakt van de batch-service om een werk belasting op meerdere reken knooppunten uit te voeren, met behulp van Azure Storage voor het klaarzetten en ophalen van werkbelasting bestanden.