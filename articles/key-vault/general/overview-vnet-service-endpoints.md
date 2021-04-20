---
title: Service-eindpunten voor virtuele netwerken voor Azure Key Vault
description: Meer informatie over hoe service-eindpunten voor virtuele netwerken Azure Key Vault u de toegang tot een opgegeven virtueel netwerk kunt beperken, inclusief gebruiksscenario's.
services: key-vault
author: amitbapat
ms.author: ambapat
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 985cbe1128d1dd64fda86ef062750dc5dd068ffe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751729"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Service-eindpunten voor virtuele netwerken voor Azure Key Vault

Met de service-eindpunten voor virtuele Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eindpunten kunt u ook de toegang tot een lijst met IPv4-adresbereiken (internetprotocol versie 4) beperken. Elke gebruiker die verbinding maakt met uw sleutelkluis van buiten deze bronnen, wordt de toegang geweigerd.

Er is één belangrijke uitzondering op deze beperking. Als een gebruiker zich heeft ervoor gekozen om vertrouwde verbindingen Microsoft-services, worden verbindingen van deze services via de firewall toegestaan. Deze services omvatten bijvoorbeeld Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager en Azure Backup. Dergelijke gebruikers moeten nog steeds een geldig Azure Active Directory-token presenteren en moeten machtigingen hebben (geconfigureerd als toegangsbeleid) om de aangevraagde bewerking uit te voeren. Zie Service-eindpunten voor [virtuele netwerken voor meer informatie.](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="usage-scenarios"></a>Gebruiksscenario's

U kunt standaard [Key Vault en virtuele](network-security.md) netwerken configureren om toegang tot verkeer van alle netwerken (inclusief internetverkeer) te weigeren. U kunt toegang verlenen tot verkeer van specifieke virtuele Azure-netwerken en ip-adresbereiken van openbare internet, zodat u een beveiligde netwerkgrens voor uw toepassingen kunt bouwen.

> [!NOTE]
> Key Vault firewalls en regels voor virtuele netwerken zijn alleen van toepassing op [het gegevensvlak](security-overview.md#privileged-access) van Key Vault. Key Vault bewerkingen op het besturingsvlak (zoals bewerkingen maken, verwijderen en wijzigen, toegangsbeleid instellen, firewalls instellen en regels voor virtuele netwerken en de implementatie van geheimen of sleutels via ARM-sjablonen) worden niet beïnvloed door firewalls en regels voor virtuele netwerken.

Hier zijn enkele voorbeelden van hoe u service-eindpunten kunt gebruiken:

* U gebruikt Key Vault om versleutelingssleutels, toepassingsgeheimen en certificaten op te slaan en u wilt de toegang tot uw sleutelkluis blokkeren via het openbare internet.
* U wilt de toegang tot uw sleutelkluis vergrendelen, zodat alleen uw toepassing, of een korte lijst met aangewezen hosts, verbinding kan maken met uw sleutelkluis.
* U hebt een toepassing die wordt uitgevoerd in uw virtuele Azure-netwerk en dit virtuele netwerk is vergrendeld voor al het inkomende en uitgaande verkeer. Uw toepassing moet nog steeds verbinding maken met Key Vault om geheimen of certificaten op te halen of cryptografische sleutels te gebruiken.

## <a name="trusted-services"></a>Vertrouwde services

Hier is een lijst met vertrouwde services die toegang hebben tot een sleutelkluis als de optie Vertrouwde **services** toestaan is ingeschakeld.

|Vertrouwde service|Ondersteunde gebruiksscenario's|
| --- | --- |
|Azure Virtual Machines-implementatieservice|[Implementeer certificaten naar VM's vanuit door de klant beheerde Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Azure Resource Manager-implementatieservice voor sjablonen|[Geef beveiligde waarden door tijdens de implementatie.](../../azure-resource-manager/templates/key-vault-parameter.md)|
|Azure Disk Encryption-service voor volumeversleuteling|Toegang tot BitLocker-sleutel (Windows-VM) of DM-wachtwoordzin (Linux-VM) en sleutelversleutelingssleutel toestaan tijdens de implementatie van de virtuele machine. Hiermee schakelt [u Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)in.|
|Azure Backup|Back-up en herstel van relevante sleutels en geheimen toestaan tijdens azure Virtual Machines back-up, met behulp [van Azure Backup](../../backup/backup-overview.md).|
|Exchange Online & SharePoint Online|Toegang tot de klantsleutel toestaan voor Azure Storage Service Encryption met [klantsleutel](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Toegang tot de tenantsleutel voor [Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Implementeer een Azure Web App-certificaat via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption met Bring Your Own Key ondersteuning voor Azure SQL Database en Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Azure Storage|[Storage Service Encryption door de klant beheerde sleutels in Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Versleuteling van gegevens in Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) met een door de klant beheerde sleutel.|
|Azure Synapse Analytics|[Versleuteling van gegevens met behulp van door de klant beheerde sleutels in Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Snelle, eenvoudige en op samenwerking Apache Spark gebaseerde analyseservice](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Certificaten implementeren voor Custom Domain van Key Vault met behulp van MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Gegevensopslagreferenties ophalen uit Key Vault Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Toegang tot een sleutelkluis toestaan voor een scenario met door de klant beheerde sleutels](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Toegang tot een sleutelkluis toestaan voor een scenario met door de klant beheerde sleutels](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Door de klant beheerde sleutels in Azure Key Vault voor de Import/Export-service](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Registerversleuteling met door de klant beheerde sleutels](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[Met Key Vault certificaten voor listeners met HTTPS-functie](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> U moet het relevante toegangsbeleid voor Key Vault instellen om de bijbehorende services toegang te geven tot Key Vault.

## <a name="next-steps"></a>Volgende stappen

- Zie Configure Azure Key Vault [firewalls and virtual networks (Firewalls](network-security.md) en virtuele netwerken configureren) voor stapsgewijse instructies
- zie het [Azure Key Vault beveiligingsoverzicht](security-overview.md)
