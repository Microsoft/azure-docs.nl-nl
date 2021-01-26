---
title: Gids voor Azure Key Vault-ontwikkelaars
description: Ontwikkel aars kunnen Azure Key Vault gebruiken voor het beheren van cryptografische sleutels binnen de Microsoft Azure omgeving.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 40ce5f55b4109fee0099110c17f02b33c3eff808
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791421"
---
# <a name="azure-key-vault-developers-guide"></a>Gids voor Azure Key Vault-ontwikkelaars

Met Key Vault kunt u veilig toegang krijgen tot gevoelige informatie in uw toepassingen:

- Sleutels, geheimen en certificaten zijn beveiligd zonder dat u zelf de code hoeft te schrijven en u kunt deze eenvoudig gebruiken vanuit uw toepassingen.
- U kunt klanten toestaan hun eigen sleutels, geheimen en certificaten te beheren, zodat u zich op de basis van de functies voor de software van de kern kan concentreren. Op deze manier zijn uw toepassingen niet verantwoordelijk voor de verantwoordelijkheid of de mogelijke aansprakelijkheid voor de Tenant sleutels, geheimen en certificaten van uw klanten.
- Uw toepassing kan gebruikmaken van sleutels voor ondertekening en versleuteling, maar blijft het sleutel beheer extern vanuit uw toepassing. Zie [about Keys](../keys/about-keys.md) (Engelstalig) voor meer informatie over sleutels
- U kunt referenties, zoals wacht woorden, toegangs sleutels en SAS-tokens, beheren door ze op te slaan in Key Vault als geheimen, Zie [over geheimen](../secrets/about-secrets.md)
- Certificaten beheren. Zie [over certificaten](../certificates/about-certificates.md) voor meer informatie.

Zie [Wat is Key Vault](overview.md)voor meer algemene informatie over Azure Key Vault.

## <a name="public-previews"></a>Open bare voor beelden

Er wordt regel matig een open bare preview van een nieuwe Key Vault-functie uitgebracht. Probeer de open bare preview-functies uit en laat ons weten wat u ervan vindt via het azurekeyvault@microsoft.com e-mail adres van feedback.

## <a name="creating-and-managing-key-vaults"></a>Sleutel kluizen maken en beheren

Key Vault beheer, vergelijkbaar met andere Azure-Services, wordt uitgevoerd via Azure Resource Manager service. Azure Resource Manager is de implementatie- en beheersservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-account kunt maken, bijwerken en verwijderen. Zie [Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor meer informatie.

Toegang tot de Management-laag wordt beheerd door op [rollen gebaseerd toegangs beheer van Azure](../../role-based-access-control/overview.md). In Key Vault kunt u met management Layer, ook wel beheer of beheergebied, sleutel kluizen en de kenmerken ervan maken en beheren, inclusief het toegangs beleid, maar niet de sleutels, geheimen en certificaten, die worden beheerd op gegevens vlak. U kunt vooraf gedefinieerde rol gebruiken `Key Vault Contributor` om beheer toegang toe te kennen aan Key Vault.     

**API'S en Sdk's voor sleutel kluis beheer:**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault)<br>[Snelstartgids](quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault)<br>[Snelstartgids](quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults)<br>[Snelstartgids](https://docs.microsoft.com/azure/key-vault/general/vault-create-template)|[Verwijzing](/dotnet/api/microsoft.azure.management.keyvault)|[Verwijzing](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Verwijzing](/java/api/com.microsoft.azure.management.keyvault)|[Verwijzing](/javascript/api/@azure/arm-keyvault)|

Zie [client bibliotheken](client-libraries.md) voor installatie pakketten en bron code.

Zie [Key Vault-beheer vlak](./secure-your-key-vault.md#management-plane-and-azure-rbac) voor meer informatie over Key Vault beheer vlak

## <a name="authenticate-to-key-vault-in-code"></a>Verifiëren voor Key Vault in code

Key Vault maakt gebruik van Azure AD-verificatie waarvoor Azure AD security principal toegang moet verlenen. Een beveiligings-principal voor Azure AD kan een gebruiker, een service-principal van de toepassing, een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)of een groep van elk type beveiligings-principals zijn.

### <a name="authentication-best-practices"></a>Aanbevolen procedures voor verificatie

Het is raadzaam beheerde identiteit te gebruiken voor toepassingen die zijn geïmplementeerd in Azure. Als u Azure-Services gebruikt die geen beheerde identiteit ondersteunen of als toepassingen op locatie worden geïmplementeerd, is de [Service-Principal met een certificaat](../../active-directory/develop/howto-create-service-principal-portal.md) een mogelijk alternatief. In dat scenario moet het certificaat worden opgeslagen in Key Vault en vaak worden gedraaid. Service-principals met geheim kunnen worden gebruikt voor ontwikkel-en test omgevingen, en lokaal of in Cloud Shell het gebruik van User Principal wordt aanbevolen.

Aanbevolen beveiligings-principals per omgeving:
- **Productie omgeving**:
  - Beheerde identiteit of Service-Principal met een certificaat
- **Test-en ontwikkelings omgevingen**:
  - Beheerde identiteit, Service-Principal met certificaat of Service-Principal met geheim
- **Lokale ontwikkeling**:
  - Gebruikers principal of Service-Principal met geheim

De bovenstaande verificaties scenario's worden ondersteund door de **Azure Identity client-bibliotheek** en geïntegreerd met Key Vault sdk's. Azure Identity Library kan worden gebruikt in verschillende omgevingen en platformen zonder uw code te wijzigen. Met Azure-identiteit wordt ook automatisch een verificatie token opgehaald van aangemeld bij Azure-gebruiker met Azure CLI, Visual Studio, Visual Studio code en anderen. 

Voor meer informatie over Azure Identity client libarary raadpleegt u:

**Azure Identity client-bibliotheken**

| .NET | Python | Java | Javascript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK java script](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> De [bibliotheek voor app-verificatie](/dotnet/api/overview/azure/service-to-service-authentication) die is aanbevolen voor Key Vault .NET SDK-versie 3, die momenteel depracated is. Volg de [AppAuthentication naar Azure. richt lijnen voor identiteits migratie](/dotnet/api/overview/azure/app-auth-migration) om naar Key Vault .NET SDK versie 4 te migreren.

Voor zelf studies over het verifiëren van Key Vault in toepassingen raadpleegt u:
- [Verifiëren bij Key Vault in de toepassing die in de virtuele machine wordt gehost in .NET](./tutorial-net-virtual-machine.md)
- [Verifiëren bij Key Vault in de toepassing die in de virtuele machine wordt gehost in python](./tutorial-python-virtual-machine.md)
- [Verifiëren bij Key Vault met App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Sleutels, certificaten en geheimen beheren

Toegang tot sleutels, geheimen en certificaten wordt bepaald door het gegevens vlak. Toegangs beheer voor gegevens vlak kan worden uitgevoerd met behulp van lokaal beleid voor toegang tot de kluis of Azure RBAC (preview).

**Sleutels API'S en Sdk's**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/key)<br>[Snelstartgids](../keys/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault/)<br>[Snelstartgids](../keys/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#key-operations)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Snelstartgids](../keys/quick-create-template.md)|[Verwijzing](/dotnet/api/azure.security.keyvault.keys)<br>[Snelstartgids](../keys/quick-create-net.md)|[Verwijzing](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Snelstartgids](../keys/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Snelstartgids](../keys/quick-create-java.md)|[Verwijzing](/javascript/api/@azure/keyvault-keys/)<br>[Snelstartgids](../keys/quick-create-node.md)|

**API'S en Sdk's van certificaten**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/certificate)<br>[Snelstartgids](../certificates/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault)<br>[Snelstartgids](../certificates/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#certificate-operations)|N.v.t.|[Verwijzing](/dotnet/api/azure.security.keyvault.certificates)<br>[Snelstartgids](../certificates/quick-create-net.md)|[Verwijzing](/python/api/overview/azure/keyvault-certificates-readme)<br>[Snelstartgids](../certificates/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Snelstartgids](../certificates/quick-create-java.md)|[Verwijzing](/javascript/api/@azure/keyvault-certificates/)<br>[Snelstartgids](../certificates/quick-create-node.md)|

**Geheimen en Sdk's van Secrets**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/secret)<br>[Snelstartgids](../secrets/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault/)<br>[Snelstartgids](../secrets/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#secret-operations)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Snelstartgids](../secrets/quick-create-template.md)|[Verwijzing](/dotnet/api/azure.security.keyvault.secrets)<br>[Snelstartgids](../secrets/quick-create-net.md)|[Verwijzing](/python/api/overview/azure/keyvault-secrets-readme)<br>[Snelstartgids](../secrets/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Snelstartgids](../secrets/quick-create-java.md)|[Verwijzing](/javascript/api/@azure/keyvault-secrets/)<br>[Snelstartgids](../secrets/quick-create-node.md)|

Zie [client bibliotheken](client-libraries.md) voor installatie pakketten en bron code.

Zie [Key Vault data-vlak en toegangs beleid](./secure-your-key-vault.md#data-plane-and-access-policies) en [Key Vault gegevens vlak en Azure RBAC (preview)](./secure-your-key-vault.md#data-plane-and-azure-rbac-preview) voor meer informatie over de beveiliging van Key Vault gegevens vlak.

### <a name="code-examples"></a>Codevoorbeelden

Zie voor meer voor beelden van het gebruik van Key Vault met uw toepassingen:

- Code voorbeelden [Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) code voor Azure Key Vault. 

## <a name="how-tos"></a>Procedures

De volgende artikelen en scenario's bevatten toepassingsspecifieke richt lijnen voor het werken met Azure Key Vault:

- Toegang [tot Key Vault achter de firewall](access-behind-firewall.md) : voor toegang tot een sleutel kluis moet de client toepassing voor de sleutel kluis toegang hebben tot meerdere eind punten voor verschillende functies.
- Certificaten implementeren op Vm's vanuit Key Vault- [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) : een Cloud toepassing die wordt uitgevoerd in een VM in azure, heeft een certificaat nodig. Hoe krijgt u dit certificaat vandaag nog aan deze VM?
- [Azure web app-certificaat implementeren via Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Wijs een toegangs beleid toe ([cli](assign-access-policy-cli.md)  |  [Power shell](assign-access-policy-powershell.md)  |  -[Portal](assign-access-policy-portal.md)). 
- Het [gebruik van Key Vault zacht verwijderen met CLI](./key-vault-recovery.md) begeleidt u bij het gebruik en de levens cyclus van een sleutel kluis en verschillende sleutel kluis objecten waarvoor het zacht verwijderen is ingeschakeld.
- [Veilige waarden (zoals wacht woorden) door geven tijdens de implementatie](../../azure-resource-manager/templates/key-vault-parameter.md) : wanneer u tijdens de implementatie een beveiligde waarde (zoals een wacht woord) moet door geven als een para meter, kunt u die waarde opslaan als een geheim in een Azure Key Vault en naar de waarde in andere Resource Manager-sjablonen verwijzen.

## <a name="integrated-with-key-vault"></a>Geïntegreerd met Key Vault

Deze artikelen zijn over andere scenario's en services die gebruikmaken van of worden geïntegreerd met Key Vault.

- [Versleuteling op rest](../../security/fundamentals/encryption-atrest.md) maakt het mogelijk om gegevens te coderen (versleuteling) wanneer deze persistent worden gemaakt. Gegevens versleutelings sleutels worden vaak versleuteld met een sleutel versleutelings sleutel in Azure Key Vault om de toegang verder te beperken.
- Met [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kunt u uw eigen Tenant sleutel beheren. In plaats van micro soft uw Tenant sleutel te laten beheren (standaard), kunt u bijvoorbeeld uw eigen Tenant sleutel beheren om te voldoen aan specifieke regels die van toepassing zijn op uw organisatie. Uw eigen tenantsleutel beheren wordt ook wel aangeduid als BYOK (Bring Your Own Key).
- Met [Azure Private Link service](private-link-service.md) kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Key Vault, Azure Storage en Azure Cosmos DB) en Azure hostende klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk.
- Key Vault integratie met [Event grid](../../event-grid/event-schema-key-vault.md)  kunnen gebruikers een melding ontvangen wanneer de status van een geheim dat is opgeslagen in de sleutel kluis is gewijzigd. U kunt een nieuwe versie van geheimen distribueren naar toepassingen of draaien in de buurt van verlopen geheimen om storingen te voor komen.
- U kunt uw [Azure Devops](/azure/devops/pipelines/release/azure-key-vault) -geheimen beveiligen tegen ongewenste toegang in Key Vault.
- [Geheim dat is opgeslagen in Key Vault in DataBricks gebruiken om verbinding te maken met Azure Storage](./integrate-databricks-blob-storage.md)
- De Azure Key Vault-provider configureren en uitvoeren voor het [stuur programma voor geheimen Store CSI](./key-vault-integrate-kubernetes.md) op Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Key Vault overzichten en concepten

- [Key Vault gedrag bij zacht verwijderen](soft-delete-overview.md) beschrijft een functie waarmee verwijderde objecten kunnen worden hersteld, of het verwijderen per ongeluk of opzettelijk is geslaagd.
- [Key Vault-client beperking](overview-throttling.md) gaat u naar de basis concepten van beperking en biedt een benadering voor uw app.
- [Key Vault Security-wereld](overview-security-worlds.md) beschrijft de relaties tussen regio's en beveiligings gebieden.

## <a name="social"></a>Sociaal netwerken

- [Key Vault blog](/archive/blogs/kv/)
- [Key Vault forum](https://aka.ms/kvforum)