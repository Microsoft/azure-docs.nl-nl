---
title: Language Understanding-service versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). Dit artikel heeft betrekking op gegevens versleuteling in rust voor Language Understanding (LUIS) en hoe u CMK kunt inschakelen en beheren.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 2dcfff005eaaac034f5fed13b6d4d18e20d2afae
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524484"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Language Understanding-service versleuteling van gegevens in rust

Met de Language Understanding-service worden uw gegevens automatisch versleuteld wanneer deze in de cloud worden bewaard. De Language Understanding-service versleuteling beschermt uw gegevens en helpt u te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie.

## <a name="about-cognitive-services-encryption"></a>Over Cognitive Services versleuteling

Gegevens worden versleuteld en ontsleuteld met behulp van [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-compatibele 256-bits AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) versleuteling. Versleuteling en ontsleuteling zijn transparant, wat betekent dat versleuteling en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om van versleuteling te kunnen profiteren.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Uw abonnement maakt standaard gebruik van door Microsoft beheerde versleutelingssleutels. Er is ook de mogelijkheid om uw abonnement te beheren met uw eigen sleutels, genaamd door de klant beheerde sleutels (CMK). CMK biedt meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelingssleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Er is ook een optie voor het beheren van uw abonnement met uw eigen sleutels. Door de klant beheerde sleutels (CMK), ook wel bekend als het nemen van uw eigen sleutel (BYOK), bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelingssleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.

U moet Azure Key Vault gebruiken om door de klant beheerde sleutels op te slaan. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. De Cognitive Services resource en de sleutel kluis moeten zich in dezelfde regio bevinden en in dezelfde Azure Active Directory (Azure AD)-Tenant, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.

### <a name="customer-managed-keys-for-language-understanding"></a>Door de klant beheerde sleutels voor Language Understanding

Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [Luis-Service Customer-Managed](https://aka.ms/cogsvc-cmk)en verzendt u het aanvraag formulier. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Na goed keuring voor het gebruik van CMK met LUIS, moet u een nieuwe Language Understanding resource maken op basis van de Azure Portal en E0 selecteren als prijs categorie. De nieuwe SKU werkt hetzelfde als de F0-SKU die al beschikbaar is, met uitzonde ring van CMK. Gebruikers kunnen geen upgrade uitvoeren van de F0 naar de nieuwe E0-SKU.

![Installatie kopie van LUIS-abonnement](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>Beperkingen

Er zijn enkele beperkingen bij het gebruik van de E0-laag met bestaande/eerder gemaakte toepassingen:

* Migratie naar een E0-resource wordt geblokkeerd. Gebruikers kunnen hun apps alleen migreren naar F0 resources. Nadat u een bestaande resource naar F0 hebt gemigreerd, kunt u een nieuwe resource maken in de E0-laag. Meer informatie over [migratie vindt u hier](./luis-migration-authoring.md).  
* Het verplaatsen van toepassingen naar of van een E0-resource wordt geblokkeerd. Een tijdelijke oplossing voor deze beperking is het exporteren van uw bestaande toepassing en deze te importeren als een E0-resource.
* De Bing spell check-functie wordt niet ondersteund.
* Het registreren van eind gebruikers verkeer wordt uitgeschakeld als uw toepassing E0 is.
* De functie voor spraak gebeuren van de Azure bot-service wordt niet ondersteund voor toepassingen in de laag E0. Deze functie is beschikbaar via de Azure Bot Service, die geen ondersteuning biedt voor CMK.
* De speech gebeuren-functie van de portal vereist Azure Blob Storage. Zie [uw eigen opslag plaatsen](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)voor meer informatie.

### <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

Een nieuwe Cognitive Services resource wordt altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die aan de Cognitive Services-resource is gekoppeld. De beheerde identiteit is alleen beschikbaar nadat de resource is gemaakt met behulp van de prijs categorie voor CMK.

Zie voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor Cognitive Services versleuteling:

- [Door de klant beheerde sleutels configureren met Key Vault voor Cognitive Services versleuteling van de Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Door door de klant beheerde sleutels in te scha kelen, wordt ook een door het systeem toegewezen beheerde identiteit, een functie van Azure AD, ingeschakeld. Zodra de door het systeem toegewezen beheerde identiteit is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Na registratie krijgt de beheerde identiteit toegang tot de Key Vault geselecteerd tijdens de installatie van de door de klant beheerde sleutel. Meer informatie over [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md)vindt u in.

> [!IMPORTANT]
> Als u door het systeem toegewezen beheerde identiteiten uitschakelt, wordt de toegang tot de sleutel kluis verwijderd en worden alle gegevens die zijn versleuteld met de klant sleutels niet meer toegankelijk. Alle functies, afhankelijk van deze gegevens, werken niet meer.

> [!IMPORTANT]
> Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen onder de voor vallen. Als u het abonnement, de resource groep of de resource van een Azure AD-Directory vervolgens naar een andere verplaatst, wordt de beheerde identiteit die aan de resource is gekoppeld, niet overgezet naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet de instellingen **voorlopig verwijderen** en **niet wissen** in de sleutel kluis inschakelen.

Alleen RSA-sleutels met een grootte van 2048 worden ondersteund met Cognitive Services versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](../../key-vault/general/about-keys-secrets-certificates.md)voor meer informatie over sleutels.

### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u de Cognitive Services resource bijwerken om de nieuwe sleutel-URI te gebruiken. Voor informatie over het bijwerken van de resource voor het gebruik van een nieuwe versie van de sleutel in de Azure Portal, zie de sectie met de titel **Update de sleutel versie** in [door de klant beheerde sleutels configureren voor Cognitive Services met behulp van de Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md).

Als u de sleutel roteert, wordt het opnieuw versleutelen van gegevens in de bron niet geactiveerd. Er is geen verdere actie vereist van de gebruiker.

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](/powershell/module/az.keyvault//) of [Azure Key Vault cli](/cli/azure/keyvault)(Engelstalig) voor meer informatie. Toegang intrekken effectief blokkeert de toegang tot alle gegevens in de Cognitive Services resource, omdat de versleutelings sleutel niet toegankelijk is voor Cognitive Services.

## <a name="next-steps"></a>Volgende stappen

* [Aanvraag formulier voor LUIS-service Customer-Managed](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](../../key-vault/general/overview.md)
