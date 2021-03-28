---
title: Service-eindpunten voor virtuele netwerken voor Azure Key Vault
description: Meer informatie over hoe u met virtuele netwerk service-eind punten voor Azure Key Vault de toegang tot een opgegeven virtueel netwerk kunt beperken, met inbegrip van gebruiks scenario's.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: ae22f07a70f3317b62776e5024b7a3d1084516a1
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643491"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Service-eindpunten voor virtuele netwerken voor Azure Key Vault

Met de service-eind punten voor virtuele netwerken voor Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eind punten kunt u ook de toegang beperken tot een lijst met IPv4-adresbereiken (Internet Protocol versie 4). Gebruikers die verbinding maken met uw sleutel kluis van buiten deze bronnen, krijgen geen toegang.

Er is een belang rijke uitzonde ring op deze beperking. Als een gebruiker is aangemeld om vertrouwde micro soft-Services toe te staan, kunnen verbindingen van deze services via de firewall worden toegestaan. Deze services omvatten bijvoorbeeld Office 365 Exchange Online, Office 365 share point online, Azure compute, Azure Resource Manager en Azure Backup. Dergelijke gebruikers moeten nog steeds een geldig Azure Active Directory token presen teren en moeten machtigingen hebben (geconfigureerd als toegangs beleid) om de aangevraagde bewerking uit te voeren. Zie [service-eind punten voor virtuele netwerken](../../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.

## <a name="usage-scenarios"></a>Gebruiksscenario's

U kunt [Key Vault firewalls en virtuele netwerken](network-security.md) configureren om de toegang tot verkeer van alle netwerken (met inbegrip van Internet verkeer) standaard te weigeren. U kunt toegang verlenen aan verkeer van specifieke virtuele Azure-netwerken en open bare IP-adresbereiken voor het Internet, zodat u een beveiligde netwerk grens voor uw toepassingen maakt.

> [!NOTE]
> Key Vault firewalls en regels voor virtuele netwerken zijn alleen van toepassing op het [gegevens vlak](secure-your-key-vault.md#data-plane-access-control) van Key Vault. Key Vault besturings vlak bewerkingen (zoals maken, verwijderen en wijzigen van bewerkingen, het instellen van toegangs beleid, het instellen van firewalls en regels voor virtuele netwerken en de implementatie van geheimen of sleutels via ARM-sjablonen) worden niet beïnvloed door firewalls en regels voor virtuele netwerken.

Hier volgen enkele voor beelden van hoe u service-eind punten kunt gebruiken:

* U gebruikt Key Vault om versleutelings sleutels, toepassings geheimen en certificaten op te slaan, en u wilt de toegang tot uw sleutel kluis blok keren via het open bare Internet.
* U de toegang tot uw sleutel kluis wilt vergren delen zodat alleen uw toepassing of een korte lijst met aangewezen hosts verbinding kan maken met uw sleutel kluis.
* U hebt een toepassing die wordt uitgevoerd in uw virtuele Azure-netwerk en dit virtuele netwerk is vergrendeld voor al het binnenkomende en uitgaande verkeer. Uw toepassing moet nog steeds verbinding maken met Key Vault om geheimen of certificaten op te halen, of om cryptografische sleutels te gebruiken.

## <a name="trusted-services"></a>Vertrouwde services

Hier volgt een lijst met vertrouwde services die toegang mogen hebben tot een sleutel kluis als de optie **vertrouwde services toestaan** is ingeschakeld.

|Vertrouwde service|Ondersteunde gebruiks scenario's|
| --- | --- |
|Azure Virtual Machines Deployment-service|[Implementeer certificaten op vm's vanuit door de klant beheerde Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Implementatie service voor Azure Resource Manager-sjabloon|[Beveiligde waarden door geven tijdens de implementatie](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Volume Encryption-service Azure Disk Encryption|Toegang tot de BitLocker-sleutel (Windows-VM) of DM-wachtwoordzin (Linux-VM) en sleutel versleutelings sleutel toestaan tijdens de implementatie van de virtuele machine. Hiermee maakt u [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)mogelijk.|
|Azure Backup|Het maken van back-ups en het herstellen van relevante sleutels en geheimen tijdens Azure Virtual Machines backup toestaan met behulp van [Azure backup](../../backup/backup-overview.md).|
|Exchange Online & share point online|Toegang tot de klant sleutel toestaan voor de code ring van Azure Storage-service met de code van de [klant](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Toegang tot de Tenant sleutel voor [Azure Information Protection toestaan.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Implementeer het Azure web app-certificaat via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption met Bring your own Key ondersteuning voor Azure SQL database en Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Azure Storage|[Storage service Encryption door de klant beheerde sleutels gebruiken in azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Versleuteling van gegevens in azure data Lake Store](../../data-lake-store/data-lake-store-encryption.md) met een door de klant beheerde sleutel.|
|Azure Synapse Analytics|[Versleuteling van gegevens met door de klant beheerde sleutels in Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Snelle, eenvoudige en samen werkende, Apache Spark-gebaseerde analyse service](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Certificaten voor een aangepast domein van Key Vault implementeren met behulp van MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Referenties voor gegevens opslag ophalen in Key Vault van Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Toegang verlenen tot een sleutel kluis voor het scenario door de klant beheerde sleutels](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Toegang verlenen tot een sleutel kluis voor het scenario door de klant beheerde sleutels](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Door de klant beheerde sleutels gebruiken in Azure Key Vault voor de import/export-service](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Register versleuteling met door de klant beheerde sleutels](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[Key Vault-certificaten gebruiken voor listeners die HTTPS ondersteunen](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> U moet de relevante Key Vault toegangs beleid instellen zodat de bijbehorende services toegang krijgen tot Key Vault.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Key Vault firewalls en virtuele netwerken configureren](network-security.md) voor stapsgewijze instructies.
- Zie het [overzicht van Azure Key Vault beveiliging](security-overview.md)
