---
title: Azure Synapse Analytics versleuteling
description: Een artikel waarin versleuteling in Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 6ddafb0e76799e3d8011232534c505f97c79b22e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751126"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Versleuteling voor Azure Synapse Analytics-werkruimten

In dit artikel wordt het volgende beschreven:
* Versleuteling van data-at-rest in Synapse Analytics werkruimten.
* Configuratie van Synapse-werkruimten voor het inschakelen van versleuteling met een door de klant beheerde sleutel.
* Sleutels beheren die worden gebruikt voor het versleutelen van gegevens in werkruimten.

## <a name="encryption-of-data-at-rest"></a>Versleuteling van data-at-rest

Een volledige versleuteling-at-rest-oplossing zorgt ervoor dat de gegevens nooit in niet-versleutelde vorm worden opgeslagen. Dubbele versleuteling van data-at-rest vermindert bedreigingen met twee afzonderlijke versleutelingslagen om bescherming te bieden tegen eventuele bedreigingen in één laag. Azure Synapse Analytics biedt een tweede versleutelingslaag voor de gegevens in uw werkruimte met een door de klant beheerde sleutel. Deze sleutel wordt in uw [Azure Key Vault,](../../key-vault/general/overview.md)zodat u eigenaar kunt worden van sleutelbeheer en -rotatie.

De eerste versleutelingslaag voor Azure-services wordt ingeschakeld met door het platform beheerde sleutels. Standaard worden Azure Disks en gegevens in Azure Storage-accounts automatisch 'at rest' versleuteld. Meer informatie over hoe versleuteling wordt gebruikt in Microsoft Azure in overzicht [van Azure Encryption](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Azure Synapse versleuteling

In deze sectie krijgt u meer inzicht in hoe door de klant beheerde sleutelversleuteling wordt ingeschakeld en afgedwongen in Synapse-werkruimten. Deze versleuteling maakt gebruik van bestaande sleutels of nieuwe sleutels die worden gegenereerd in Azure Key Vault. Eén sleutel wordt gebruikt om alle gegevens in een werkruimte te versleutelen. Synapse-werkruimten ondersteunen RSA-sleutels met sleutels van 2048 en 3072 bytegrootte.

> [!NOTE]
> Synapse-werkruimten bieden geen ondersteuning voor het gebruik van Elliptic-Curve ECC-sleutels (Cryptography) voor versleuteling.

De gegevens in de volgende Synapse-onderdelen worden versleuteld met de door de klant beheerde sleutel die is geconfigureerd op werkruimteniveau:
* SQL-pools
 * Toegewezen SQL-pools
 * Serverloze SQL-pools
* Apache Spark-pools
* Azure Data Factory integratieruntimes, pijplijnen en gegevenssets.

## <a name="workspace-encryption-configuration"></a>Configuratie van werkruimteversleuteling

Werkruimten kunnen worden geconfigureerd om dubbele versleuteling in te stellen met een door de klant beheerde sleutel op het moment dat de werkruimte wordt gemaakt. Selecteer de optie Dubbele versleuteling inschakelen met behulp van een door de klant beheerde sleutel op het tabblad Beveiliging bij het maken van uw nieuwe werkruimte. U kunt ervoor kiezen om een sleutel-id-URI in te voeren of een selectie te maken uit een lijst met sleutelkluizen in dezelfde **regio** als de werkruimte. Op Key Vault zelf moet **beveiliging tegen ops manier van ops** manier zijn ingeschakeld.

> [!IMPORTANT]
> De configuratie-instelling voor dubbele versleuteling kan niet worden gewijzigd nadat de werkruimte is gemaakt.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="In dit diagram ziet u de optie die moet worden geselecteerd om een werkruimte in te stellen voor dubbele versleuteling met een door de klant beheerde sleutel.":::

### <a name="key-access-and-workspace-activation"></a>Sleuteltoegang en activering van werkruimte

Het Azure Synapse versleutelingsmodel met door de klant beheerde sleutels omvat de werkruimte die toegang heeft tot de sleutels in Azure Key Vault om zo nodig te versleutelen en ontsleutelen. De sleutels worden toegankelijk gemaakt voor de werkruimte via een toegangsbeleid of [Azure Key Vault RBAC-toegang.](../../key-vault/general/rbac-guide.md) Wanneer u machtigingen verleent via een [Azure Key Vault-toegangsbeleid,](../../key-vault/general/security-overview.md#key-vault-authentication-options) kiest u de optie Alleen toepassing tijdens het maken van het beleid (selecteer de beheerde identiteit van de werkruimte en voeg deze niet toe als een geautoriseerde toepassing).

 Aan de beheerde identiteit van de werkruimte moeten de machtigingen worden verleend die nodig zijn voor de sleutelkluis voordat de werkruimte kan worden geactiveerd. Deze gefaseerd aanpak voor het activeren van werkruimten zorgt ervoor dat gegevens in de werkruimte worden versleuteld met de door de klant beheerde sleutel. Houd er rekening mee dat versleuteling kan worden ingeschakeld of uitgeschakeld voor toegewezen SQL-pools. Elke pool is niet standaard ingeschakeld voor versleuteling.

#### <a name="permissions"></a>Machtigingen

Als u data-at-rest wilt versleutelen of ontsleutelen, moet de beheerde identiteit van de werkruimte de volgende machtigingen hebben:
* WrapKey (om een sleutel in te voegen in Key Vault bij het maken van een nieuwe sleutel).
* UnwrapKey (om de sleutel voor ontsleuteling op te halen).
* Get (om het openbare deel van een sleutel te lezen)

#### <a name="workspace-activation"></a>Activering van werkruimte

Nadat uw werkruimte (met dubbele versleuteling ingeschakeld) is gemaakt, blijft deze in de status In behandeling totdat de activering is geslaagd. De werkruimte moet worden geactiveerd voordat u alle functionaliteit volledig kunt gebruiken. U kunt bijvoorbeeld alleen een nieuwe toegewezen SQL-pool maken zodra de activering is geslaagd. Verleen de beheerde identiteit van de werkruimte toegang tot de sleutelkluis en klik op de activeringskoppeling in de banner Azure Portal werkruimte. Zodra de activering is voltooid, is uw werkruimte klaar voor gebruik, met de zekerheid dat alle gegevens erin zijn beveiligd met uw door de klant beheerde sleutel. Zoals eerder vermeld, moet voor de sleutelkluis beveiliging voor opsluizen zijn ingeschakeld om de activering te laten slagen.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="In dit diagram ziet u de banner met de activeringskoppeling voor de werkruimte.":::


### <a name="manage-the-workspace-customer-managed-key"></a>De door de klant beheerde sleutel van de werkruimte beheren 

U kunt de door de klant beheerde  sleutel die wordt gebruikt voor het versleutelen van gegevens wijzigen op de pagina Versleuteling in de Azure Portal. Ook hier kunt u een nieuwe sleutel kiezen met behulp van een sleutel-id of kiezen uit Key Vaults waar u toegang tot hebt in dezelfde regio als de werkruimte. Als u een sleutel in een andere sleutelkluis kiest dan de sleutel die u eerder hebt gebruikt, verleent u de machtigingen Get, Wrap en Unwrap aan de beheerde identiteit van de werkruimte voor de nieuwe sleutelkluis. De werkruimte valideert de toegang tot de nieuwe sleutelkluis en alle gegevens in de werkruimte worden opnieuw versleuteld met de nieuwe sleutel.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="In dit diagram ziet u de sectie Versleuteling van de werkruimte in Azure Portal.":::

>[!IMPORTANT]
>Wanneer u de versleutelingssleutel van een werkruimte verandert, behoudt u de sleutel totdat u deze in de werkruimte vervangt door een nieuwe sleutel. Dit is om het ontsleutelen van gegevens met de oude sleutel toe te staan voordat deze opnieuw worden versleuteld met de nieuwe sleutel.

Azure Key Vault-beleid voor automatische, periodieke rotatie van sleutels of acties voor de sleutels kan leiden tot het maken van nieuwe sleutelversies. U kunt ervoor kiezen om alle gegevens in de werkruimte opnieuw te versleutelen met de nieuwste versie van de actieve sleutel. Als u opnieuw wilt versleutelen, wijzigt u de sleutel in de Azure Portal in een tijdelijke sleutel en schakelt u vervolgens terug naar de sleutel die u wilt gebruiken voor versleuteling. Als u bijvoorbeeld gegevensversleuteling wilt bijwerken met behulp van de meest recente versie van de actieve sleutel Key1, wijzigt u de door de klant beheerde sleutel van de werkruimte in tijdelijke sleutel Key2. Wacht tot de versleuteling met Key2 is voltooien. Vervolgens wordt de door de klant beheerde sleutel van de werkruimte teruggewisseld naar Key1-gegevens in de werkruimte met de nieuwste versie van Key1.

> [!NOTE]
> Azure Synapse Analytics werkt actief aan het toevoegen van functionaliteit om gegevens automatisch opnieuw te versleutelen wanneer er nieuwe sleutelversies worden gemaakt. Totdat deze functionaliteit beschikbaar is, dwingt u het opnieuw versleutelen van gegevens af met behulp van het bovenstaande proces om consistentie in uw werkruimte te garanderen.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL Transparent Data Encryption met door de service beheerde sleutels

SQL Transparent Data Encryption (TDE) is beschikbaar voor toegewezen SQL-pools in werkruimten *die niet* zijn ingeschakeld voor dubbele versleuteling. In dit type werkruimte wordt een door de service beheerde sleutel gebruikt om dubbele versleuteling te bieden voor de gegevens in de toegewezen SQL-pools. TDE met de door de service beheerde sleutel kan worden ingeschakeld of uitgeschakeld voor afzonderlijke toegewezen SQL-pools.

## <a name="next-steps"></a>Volgende stappen

[Ingebouwd Azure-beleid gebruiken om versleutelingsbeveiliging te implementeren voor Synapse-werkruimten](../policy-reference.md)

