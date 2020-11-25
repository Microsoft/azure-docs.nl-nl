---
title: Gebruik de Azure Portal voor het configureren van door de klant beheerde sleutels
titleSuffix: Cognitive Services
description: Meer informatie over het gebruik van de Azure Portal voor het configureren van door de klant beheerde sleutels met Azure Key Vault. Door de klant beheerde sleutels bieden u de mogelijkheid om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 17dff6056564b421f0741f55ce8171f3251c94c9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026782"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

U moet Azure Key Vault gebruiken om door de klant beheerde sleutels op te slaan. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. De Cognitive Services resource en de sleutel kluis moeten zich in dezelfde regio bevinden en in dezelfde Azure Active Directory (Azure AD)-Tenant, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.

Wanneer een nieuwe Cognitive Services resource wordt gemaakt, wordt deze altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die aan de Cognitive Services-resource is gekoppeld. De beheerde identiteit is alleen beschikbaar nadat de resource is gemaakt met behulp van de prijs categorie die is vereist voor CMK.

Door door de klant beheerde sleutels in te scha kelen, wordt ook een door het systeem toegewezen [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md), een functie van Azure AD, ingeschakeld. Zodra de door het systeem toegewezen beheerde identiteit is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Na registratie krijgt de beheerde identiteit toegang tot de Key Vault geselecteerd tijdens de installatie van de door de klant beheerde sleutel. 

> [!IMPORTANT]
> Als u door het systeem toegewezen beheerde identiteiten uitschakelt, wordt de toegang tot de sleutel kluis verwijderd en worden alle gegevens die zijn versleuteld met de klant sleutels niet meer toegankelijk. Alle functies, afhankelijk van deze gegevens, werken niet meer.

> [!IMPORTANT]
> Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen onder de voor vallen. Als u het abonnement, de resource groep of de resource van een Azure AD-Directory vervolgens naar een andere verplaatst, wordt de beheerde identiteit die aan de resource is gekoppeld, niet overgezet naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

## <a name="configure-azure-key-vault"></a>Azure Key Vault configureren

Voor het gebruik van door de klant beheerde sleutels moeten twee eigenschappen worden ingesteld in de sleutel kluis, **zacht verwijderen** en **niet leeg** worden gemaakt. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met Power shell of Azure CLI in een nieuwe of bestaande sleutel kluis.

> [!IMPORTANT]
> Als u niet de opties **voorlopig verwijderen** en **niet wissen** hebt ingeschakeld en u de sleutel verwijdert, kunt u de gegevens in uw cognitieve service resource niet herstellen.

Voor meer informatie over het inschakelen van deze eigenschappen voor een bestaande sleutelkluis raadpleegt u de secties **Voorlopig verwijderen inschakelen** en **Beveiliging tegen leegmaken inschakelen** in een van de volgende artikelen:

- [Voorlopig verwijderen gebruiken met Power shell](../../key-vault/general/soft-delete-powershell.md).
- [Voorlopig verwijderen gebruiken met CLI](../../key-vault/general/soft-delete-cli.md).

Alleen RSA-sleutels met een grootte van 2048 worden ondersteund met Azure Storage versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](../../key-vault/general/about-keys-secrets-certificates.md)voor meer informatie over sleutels.

## <a name="enable-customer-managed-keys-for-your-resource"></a>Door de klant beheerde sleutels voor uw resource inschakelen

Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Navigeer naar uw Cognitive Services-resource.
1. Klik op de Blade **instellingen** voor uw Cognitive Services bron op **versleuteling**. Selecteer de optie door de **klant beheerde sleutels** , zoals wordt weer gegeven in de volgende afbeelding.

    ![Scherm afbeelding die laat zien hoe u door de klant beheerde sleutels selecteert](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Een sleutel opgeven

Nadat u door de klant beheerde sleutels hebt ingeschakeld, hebt u de mogelijkheid om een sleutel op te geven die u wilt koppelen aan de Cognitive Services resource.

### <a name="specify-a-key-as-a-uri"></a>Een sleutel opgeven als een URI

Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutel-URI in de Azure Portal wilt zoeken, navigeert u naar uw sleutel kluis en selecteert u de instelling **sleutels** . Selecteer de gewenste sleutel en klik vervolgens op de sleutel om de versies ervan weer te geven. Selecteer een sleutel versie om de instellingen voor die versie weer te geven.
1. Kopieer de waarde van het veld **sleutel-id** , dat de URI levert.

    ![Scherm opname van sleutel kluis sleutel-URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. Kies in de **versleutelings** instellingen voor uw opslag account de optie **sleutel-URI opgeven** .
1. Plak de URI die u hebt gekopieerd in het veld **sleutel-URI** .

   ![Scherm afbeelding die laat zien hoe de sleutel-URI moet worden ingevoerd](../media/cognitive-services-encryption/ssecmk2.png)

1. Geef het abonnement op dat de sleutel kluis bevat.
1. Sla uw wijzigingen op.

### <a name="specify-a-key-from-a-key-vault"></a>Een sleutel uit een sleutel kluis opgeven

Als u een sleutel van een sleutel kluis wilt opgeven, moet u eerst controleren of u een sleutel kluis hebt die een sleutel bevat. Voer de volgende stappen uit om een sleutel van een sleutel kluis op te geven:

1. Kies de optie **selecteren uit Key Vault** .
1. Selecteer de sleutel kluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutel kluis.

   ![Scherm afbeelding met door de klant beheerde sleutel optie](../media/cognitive-services-encryption/ssecmk3.png)

1. Sla uw wijzigingen op.

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, werkt u de Cognitive Services resource bij om de nieuwe versie te gebruiken. Volg deze stappen:

1. Navigeer naar uw Cognitive Services-resource en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe sleutel versie. U kunt ook de sleutel kluis en de sleutel opnieuw selecteren om de versie bij te werken.
1. Sla uw wijzigingen op.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Voer de volgende stappen uit om de sleutel te wijzigen die wordt gebruikt voor versleuteling:

1. Navigeer naar uw Cognitive Services-resource en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe sleutel. U kunt ook de sleutel kluis selecteren en een nieuwe sleutel kiezen.
1. Sla uw wijzigingen op.

## <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u de Cognitive Services resource bijwerken om de nieuwe sleutel-URI te gebruiken. Zie [de sleutel versie bijwerken](#update-the-key-version)voor meer informatie over het bijwerken van de resource voor het gebruik van een nieuwe versie van de sleutel in de Azure Portal.

Als u de sleutel roteert, wordt het opnieuw versleutelen van gegevens in de bron niet geactiveerd. Er is geen verdere actie vereist van de gebruiker.

## <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](/powershell/module/az.keyvault//) of [Azure Key Vault cli](/cli/azure/keyvault)(Engelstalig) voor meer informatie. Toegang intrekken effectief blokkeert de toegang tot alle gegevens in de Cognitive Services resource, omdat de versleutelings sleutel niet toegankelijk is voor Cognitive Services.

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uitschakelt, wordt uw Cognitive Services-resource vervolgens versleuteld met door micro soft beheerde sleutels. Voer de volgende stappen uit om door de klant beheerde sleutels uit te scha kelen:

1. Navigeer naar uw Cognitive Services-resource en geef de **versleutelings** instellingen weer.
1. Schakel het selectie vakje naast de instelling **uw eigen sleutel gebruiken** uit.
