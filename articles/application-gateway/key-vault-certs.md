---
title: TLS-beëindiging met Azure Key Vault certificaten
description: Meer informatie over hoe u Azure-toepassing gateway kunt integreren met Key Vault voor server certificaten die zijn gekoppeld aan listeners met HTTPS-functionaliteit.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 694868f2a75cc66bf9e3ede9d12e30a2cc3d7af9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185934"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-beëindiging met Key Vault-certificaten

[Azure Key Vault](../key-vault/general/overview.md) is een door een platform beheerd geheim archief dat u kunt gebruiken voor het beveiligen van geheimen, sleutels en TLS/SSL-certificaten. Azure-toepassing gateway ondersteunt de integratie met Key Vault voor server certificaten die zijn gekoppeld aan listeners met HTTPS-functionaliteit. Deze ondersteuning is beperkt tot de v2-SKU van Application Gateway.

Key Vault-integratie biedt twee modellen voor het beëindigen van TLS:

- U kunt expliciet TLS/SSL-certificaten opgeven die zijn gekoppeld aan de listener. Dit model is de traditionele manier om TLS/SSL-certificaten door te geven aan Application Gateway voor het beëindigen van TLS.
- U kunt eventueel een verwijzing naar een bestaand Key Vault certificaat of geheim opgeven wanneer u een HTTPS-listener maakt.

Application Gateway integratie met Key Vault biedt veel voor delen, waaronder:

- Sterkere beveiliging, omdat TLS/SSL-certificaten niet rechtstreeks worden afgehandeld door het team voor toepassings ontwikkeling. Met integratie kan een afzonderlijk beveiligings team het volgende doen:
  * Stel toepassings gateways in.
  * De levens cyclus van de toepassings gateway beheren.
  * Machtigingen verlenen aan geselecteerde toepassings gateways om toegang te krijgen tot certificaten die zijn opgeslagen in de sleutel kluis.
- Ondersteuning voor het importeren van bestaande certificaten in uw sleutel kluis. Of gebruik Key Vault Api's om nieuwe certificaten te maken en te beheren met een van de vertrouwde Key Vault partners.
- Ondersteuning voor het automatisch verlengen van certificaten die zijn opgeslagen in uw sleutel kluis.

Application Gateway ondersteunt momenteel alleen door software gevalideerde certificaten. HSM (Hardware Security module)-gevalideerde certificaten worden niet ondersteund. Nadat Application Gateway is geconfigureerd voor het gebruik van Key Vault certificaten, haalt de instanties het certificaat op Key Vault en installeert ze lokaal voor TLS-beëindiging. De exemplaren pollen ook Key Vault met een interval van 4 uur om een vernieuwde versie van het certificaat op te halen, als dit bestaat. Als er een bijgewerkt certificaat wordt gevonden, wordt het TLS/SSL-certificaat dat momenteel is gekoppeld aan de HTTPS-listener automatisch gedraaid.

> [!NOTE]
> Het Azure Portal ondersteunt alleen certificaten met alleen een sleutel kluis, geen geheimen. Application Gateway ondersteunt nog steeds referentie geheimen van de sleutel kluis, maar alleen via niet-Portal resources zoals Power shell, CLI, API, ARM-sjablonen, enzovoort. 

## <a name="how-integration-works"></a>Hoe de integratie werkt

Voor de integratie van Application Gateway met Key Vault is een configuratie proces in drie stappen vereist:

1. **Een door de gebruiker toegewezen beheerde identiteit maken**

   U maakt of hergebruikt een bestaande door de gebruiker toegewezen beheerde identiteit, die Application Gateway gebruikt om certificaten uit Key Vault namens u op te halen. Zie een [rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)voor meer informatie. Met deze stap maakt u een nieuwe identiteit in de Azure Active Directory Tenant. De identiteit wordt vertrouwd door het abonnement dat wordt gebruikt om de identiteit te maken.

1. **Uw sleutel kluis configureren**

   Vervolgens importeert u een bestaand certificaat of maakt u een nieuwe in uw sleutel kluis. Het certificaat wordt gebruikt door toepassingen die via de toepassings gateway worden uitgevoerd. In deze stap kunt u ook een sleutel kluis geheim gebruiken dat is opgeslagen als een met een wacht woord kleiner, met base 64 gecodeerd PFX-bestand. We raden u aan om een certificaat type te gebruiken vanwege de mogelijkheid tot het vernieuwen van de sleutel die beschikbaar is voor de objecten van het type certificaat. Nadat u een certificaat of een geheim hebt gemaakt, definieert u het toegangs beleid in de sleutel kluis zodat de identiteit kan worden toegekend toegang tot het geheim *te krijgen.*
   
   > [!IMPORTANT]
   > Voor Application Gateway is momenteel Key Vault toegang tot alle netwerken toegestaan om de integratie te benutten. Key Vault-integratie wordt niet ondersteund wanneer Key Vault is ingesteld op alleen persoonlijke eind punten toestaan en netwerk toegang selecteren. Ondersteuning voor privé-en Select-netwerken vindt u in de Works-functie voor volledige integratie van Key Vault met Application Gateway. 

   > [!NOTE]
   > Als u de toepassings gateway via een ARM-sjabloon implementeert door gebruik te maken van de Azure CLI of Power shell of via een Azure-toepassing die is geïmplementeerd vanuit het Azure Portal, wordt het SSL-certificaat opgeslagen in de sleutel kluis als een base64-gecodeerd PFX-bestand. U moet de stappen in [Use Azure Key Vault volt ooien om de waarde van Secure para meter door te geven tijdens de implementatie](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > Het is met name belang rijk om `enabledForTemplateDeployment` in te stellen op `true` . Het certificaat is mogelijk geen wacht woord of het kan een wacht woord hebben. In het geval van een certificaat met een wacht woord toont het volgende voor beeld een mogelijke configuratie voor de `sslCertificates` vermelding in de `properties` voor de arm-sjabloon configuratie voor een app-gateway. De waarden van `appGatewaySSLCertificateData` en `appGatewaySSLCertificatePassword` worden opgezocht uit de sleutel kluis, zoals beschreven in de sectie [referentie geheimen met een dynamische id](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Volg de verwijzingen naar achter `parameters('secretName')` om te zien hoe de zoek actie plaatsvindt. Als het certificaat geen wacht woord is, laat u het `password` item weg.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **De toepassingsgateway configureren**

   Nadat u de twee voor gaande stappen hebt voltooid, kunt u een bestaande toepassings gateway instellen of wijzigen voor het gebruik van de door de gebruiker toegewezen beheerde identiteit. Zie [set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity)voor meer informatie.

   U kunt ook het TLS/SSL-certificaat van de HTTP-listener zo configureren dat deze verwijst naar de volledige URI van het Key Vault certificaat of de geheim-ID.

   ![Sleutel kluis certificaten](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Volgende stappen

[TLS-beëindiging met Key Vault certificaten configureren met behulp van Azure PowerShell](configure-keyvault-ps.md)
