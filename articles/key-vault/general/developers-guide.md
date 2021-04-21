---
title: Gids voor Azure Key Vault-ontwikkelaars
description: Ontwikkelaars kunnen Azure Key Vault gebruiken voor het beheren van cryptografische sleutels in Microsoft Azure omgeving.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 08ac1ae09741b63648aec2b51b6a774a46b9af7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818436"
---
# <a name="azure-key-vault-developers-guide"></a>Gids voor Azure Key Vault-ontwikkelaars

Key Vault kunt u veilig toegang krijgen tot gevoelige informatie vanuit uw toepassingen:

- Sleutels, geheimen en certificaten worden beveiligd zonder dat u de code zelf moet schrijven en u ze eenvoudig kunt gebruiken vanuit uw toepassingen.
- U kunt klanten de mogelijkheid bieden om hun eigen sleutels, geheimen en certificaten in bezit te hebben en te beheren, zodat u zich kunt concentreren op het leveren van de belangrijkste softwarefuncties. Op deze manier zijn uw toepassingen niet eigenaar van de verantwoordelijkheid of mogelijke aansprakelijkheid voor de tenantsleutels, geheimen en certificaten van uw klanten.
- Uw toepassing kan sleutels gebruiken voor ondertekening en versleuteling, maar houdt het sleutelbeheer extern van uw toepassing. Zie Over sleutels voor meer [informatie over sleutels](../keys/about-keys.md)
- U kunt referenties zoals wachtwoorden, toegangssleutels en SAS-tokens beheren door ze op te slaan in Key Vault geheimen. Zie [Over geheimen](../secrets/about-secrets.md)
- Certificaten beheren. Zie About [Certificates (Over certificaten) voor meer informatie](../certificates/about-certificates.md)

Zie Wat is Key Vault voor meer algemene [informatie over Azure Key Vault.](overview.md)

## <a name="public-previews"></a>Openbare previews

Periodiek brengen we een openbare preview uit van een nieuwe Key Vault functie. Probeer openbare preview-functies uit en laat ons weten wat u vindt via azurekeyvault@microsoft.com , ons e-mailadres voor feedback.

## <a name="creating-and-managing-key-vaults"></a>Sleutelkluizen maken en beheren

Key Vault beheer wordt, net als bij andere Azure-services, uitgevoerd via Azure Resource Manager service. Azure Resource Manager is de implementatie- en beheersservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-account kunt maken, bijwerken en verwijderen. Zie voor meer informatie [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

Toegang tot de beheerlaag wordt beheerd door [op rollen gebaseerd toegangsbeheer van Azure.](../../role-based-access-control/overview.md) In Key Vault, beheerlaag, ook wel bekend als beheer- of besturingsvlak, kunt u key vaults en de kenmerken ervan maken en beheren, inclusief toegangsbeleid, maar niet sleutels, geheimen en certificaten, die worden beheerd op het gegevensvlak. U kunt een vooraf gedefinieerde rol `Key Vault Contributor` gebruiken om beheertoegang te verlenen tot Key Vault.     

**API's en SDK's voor sleutelkluisbeheer:**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault)<br>[Snelstartgids](quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault)<br>[Snelstartgids](quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults)<br>[Snelstartgids](./vault-create-template.md)|[Verwijzing](/dotnet/api/microsoft.azure.management.keyvault)|[Verwijzing](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Verwijzing](/java/api/com.microsoft.azure.management.keyvault)|[Verwijzing](/javascript/api/@azure/arm-keyvault)|

Zie [Clientbibliotheken](client-libraries.md) voor installatiepakketten en broncode.

Zie Beveiligingsfuncties Key Vault meer informatie [Azure Key Vault het beheervlak](security-features.md)

## <a name="authenticate-to-key-vault-in-code"></a>Verifiëren voor Key Vault in code

Key Vault maakt gebruik van Azure AD-verificatie die azure AD-beveiligingsprincipaal vereist om toegang te verlenen. Een Azure AD-beveiligingsprincipaal kan een gebruiker, een toepassingsservice-principal, een beheerde identiteit voor [Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)of een groep van elk type beveiligings-principals zijn.

### <a name="authentication-best-practices"></a>Best practices voor verificatie

Het wordt aanbevolen om beheerde identiteit te gebruiken voor toepassingen die zijn geïmplementeerd in Azure. Als u Azure-services gebruikt die geen beheerde identiteit ondersteunen of als toepassingen on-premises zijn geïmplementeerd, is [een service-principal](../../active-directory/develop/howto-create-service-principal-portal.md) met een certificaat een mogelijk alternatief. In dat scenario moet het certificaat worden opgeslagen in Key Vault en vaak worden geroteerd. Service-principal met geheim kan worden gebruikt voor ontwikkel- en testomgevingen en lokaal of in Cloud Shell gebruik van user principal wordt aanbevolen.

Aanbevolen beveiligingsprincipa per omgeving:
- **Productieomgeving:**
  - Beheerde identiteit of service-principal met een certificaat
- **Test- en ontwikkelomgevingen:**
  - Beheerde identiteit, service-principal met certificaat of service-principal met geheim
- **Lokale ontwikkeling:**
  - Gebruikers-principal of service-principal met geheim

Bovenstaande verificatiescenario's worden ondersteund door **de Azure Identity-clientbibliotheek** en geïntegreerd met Key Vault SDK's. Azure Identity Library kan worden gebruikt in verschillende omgevingen en platforms zonder uw code te wijzigen. Azure Identity haalt ook automatisch een verificatie-token op van aangemeld bij een Azure-gebruiker met Azure CLI, Visual Studio, Visual Studio Code en andere. 

Zie voor meer informatie over de libarary van de Azure Identity-client:

**Azure Identity-clientbibliotheken**

| .NET | Python | Java | Javascript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [App-verificatiebibliotheek](/dotnet/api/overview/azure/service-to-service-authentication) die is aanbevolen Key Vault .NET SDK versie 3, die momenteel is afgeschaft. Volg [AppAuthentication to Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration) (Richtlijnen voor migratie van AppAuthentication naar Azure.Identity) om te migreren naar Key Vault .NET SDK versie 4.

Zie voor zelfstudies over het verifiëren van Key Vault in toepassingen:
- [Verifiëren om Key Vault in een toepassing die wordt gehost in VM in .NET](./tutorial-net-virtual-machine.md)
- [Verifiëren voor Key Vault in een toepassing die wordt gehost in VM in Python](./tutorial-python-virtual-machine.md)
- [Verifiëren om Key Vault te App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Sleutels, certificaten en geheimen beheren

De toegang tot sleutels, geheimen en certificaten wordt bepaald door het gegevensvlak. Toegangsbeheer voor de gegevensvlak kan worden uitgevoerd met behulp van toegangsbeleid voor lokale kluizen of Azure RBAC.

**Sleutels-API's en SDK's**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/key)<br>[Snelstartgids](../keys/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault/)<br>[Snelstartgids](../keys/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#key-operations)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Snelstartgids](../keys/quick-create-template.md)|[Verwijzing](/dotnet/api/azure.security.keyvault.keys)<br>[Snelstartgids](../keys/quick-create-net.md)|[Verwijzing](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Snelstartgids](../keys/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Snelstartgids](../keys/quick-create-java.md)|[Verwijzing](/javascript/api/@azure/keyvault-keys/)<br>[Snelstartgids](../keys/quick-create-node.md)|

**Certificates API's en SDK's**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/certificate)<br>[Snelstartgids](../certificates/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault)<br>[Snelstartgids](../certificates/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#certificate-operations)|N.v.t.|[Verwijzing](/dotnet/api/azure.security.keyvault.certificates)<br>[Snelstartgids](../certificates/quick-create-net.md)|[Verwijzing](/python/api/overview/azure/keyvault-certificates-readme)<br>[Snelstartgids](../certificates/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Snelstartgids](../certificates/quick-create-java.md)|[Verwijzing](/javascript/api/@azure/keyvault-certificates/)<br>[Snelstartgids](../certificates/quick-create-node.md)|

**Geheimen-API's en SDK's**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/secret)<br>[Snelstartgids](../secrets/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault/)<br>[Snelstartgids](../secrets/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#secret-operations)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Snelstartgids](../secrets/quick-create-template.md)|[Verwijzing](/dotnet/api/azure.security.keyvault.secrets)<br>[Snelstartgids](../secrets/quick-create-net.md)|[Verwijzing](/python/api/overview/azure/keyvault-secrets-readme)<br>[Snelstartgids](../secrets/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Snelstartgids](../secrets/quick-create-java.md)|[Verwijzing](/javascript/api/@azure/keyvault-secrets/)<br>[Snelstartgids](../secrets/quick-create-node.md)|

Zie [Clientbibliotheken](client-libraries.md) voor installatiepakketten en broncode.

Zie beveiligingsfuncties voor Key Vault meer informatie over de beveiliging [Azure Key Vault gegevensvlak.](security-features.md)

### <a name="code-examples"></a>Codevoorbeelden

Voor volledige voorbeelden van het Key Vault met uw toepassingen, zie:

- [Azure Key Vault codevoorbeelden](https://azure.microsoft.com/resources/samples/?service=key-vault) - Codevoorbeelden voor Azure Key Vault. 

## <a name="how-tos"></a>Procedures

De volgende artikelen en scenario's bieden taakspecifieke richtlijnen voor het werken met Azure Key Vault:

- [Toegang tot Key Vault firewall:](access-behind-firewall.md) voor toegang tot een sleutelkluis moet uw key vault-clienttoepassing toegang kunnen krijgen tot meerdere eindpunten voor verschillende functies.
- Certificaten implementeren op VM's vanuit [Key Vault- Windows,](../../virtual-machines/extensions/key-vault-windows.md) [Linux-](../../virtual-machines/extensions/key-vault-linux.md) Een cloudtoepassing die wordt uitgevoerd in een virtuele azure-VM heeft een certificaat nodig. Hoe krijgt u dit certificaat vandaag nog in deze VM?
- [Azure Web App Certificate implementeren via Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Een toegangsbeleid toewijzen ([CLI](assign-access-policy-cli.md)  |  [PowerShell-portal).](assign-access-policy-powershell.md)  |  [](assign-access-policy-portal.md) 
- [Als u een Key Vault cli gebruikt,](./key-vault-recovery.md) wordt u begeleid bij het gebruik en de levenscyclus van een sleutelkluis en verschillende sleutelkluisobjecten waarop de functie voor zacht verwijderen is ingeschakeld.
- Veilige waarden [(zoals wachtwoorden)](../../azure-resource-manager/templates/key-vault-parameter.md) doorgeven tijdens de implementatie: wanneer u tijdens de implementatie een beveiligde waarde (zoals een wachtwoord) als parameter moet doorgeven, kunt u die waarde als geheim opslaan in een Azure Key Vault en verwijzen naar de waarde in andere Resource Manager Resource Manager sjablonen.

## <a name="integrated-with-key-vault"></a>Geïntegreerd met Key Vault

Deze artikelen gaan over andere scenario's en services die gebruikmaken van of integreren met Key Vault.

- [Versleuteling-at-rest](../../security/fundamentals/encryption-atrest.md) maakt het mogelijk om gegevens te coderen (versleutelen) wanneer deze persistent zijn. Gegevensversleutelingssleutels worden vaak versleuteld met een sleutelversleutelingssleutel in Azure Key Vault toegang verder te beperken.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kunt u uw eigen tenantsleutels manageren. In plaats van dat Microsoft bijvoorbeeld uw tenantsleutel beheert (de standaardinstelling), kunt u uw eigen tenantsleutel beheren om te voldoen aan specifieke regelgeving die van toepassing is op uw organisatie. Uw eigen tenantsleutel beheren wordt ook wel aangeduid als BYOK (Bring Your Own Key).
- [met Azure Private Link Service](private-link-service.md) hebt u toegang tot Azure Services (bijvoorbeeld Azure Key Vault, Azure Storage en Azure Cosmos DB) en in Azure gehoste klant-/partnerservices via een privé-eindpunt in uw virtuele netwerk.
- Key Vault integratie met [Event Grid](../../event-grid/event-schema-key-vault.md)  kunnen gebruikers een melding ontvangen wanneer de status van een geheim dat is opgeslagen in de sleutelkluis is gewijzigd. U kunt nieuwe versie van geheimen distribueren naar toepassingen of geheimen die bijna verlopen draaien om uitval te voorkomen.
- U kunt uw [Azure Devops-geheimen](/azure/devops/pipelines/release/azure-key-vault) beschermen tegen ongewenste toegang in Key Vault.
- [Gebruik geheim dat is opgeslagen in Key Vault databricks om verbinding te maken met Azure Storage](./integrate-databricks-blob-storage.md)
- Configureer en voer de Azure Key Vault voor het [stuurprogramma Secrets Store CSI op](./key-vault-integrate-kubernetes.md) Kubernetes uit

## <a name="key-vault-overviews-and-concepts"></a>Key Vault en concepten

- [Key Vault gedrag voor het](soft-delete-overview.md) verwijderen van gegevens wordt een functie beschreven waarmee verwijderde objecten kunnen worden hersteld, ongeacht of het verwijderen per ongeluk of opzettelijk is gebeurd.
- [Key Vault clientbeperking](overview-throttling.md) wordt u op de basisconcepten van beperking georiënteerd en biedt een benadering voor uw app.
- [Key Vault beveiligingswerelden worden](overview-security-worlds.md) de relaties tussen regio's en beveiligingsgebieden beschreven.

## <a name="social"></a>Sociaal netwerken

- [Key Vault Blog](/archive/blogs/kv/)
- [Key Vault Forum](https://aka.ms/kvforum)