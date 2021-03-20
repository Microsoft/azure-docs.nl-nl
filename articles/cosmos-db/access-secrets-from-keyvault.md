---
title: Key Vault gebruiken om Azure Cosmos DB sleutels op te slaan en te openen
description: Gebruik Azure Key Vault om Azure Cosmos DB connection string, sleutels, eind punten op te slaan en te openen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a9bea0664f99a21ac734de666c802e9875ff00b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359316"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Cosmos-sleutels beveiligen met Azure Key Vault 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> De aanbevolen oplossing voor toegang tot Azure Cosmos DB sleutels is het gebruik van een door het [systeem toegewezen beheerde identiteit](managed-identity-based-authentication.md). Als uw service niet kan profiteren van beheerde identiteiten, gebruikt u de [oplossing op basis](certificate-based-authentication.md)van een certificaat. Als de oplossing voor beheerde identiteits oplossing en op basis van certificaten niet aan uw behoeften voldoet, moet u de onderstaande sleutel kluis oplossing gebruiken.

Wanneer u Azure Cosmos DB gebruikt voor uw toepassingen, kunt u toegang krijgen tot de data base, verzamelingen en documenten met behulp van het eind punt en de sleutel binnen het configuratie bestand van de app.  Het is echter niet veilig om sleutels en URL rechtstreeks in de toepassings code te plaatsen, omdat ze beschikbaar zijn in de Lees bare tekst indeling voor alle gebruikers. Zorg ervoor dat het eindpunt en de sleutels wel beschikbaar zijn, maar via een beveiligd mechanisme. Azure Key Vault kan u helpen bij het veilig opslaan en beheren van toepassingsgeheimen.

De volgende stappen zijn vereist om Azure Cosmos DB toegangs sleutels op te slaan en te lezen vanuit Key Vault:

* Een sleutelkluis maken  
* Azure Cosmos DB toegangs sleutels toevoegen aan de Key Vault  
* Een Azure-webtoepassing maken  
* De toepassing registreren & machtigingen verlenen om de Key Vault te lezen  


## <a name="create-a-key-vault"></a>Een sleutelkluis maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).  
2. Selecteer **een resource maken > beveiligings > Key Vault**.  
3. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:  
   * **Naam:** Geef een unieke naam op voor de Key Vault.  
   * **Abonnement:** Kies het abonnement dat u gaat gebruiken.  
   * Klik onder **resource groep** op **nieuwe maken** en voer een naam voor de resource groep in.  
   * Kies een locatie in de vervolgkeuzelijst Locatie.  
   * Wijzig de standaard instellingen van de andere opties.  
4. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Azure Cosmos DB toegangs sleutels toevoegen aan de Key Vault.
1. Ga naar het Key Vault dat u in de vorige stap hebt gemaakt en open het tabblad **geheimen** .  
2. Selecteer **+ genereren/importeren**, 

   * Selecteer **hand matig** voor **Upload opties**.
   * Geef een **naam** op voor uw geheim
   * Geef de connection string van uw Cosmos DB-account op in het veld **waarde** . En selecteer vervolgens **Maken**.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Een geheim maken":::

4. Nadat het geheim is gemaakt, opent u het en kopieert u de geheime id * * in de volgende indeling. U gebruikt deze id in de volgende sectie. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Een Azure-webtoepassing maken

1. Een Azure-webtoepassing maken of u kunt de app downloaden vanuit de [github-opslag plaats](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo). Het is een eenvoudige MVC-toepassing.  

2. Pak de gedownloade toepassing uit en open het bestand **HomeController. cs** . Werk de geheime ID in op de volgende regel:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Sla** het bestand op en **bouw** de oplossing.  
4. Implementeer de toepassing vervolgens naar Azure. Klik met de rechter muisknop op project en kies **publiceren**. Maak een nieuw app service-profiel (u kunt de app WebAppKeyVault1 noemen) en selecteer **publiceren**.   

5. Zodra de toepassing is geïmplementeerd. Ga vanuit het Azure Portal naar de web-app die u hebt geïmplementeerd en schakel de **beheerde service-identiteit** van deze toepassing in.  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Beheerde service-identiteit":::

Als u de toepassing nu wilt uitvoeren, wordt de volgende fout weer gegeven, omdat u geen machtiging hebt gekregen voor deze toepassing in Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="App geïmplementeerd zonder toegang":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>De toepassing registreren & machtigingen verlenen om de Key Vault te lezen

In deze sectie registreert u de toepassing met Azure Active Directory en geeft u machtigingen voor de toepassing om de Key Vault te lezen. 

1. Ga naar het Azure Portal en open de **Key Vault** die u hebt gemaakt in de vorige sectie.  

2. Open **toegangs beleid**, selecteer **+ nieuwe** zoeken de web-app die u hebt geïmplementeerd, selecteer machtigingen en selecteer **OK**.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Toegangsbeleid toevoegen":::

Als u de toepassing nu uitvoert, kunt u het geheim lezen van Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="App geïmplementeerd met geheim":::
 
Op dezelfde manier kunt u een gebruiker toevoegen voor toegang tot de sleutel kluis. U moet uzelf toevoegen aan de Key Vault door **toegangs beleid** te selecteren en vervolgens alle machtigingen te verlenen die u nodig hebt om de toepassing uit te voeren vanuit Visual Studio. Wanneer deze toepassing wordt uitgevoerd vanaf uw bureau blad, neemt u uw identiteit in beslag.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel over [firewall ondersteuning](how-to-configure-firewall.md) om een firewall te configureren voor Azure Cosmos db.
* Zie voor meer informatie over het configureren van het service-eind punt van de virtuele netwerk, [beveiligde toegang via het VNet-service-eindpunt](how-to-configure-vnet-service-endpoint.md) artikel
