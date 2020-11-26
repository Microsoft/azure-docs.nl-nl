---
title: Versleuteling van Azure Synapse Analytics
description: Een artikel met uitleg over versleuteling in azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 17dbdbbef45e0068601835197a1177ee20d98ca3
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296755"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces-preview"></a>Versleuteling voor Azure Synapse Analytics (voor beeld van werk ruimten)

In dit artikel worden de volgende onderwerpen beschreven:
* Versleuteling van gegevens in rust in azure Synapse Analytics-werk ruimten.
* Configuratie van Synapse-werk ruimten om versleuteling in te scha kelen met een door de klant beheerde sleutel.
* Sleutels beheren die worden gebruikt voor het versleutelen van gegevens in werk ruimten.

## <a name="encryption-of-data-at-rest"></a>Versleuteling van data-at-rest

Een complete oplossing voor versleuteling op rest zorgt ervoor dat de gegevens in niet-versleutelde vorm niet worden bewaard. Dubbele versleuteling van gegevens in rust vermindert de bedreigingen met twee, afzonderlijke coderings lagen om te beschermen tegen inbreuken op een enkele laag. Azure Synapse Analytics biedt een tweede laag code ring voor de gegevens in uw werk ruimte met een door de klant beheerde sleutel. Deze sleutel wordt in uw [Azure Key Vault](../../key-vault/general/overview.md)beveiligd, zodat u eigenaar van sleutel beheer en-rotatie kunt worden.

De eerste laag versleuteling voor Azure-Services is ingeschakeld met door het platform beheerde sleutels. Standaard worden Azure-schijven en gegevens in Azure Storage-accounts automatisch versleuteld op rest. Meer informatie over hoe versleuteling wordt gebruikt in Microsoft Azure in het [overzicht van Azure-versleuteling](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Azure Synapse-versleuteling

In deze sectie wordt uitgelegd hoe de door de klant beheerde sleutel versleuteling wordt ingeschakeld en afgedwongen in Synapse-werk ruimten. Deze versleuteling maakt gebruik van bestaande sleutels of nieuwe sleutels die zijn gegenereerd in Azure Key Vault. Er wordt één sleutel gebruikt voor het versleutelen van alle gegevens in een werk ruimte. Synapse-werk ruimten ondersteunen RSA-sleutels met 2048 en 3072 sleutels van byte grootte.

> [!NOTE]
> Synapse-werk ruimten bieden geen ondersteuning voor het gebruik van ECC-sleutels (Elliptic-Curve Cryptography) voor versleuteling.

De gegevens in de volgende Synapse-onderdelen worden versleuteld met de door de klant beheerde sleutel die is geconfigureerd op het niveau van de werk ruimte:
* SQL-pools
 * Toegewezen SQL-groepen
 * SQL-groepen zonder server
* Apache Spark groepen
* Azure Data Factory Integration runtimes, pijp lijnen en gegevens sets.

## <a name="workspace-encryption-configuration"></a>Configuratie van werk ruimte versleuteling

U kunt werk ruimten configureren om dubbele versleuteling in te scha kelen met een door de klant beheerde sleutel op het moment dat de werk ruimte wordt gemaakt. Selecteer de optie dubbele versleuteling inschakelen met een door de klant beheerde sleutel op het tabblad Beveiliging bij het maken van uw nieuwe werk ruimte. U kunt ervoor kiezen om een sleutel-id-URI in te voeren of een selectie te selecteren in een lijst met sleutel kluizen in **dezelfde regio** als de werk ruimte. Voor de Key Vault zelf moet de **beveiliging opschonen zijn ingeschakeld**.

> [!IMPORTANT]
> Op dit moment kan de configuratie-instelling voor dubbele versleuteling niet meer worden gewijzigd nadat de werk ruimte is gemaakt.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Dit diagram toont de optie die moet worden geselecteerd om een werk ruimte in te scha kelen voor dubbele versleuteling met een door de klant beheerde sleutel.":::

### <a name="key-access-and-workspace-activation"></a>Toegang tot sleutels en de werk ruimte activeren

Het Azure Synapse-versleutelings model met door de klant beheerde sleutels omvat de werk ruimte die toegang heeft tot de sleutels in Azure Key Vault om zo nodig te versleutelen en te ontsleutelen. De sleutels worden toegankelijk gemaakt voor de werk ruimte via een toegangs beleid of Azure Key Vault RBAC-toegang ([Preview](../../key-vault/general/rbac-guide.md)). Wanneer u machtigingen verleent via een Azure Key Vault toegangs beleid, kiest u de optie ' alleen toepassing ' tijdens het maken van een beleid.

 De beheerde identiteit van de werk ruimte moet de benodigde machtigingen hebben voor de sleutel kluis voordat de werk ruimte kan worden geactiveerd. Deze gefaseerde benadering voor het activeren van werk ruimten zorgt ervoor dat de gegevens in de werk ruimte worden versleuteld met de door de klant beheerde sleutel. Versleuteling kan worden in-of uitgeschakeld voor toegewezen SQL-groepen. elke groep is standaard niet ingeschakeld voor versleuteling.

#### <a name="permissions"></a>Machtigingen

Voor het versleutelen of ontsleutelen van gegevens in rust, moet de beheerde identiteit van de werk ruimte de volgende machtigingen hebben:
* WrapKey (als u een sleutel wilt invoegen in Key Vault bij het maken van een nieuwe sleutel).
* Sleutel uitpakken (om de sleutel voor ontsleuteling op te halen).
* Get (lezen van het open bare deel van een sleutel)

#### <a name="workspace-activation"></a>Activering van werk ruimte

Nadat uw werk ruimte (met dubbele versleuteling is ingeschakeld) is gemaakt, blijft deze in de status in behandeling totdat de activering slaagt. De werk ruimte moet worden geactiveerd voordat u alle functionaliteit volledig kunt gebruiken. U kunt bijvoorbeeld een nieuwe exclusieve SQL-groep maken zodra de activering is geslaagd. Verleen de werk ruimte beheerde identiteits toegang tot de sleutel kluis en klik op de activerings koppeling in de werk ruimte Azure Portal banner. Zodra de activering is voltooid, is de werk ruimte klaar voor gebruik met de zekerheid dat alle gegevens in de app worden beveiligd met uw door de klant beheerde sleutel. Zoals eerder is vermeld, moet de sleutel kluis de beveiliging opschonen ingeschakeld hebben om de activering te volt ooien.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Dit diagram toont de banner met de activerings koppeling voor de werk ruimte.":::


### <a name="manage-the-workspace-customer-managed-key"></a>De door de klant beheerde sleutel van de werk ruimte beheren 

U kunt de door de klant beheerde sleutel die wordt gebruikt voor het versleutelen van gegevens van de **versleutelings pagina in** de Azure portal wijzigen. Hier kunt u ook een nieuwe sleutel kiezen met behulp van een sleutel-id of een sleutel kluis selecteren waartoe u toegang hebt in dezelfde regio als de werk ruimte. Als u een sleutel in een andere sleutel kluis kiest uit de sjablonen die eerder zijn gebruikt, verleent u de werk ruimte Managed Identity ' Get ', ' wrap ' en ' Unwrap ' voor de nieuwe sleutel kluis. De werk ruimte valideert de toegang tot de nieuwe sleutel kluis en alle gegevens in de werk ruimte worden opnieuw versleuteld met de nieuwe sleutel.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Dit diagram toont de sectie werkruimte versleuteling in de Azure Portal.":::

Azure Key kluizen-beleids regels voor automatische, periodieke rotatie van sleutels of acties op de sleutels kunnen resulteren in het maken van nieuwe sleutel versies. U kunt ervoor kiezen om alle gegevens in de werk ruimte opnieuw te versleutelen met de meest recente versie van de actieve sleutel. Als u het opnieuw wilt versleutelen, wijzigt u de sleutel in de Azure Portal naar een tijdelijke sleutel en gaat u terug naar de sleutel die u wilt gebruiken voor versleuteling. Als voor beeld: als u gegevens versleuteling wilt bijwerken met de nieuwste versie van actieve sleutel Key1, wijzigt u de door de klant beheerde sleutel van de werk ruimte in de tijdelijke sleutel, Key2. Wacht totdat de versleuteling is voltooid. Schakel vervolgens de door de klant beheerde sleutel terug naar Key1-data in de werk ruimte en versleuteld met de meest recente versie van Key1.

> [!NOTE]
> Azure Synapse Analytics werkt actief aan het toevoegen van functionaliteit voor het automatisch opnieuw versleutelen van gegevens wanneer er nieuwe sleutel versies worden gemaakt. Als deze functionaliteit niet beschikbaar is, dwingt u de gegevens opnieuw te versleutelen met behulp van het proces dat hierboven wordt beschreven, zodat de consistentie in uw werk ruimte wordt gewaarborgd.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL Transparent Data Encryption met door service beheerde sleutels

SQL Transparent Data Encryption (TDE) is beschikbaar voor toegewezen SQL-groepen in werk ruimten die *niet* zijn ingeschakeld voor dubbele versleuteling. In dit type werk ruimte wordt een door een service beheerde sleutel gebruikt om dubbele versleuteling te bieden voor de gegevens in de toegewezen SQL-groepen. TDE met de door de service beheerde sleutel kan worden ingeschakeld of uitgeschakeld voor afzonderlijke toegewezen SQL-groepen.

## <a name="next-steps"></a>Volgende stappen

[Ingebouwd Azure-beleid gebruiken voor het implementeren van versleutelings beveiliging voor Synapse-werk ruimten](../policy-reference.md)

