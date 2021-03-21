---
title: Door de klant beheerde sleutels gebruiken om uw configuratie gegevens te versleutelen
description: Uw configuratie gegevens versleutelen met door de klant beheerde sleutels
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930497"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Door de klant beheerde sleutels gebruiken voor het versleutelen van uw app-configuratie gegevens
Met Azure-app configuratie wordt [gevoelige informatie op rest versleuteld](../security/fundamentals/encryption-atrest.md). Het gebruik van door de klant beheerde sleutels biedt verbeterde gegevens beveiliging door u de mogelijkheid te bieden om uw versleutelings sleutels te beheren.  Wanneer beheerde sleutel versleuteling wordt gebruikt, wordt alle gevoelige informatie in de app-configuratie versleuteld met een door de gebruiker verstrekte Azure Key Vault sleutel.  Dit biedt de mogelijkheid om de versleutelings sleutel op aanvraag te draaien.  Het biedt ook de mogelijkheid om de toegang tot gevoelige gegevens in de Azure-app configuratie in te trekken door de toegang tot de sleutel van het app-configuratie-exemplaar in te trekken.

## <a name="overview"></a>Overzicht 
Met Azure-app configuratie wordt gevoelige informatie op rest versleuteld met behulp van een 256-bits AES-versleutelings sleutel van micro soft. Elk app-configuratie-exemplaar heeft een eigen versleutelings sleutel die wordt beheerd door de service en die wordt gebruikt om gevoelige informatie te versleutelen. Gevoelige informatie bevat de waarden die worden gevonden in sleutel-waardeparen.  Wanneer door de klant beheerde sleutel mogelijkheid is ingeschakeld, gebruikt de app-configuratie een beheerde identiteit die is toegewezen aan het app-configuratie-exemplaar om te verifiëren met Azure Active Directory. De beheerde identiteit roept vervolgens Azure Key Vault en verloopt de versleutelings sleutel van het app-configuratie-exemplaar. De ingepakte versleutelings sleutel wordt vervolgens opgeslagen en de niet-gepakte versleutelings sleutel wordt gedurende één uur in de configuratie van de app opgeslagen. Met app-configuratie wordt de niet-ingepakte versie van de versleutelings sleutel van het app-configuratie-exemplaar per uur vernieuwd. Dit zorgt voor de beschik baarheid onder normale bedrijfs omstandigheden. 

>[!IMPORTANT]
> Als de identiteit die is toegewezen aan het app-configuratie-exemplaar niet langer wordt geautoriseerd om de versleutelings sleutel van het exemplaar op te slaan, of als de beheerde sleutel permanent wordt verwijderd, is het niet meer mogelijk om gevoelige informatie te ontsleutelen die is opgeslagen in het app-configuratie-exemplaar. Het gebruik van de functie voor het [verwijderen](../key-vault/general/soft-delete-overview.md) van Azure Key Vault verkleint de kans dat de versleutelings sleutel per ongeluk wordt verwijderd.

Wanneer gebruikers de functie voor door de klant beheerde sleutel inschakelen op hun Azure-app configuratie-exemplaar, kunnen ze de toegang tot hun gevoelige gegevens in de service beheren. De beheerde sleutel fungeert als basis versleutelings sleutel. Een gebruiker kan de toegang tot hun app-configuratie-exemplaar intrekken voor hun beheerde sleutel door het toegangs beleid voor de sleutel kluis te wijzigen. Wanneer deze toegang wordt ingetrokken, verliest de configuratie van de app de mogelijkheid om gebruikers gegevens binnen een uur te ontsleutelen. Op dit moment worden alle toegangs pogingen op het app-configuratie-exemplaar niet uitgevoerd. Deze situatie kan worden hersteld door de service toegang tot de beheerde sleutel opnieuw te verlenen.  Binnen één uur kan de app-configuratie gebruikers gegevens ontsleutelen en onder normale omstandigheden functioneren.

>[!NOTE]
>Alle Azure-app configuratie gegevens worden Maxi maal 24 uur opgeslagen in een geïsoleerde back-up. Dit omvat de niet-gepakte versleutelings sleutel. Deze gegevens zijn niet onmiddellijk beschikbaar voor het service-of service team. In het geval van een herstel na nood gevallen wordt de configuratie van Azure-app opnieuw ingetrokken voor de beheerde sleutel gegevens.

## <a name="requirements"></a>Vereisten
De volgende onderdelen zijn vereist voor het inschakelen van de door de klant beheerde sleutel mogelijkheid voor Azure-app configuratie:
- Configuratie-exemplaar van de Standard-laag Azure-app
- Azure Key Vault met de functies Soft-verwijderen en leegmaken-beveiliging ingeschakeld
- Een RSA-of RSA-HSM-sleutel in de Key Vault
    - De sleutel mag niet verlopen zijn, moet zijn ingeschakeld en er moeten functies voor terugloop en uitpakken zijn ingeschakeld

Zodra deze resources zijn geconfigureerd, blijven er twee stappen die Azure-app configuratie toestaan om de Key Vault sleutel te gebruiken:
1. Een beheerde identiteit toewijzen aan het Azure-app-configuratie-exemplaar
2. Ken de identiteit `GET` , `WRAP` en `UNWRAP` machtigingen toe in het toegangs beleid van het doel Key Vault.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Door de klant beheerde sleutel versleuteling inschakelen voor uw Azure-app-configuratie-exemplaar
Als u wilt beginnen, hebt u een correct geconfigureerd Azure-app-configuratie-exemplaar. Als u nog geen app-configuratie-exemplaar beschikbaar hebt, volgt u een van deze Quick starts om er een in te stellen:
- [Een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)
- [Een .NET core-app maken met Azure-app configuratie](quickstart-dotnet-core-app.md)
- [Een .NET Framework-app maken met Azure-appconfiguratie](quickstart-dotnet-app.md)
- [Een Java-lente-app maken met Azure-app configuratie](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de opdrachtregelinstructies in dit artikel kunt uitvoeren.  Deze heeft algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd, met inbegrip van de .NET Core SDK. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanuit shell.azure.com.  Meer informatie over Azure Cloud Shell vindt u door [onze documentatie te lezen](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Een Azure Key Vault maken en configureren
1. Maak een Azure Key Vault met behulp van de Azure CLI.  Houd er rekening mee dat zowel als door de `vault-name` `resource-group-name` gebruiker gegeven zijn en uniek moeten zijn.  We gebruiken `contoso-vault` en `contoso-resource-group` in deze voor beelden.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Schakel de optie zacht verwijderen en leegmaken-beveiliging in voor de Key Vault. Vervang de namen van de Key Vault ( `contoso-vault` ) en de resource groep ( `contoso-resource-group` ) die u in stap 1 hebt gemaakt.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Maak een Key Vault sleutel. Geef een unieke `key-name` naam op voor deze sleutel en vervang de namen van de Key Vault ( `contoso-vault` ) die u in stap 1 hebt gemaakt. Geef op of u liever `RSA` of `RSA-HSM` versleutelt.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Met de uitvoer van deze opdracht wordt de sleutel-ID (' Kid ') weer gegeven voor de gegenereerde sleutel.  Noteer de sleutel-ID die u later in deze oefening moet gebruiken.  De sleutel-ID heeft de volgende notatie: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  De sleutel-ID bestaat uit drie belang rijke onderdelen:
    1. Key Vault URI: ' https://{My Key kluis}. kluis. Azure. net
    1. Key Vault sleutel naam: {key name}
    1. Key Vault van sleutel versie: {sleutel versie}

1. Maak een door het systeem toegewezen beheerde identiteit met behulp van de Azure CLI, waarbij u de naam van het app-configuratie-exemplaar en de resource groep die in de vorige stappen wordt gebruikt, vervangt. De beheerde identiteit wordt gebruikt voor toegang tot de beheerde sleutel. We gebruiken `contoso-app-config` om de naam van een app-configuratie-exemplaar te illustreren:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    De uitvoer van deze opdracht bevat de principal-ID ("principalId") en de Tenant-ID ("tenandId") van de aan het systeem toegewezen identiteit.  Deze Id's worden gebruikt voor het verlenen van toegang tot de identiteit van de beheerde sleutel.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. De beheerde identiteit van het Azure-app-configuratie-exemplaar moet toegang hebben tot de sleutel om sleutel validatie, versleuteling en ontsleuteling uit te voeren. De specifieke set acties waartoe deze toegang nodig heeft: `GET` , `WRAP` en `UNWRAP` voor sleutels.  Het verlenen van de toegang vereist de principal-ID van de beheerde identiteit van het app-configuratie-exemplaar. Deze waarde werd verkregen in de vorige stap. Deze wordt hieronder weer gegeven als `contoso-principalId` . Machtiging verlenen aan de beheerde sleutel via de opdracht regel:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Zodra het configuratie-exemplaar van de Azure-app toegang heeft tot de beheerde sleutel, kunnen we de door de klant beheerde sleutel mogelijkheid in de service in te scha kelen met behulp van de Azure CLI. Haal de volgende eigenschappen op die zijn vastgelegd tijdens de stappen voor het maken van sleutels: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Uw Azure-app-configuratie-exemplaar is nu geconfigureerd voor het gebruik van een door de klant beheerde sleutel die is opgeslagen in Azure Key Vault.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u uw Azure-app-configuratie-exemplaar geconfigureerd voor het gebruik van een door de klant beheerde sleutel voor versleuteling.  Meer informatie over het [integreren van uw service met door Azure beheerde identiteiten](howto-integrate-azure-managed-service-identity.md).
