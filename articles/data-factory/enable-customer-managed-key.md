---
title: Azure Data Factory versleutelen met door de klant beheerde sleutel
description: Data Factory-beveiliging verbeteren met Bring Your Own Key (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: c6c376e44c6135a800e6f7e281f8ea85b828329a
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443867"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Azure Data Factory versleutelen met door de klant beheerde sleutels

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory codeert data-at-rest, waaronder entiteitsdefinities en gegevens die zijn opgeslagen in de cache terwijl uitvoeringen bezig zijn. Standaard worden gegevens versleuteld met een willekeurig gegenereerde, door Microsoft beheerde sleutel die uniek is toegewezen aan uw data factory. Voor extra beveiligings garanties kunt u nu Bring Your Own Key (BYOK) inschakelen met de functie door de klant beheerde sleutels in Azure Data Factory. Wanneer u een door de klant beheerde sleutel opgeeft, gebruikt Data Factory __zowel__ de systeemsleutel van de fabriek als de CMK om klantgegevens te versleutelen. Wanneer een van deze ontbreekt, wordt toegang tot gegevens en factory geweigerd.

Azure Key Vault is vereist om door de klant beheerde sleutels op te slaan. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. Key Vault en Data Factory moeten zich in dezelfde Azure Active Directory (Azure AD)-tenant en in dezelfde regio bevinden, maar ze kunnen wel in verschillende abonnementen zijn. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Azure Key Vault.

## <a name="about-customer-managed-keys"></a>Over door de klant beheerde sleutels

In het volgende diagram ziet u hoe Data Factory Azure Active Directory en Azure Key Vault gebruikt om aanvragen te maken met de door de klant beheerde sleutel:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagram waarin wordt getoond hoe door de klant beheerde sleutels worden gebruikt in Azure Data Factory.":::

In de volgende lijst worden de genummerde stappen in het diagram uitgelegd:

1. Een Azure Key Vault-beheerder verleent machtigingen voor versleutelingssleutels aan de beheerde identiteit die is gekoppeld aan de Data Factory
1. Een Data Factory-beheerder schakelt de functie voor de door de klant beheerde sleutel in de factory in
1. Data Factory gebruikt de beheerde identiteit die is gekoppeld aan de factory om de toegang tot Azure Key Vault via Azure Active Directory te verifiëren
1. Data Factory verpakt de versleutelingssleutel voor de factory met de klantensleutel in Azure Key Vault
1. Voor lees- en schrijfbewerkingen stuurt Data Factory aanvragen naar Azure Key Vault om de versleutelingssleutel van het account uit te pakken om versleutelings- en ontsleutelingsbewerkingen uit te voeren

Er zijn twee manieren om door de klant beheerde sleutel versleuteling toe te voegen aan gegevens fabrieken. Er wordt tijdens het maken van de fabriek een tijd in Azure Portal en de andere is het maken van de fabriek in Data Factory gebruikers interface.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Vereisten: Azure Key Vault configureren en sleutels genereren

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Voorlopig verwijderen en Niet wissen inschakelen op Azure Key Vault

Voor het gebruik van door de klant beheerde sleutels met Data Factory moeten op de Key Vault twee eigenschappen worden ingesteld, __Voorlopig verwijderen__ en __Niet leegmaken__. Deze eigenschappen kunnen worden ingeschakeld met PowerShell of Azure CLI in een nieuwe of bestaande sleutelkluis. Zie voor meer informatie over het inschakelen van deze eigenschappen voor een bestaande sleutel kluis [Azure Key Vault Recovery Management met zacht verwijderen en beveiliging opschonen](../key-vault/general/key-vault-recovery.md)

Als u een nieuwe Azure Key Vault maakt via Azure Portal, kunnen __Voorlopig verwijderen__ en __Niet leegmaken__ als volgt worden ingeschakeld:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Scherm afbeelding die laat zien hoe u de beveiliging van zacht verwijderen en leegmaken kunt inschakelen bij het maken van Key Vault.":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Data Factory-toegang verlenen tot Azure Key Vault

Zorg ervoor dat Azure Key Vault en Azure Data Factory zich in dezelfde Azure Active Directory (Azure AD)-Tenant en in _dezelfde regio_ bevinden. Ken vanuit Azure Key Vault Access Control data factory volgende machtigingen toe: de _Get_-, _Unwrap_-en _wrap-toets_. Deze machtigingen zijn vereist om door de klant beheerde sleutels in Data Factory in te schakelen.

* Als u een door de klant beheerde sleutel versleuteling wilt toevoegen [nadat de fabriek is gemaakt in Data Factory gebruikers interface](#post-factory-creation-in-data-factory-ui), moet u ervoor zorgen dat de beheerde service-identiteit (MSI) van Data Factory de drie machtigingen heeft voor Key Vault
* Als u de versleuteling van de door de klant beheerde sleutel [tijdens het maken van de fabriek in azure Portal](#during-factory-creation-in-azure-portal)wilt toevoegen, moet u ervoor zorgen dat de door de gebruiker toegewezen beheerde identiteit (UA-MI) de drie machtigingen heeft voor Key Vault

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Scherm afbeelding die laat zien hoe u Data Factory toegang tot Key Vault inschakelt.":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Door de klant beheerde sleutel genereren of uploaden naar Azure Key Vault

U kunt een eigen sleutel maken en deze opslaan in een sleutel kluis. U kunt ook de Azure Key Vault-Api's gebruiken om sleutels te genereren. Alleen 2048-bits RSA-sleutels worden ondersteund met Data Factory-versleuteling. Zie [Over sleutels, geheimen en certificaten](../key-vault/general/about-keys-secrets-certificates.md) voor meer informatie.

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Scherm opname waarin wordt getoond hoe Customer-Managed sleutel moet worden gegenereerd.":::

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

### <a name="post-factory-creation-in-data-factory-ui"></a>Het maken van een fabriek in Data Factory gebruikers interface

In deze sectie wordt het proces beschreven voor het toevoegen van door de klant beheerde sleutel versleuteling in Data Factory gebruikers interface, _nadat_ de fabriek is gemaakt.

> [!NOTE]
> Een door de klant beheerde sleutel kan alleen in een lege data factory worden geconfigureerd. De data factory kan geen resources bevatten als gekoppelde services, pijplijnen en gegevensstromen. Het wordt aangeraden om de door de klant beheerde sleutel in te schakelen nadat de factory is gemaakt.

> [!IMPORTANT]
> Deze methode werkt niet met beheerde virtuele-netwerk fabrieken. Neem de [alternatieve route](#during-factory-creation-in-azure-portal)door als u dergelijke fabrieken wilt versleutelen.

1. Zorg ervoor dat de Managed Service Identity van de data factory (MSI) _Get_-, _wrap_ -en _key_ -machtigingen heeft voor Key Vault.

1. Zorg ervoor dat de Data Factory leeg is. Het data factory kan geen resources bevatten, zoals gekoppelde services, pijp lijnen en gegevens stromen. Voor nu resulteert het implementeren van de door de klant beheerde sleutel naar een niet-lege factory in een fout.

1. Als u de sleutel-URI in Azure Portal wilt zoeken, gaat u naar Azure Key Vault en selecteert u de instelling Sleutels. Selecteer de gewenste sleutel en selecteer vervolgens de sleutel om de versies ervan weer te geven. Een sleutelversie selecteren om de instellingen weer te geven

1. Kopieer de waarde van het veld sleutel-id, waarmee u de URI- :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="scherm afbeelding van de sleutel-URI van Key Vault kunt ophalen.":::

1. Start Azure Data Factory Portal en ga met de navigatiebalk aan de linkerkant naar de Data Factory-beheerportal

1. Klik op de scherm afbeelding van de __klant beheerd-sleutel__ pictogram om de door de :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="klant beheerde sleutel in Data Factory gebruikers interface in te scha kelen.":::

1. Voer de URI in voor de door de klant beheerde sleutel die u eerder hebt gekopieerd

1. Klik op __Opslaan__ en door de klant beheerde sleutelversleuteling wordt ingeschakeld voor Data Factory

### <a name="during-factory-creation-in-azure-portal"></a>Tijdens het maken van de fabriek in Azure Portal

In deze sectie worden de stappen beschreven voor het toevoegen van door de klant beheerde sleutel versleuteling in Azure Portal _tijdens_ de fabrieks implementatie.

Data Factory moet eerst door de klant beheerde sleutel ophalen van Key Vault om de fabriek te versleutelen. Omdat de fabrieks implementatie nog wordt uitgevoerd, is Managed Service Identity (MSI) nog niet beschikbaar voor verificatie met Key Vault. Om deze methode te gebruiken, moet de klant een door de gebruiker toegewezen beheerde identiteit (UA-MI) toewijzen aan data factory. We gaan ervan uit dat de rollen die zijn gedefinieerd in de UA-MI en verifiëren met Key Vault.

Zie [beheerde identiteits typen](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) en [roltoewijzing voor door de gebruiker toegewezen beheerde identiteit voor](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)meer informatie over door de gebruiker toegewezen beheerde identiteit.

1. Zorg ervoor dat de door de gebruiker toegewezen beheerde identiteit (UA-MI) _Get_, _dewrap sleutel_ en inpakken van _sleutel_ machtigingen voor Key Vault

1. Schakel op het tabblad __Geavanceerd__ het selectie vakje in om versleuteling in te _scha kelen met behulp van een door de gebruiker beheerde sleutel_ 
   :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="scherm afbeelding van het tabblad Geavanceerd voor het maken van Data Factory in azure Portal.":::

1. Geef de URL op voor de door de klant beheerde sleutel die is opgeslagen in Key Vault

1. Selecteer een geschikte door de gebruiker toegewezen beheerde identiteit voor verificatie met Key Vault

1. Door gaan met de fabrieks implementatie

## <a name="update-key-version"></a>Sleutelversie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u data factory bijwerken om de nieuwe versie te gebruiken. Volg dezelfde stappen zoals beschreven in de sectie [Data Factory gebruikers interface](#post-factory-creation-in-data-factory-ui), waaronder:

1. Zoek de URI voor de nieuwe sleutelversie via Azure Key Vault Portal

1. Ga naar de instelling __Door de klant beheerde sleutel__

1. Vervang en plak de URI voor de nieuwe sleutel

1. Klik op __Opslaan__ en Data Factory wordt nu versleuteld met de nieuwe sleutelversie

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Als u de sleutel wilt wijzigen die wordt gebruikt voor Data Factory-versleuteling, moet u de instellingen in Data Factory handmatig bijwerken. Volg dezelfde stappen zoals beschreven in de sectie [Data Factory gebruikers interface](#post-factory-creation-in-data-factory-ui), waaronder:

1. De URI voor de nieuwe sleutel zoeken via Azure Key Vault Portal

1. Ga naar de instelling __Door de klant beheerde sleutel__

1. Vervang en plak de URI voor de nieuwe sleutel

1. Klik op __Opslaan__ en Data Factory wordt nu versleuteld met de nieuwe sleutel

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Zodra de functie voor de door de klant beheerde sleutel is ingeschakeld, kunt u de extra beveiligingsstap niet meer verwijderen. We verwachten altijd een door de klant verschafte sleutel om factory en gegevens te versleutelen.

## <a name="next-steps"></a>Volgende stappen

Doorloop de [zelfstudies](tutorial-copy-data-dot-net.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's.