---
title: Azure Resource Manager-voorbeeldsjablonen
description: Gebruik Azure Resource Manager-sjabloonvoorbeelden voor een aantal algemene App Service-scenario's. Ontdek hoe u uw implementatie- of beheertaken voor App Service kunt automatiseren.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 08/26/2020
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 5129fccceb633991767cdd3b52bbb5b6af067270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891042"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Azure Resource Manager-sjablonen voor App Service

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor Azure App Service. Zie [Hulp bij het implementeren van apps met Azure Resource Manager-sjablonen](deploy-resource-manager-template.md) voor aanbevelingen om veelvoorkomende fouten bij het maken van app-sjablonen te voorkomen.

Zie [Microsoft.Web resource types](/azure/templates/microsoft.web/allversions) (Microsoft.Web-resourcetypen) voor informatie over de JSON-syntaxis en eigenschappen.

| Een app implementeren | Beschrijving |
|-|-|
| [Een App Service-plan en een eenvoudige Linux-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Hiermee wordt een App Service-app geïmplementeerd die is geconfigureerd voor Linux. |
| [Een App Service-plan en een eenvoudige Windows-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Hiermee wordt een App Service-app geïmplementeerd die is geconfigureerd voor Windows. |
| [Een app die is gekoppeld aan een GitHub-opslagplaats](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Hiermee wordt een App Service-app geïmplementeerd die code uit GitHub haalt. |
| [App met aangepaste implementatiesites](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Hiermee wordt een App Service-app met aangepaste implementatiesites/-omgevingen geïmplementeerd. |
| [App met privé-eindpunt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-webapp)| Hiermee implementeert u een App Service-app met een privé-eindpunt. |
|**App configureren**| **Beschrijving** |
| [App-certificaat uit Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Hiermee wordt een App Service-app-certificaat uit het Azure Key Vault-geheim geïmplementeerd en gebruikt voor TLS/SSL-binding. |
| [App met aangepast domein en SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Hiermee wordt een App Service-app met een aangepaste hostnaam geïmplementeerd en wordt een app-certificaat uit Key Vault opgehaald voor TLS/SSL-binding. |
| [App met een GoLang-extensie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Hiermee wordt een App Service-app met de Golang-site-extensie geïmplementeerd. Daarna kunt u webtoepassingen uitvoeren die zijn ontwikkeld in Golang in Azure. |
| [App met Java 8 en Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Hiermee wordt een App Service-app geïmplementeerd waarvoor Java 8 en Tomcat 8 zijn ingeschakeld. Daarna kunt u Java-toepassingen uitvoeren in Azure. |
| [App met regionale VNet-integratie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| Hiermee wordt een App Service-app geïmplementeerd waarvoor regionale VNet-integratie is ingeschakeld. |
|**Een app beveiligen**| **Beschrijving** |
| [App geïntegreerd met Azure Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Implementeert een App Service-app en een Application Gateway en isoleert het verkeer met behulp van service-eindpunt en toegangsbeperkingen. |
|**App met verbonden resources**| **Beschrijving** |
| [App in Linux met MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Hiermee wordt een App Service-app geïmplementeerd in Linux met Azure Database for MySQL. |
| [App in Linux met PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Hiermee wordt een App Service-app geïmplementeerd in Linux met Azure Database for PostgreSQL. |
|**App met verbonden resources**| **Beschrijving** |
| [App met MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Hiermee wordt een App Service-app geïmplementeerd in Windows met Azure Database for MySQL. |
| [App met PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Hiermee wordt een App Service-app geïmplementeerd in Windows met Azure Database for PostgreSQL. |
| [App met een database in Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Hiermee worden een App Service-app en database in Azure SQL Database geïmplementeerd op serviceniveau Basic. |
| [App met een Blobopslag-verbinding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Hiermee wordt een App Service-app geïmplementeerd met een verbindingsreeks voor Azure Blobopslag. Daarna kunt u Blobopslag gebruiken vanuit de app. |
| [App met Azure Cache voor Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Hiermee wordt een App Service-app met een Azure Cache voor Redis geïmplementeerd. |
| [App verbonden met een back-end-web-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection)| Hiermee worden twee web-apps geïmplementeerd (front-end en back-end) die veilig zijn verbonden met VNet-injectie en het privé-eindpunt. |
|**App Service-omgeving**| **Beschrijving** |
| [Create an App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create)(Een App Service-omgeving v2 maken) | Hiermee wordt een App Service-omgeving v2 in uw virtuele netwerk gemaakt. |
| [Create an App Service environment v2 with an ILB Address](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) (Een App Service-omgeving v2 met een ILB-adres (interne load balancer) maken) | Hiermee wordt een App Service-omgeving v2 in uw virtuele netwerk gemaakt met een privéadres voor de interne load balancer. |
| [Configure the default SSL certificate for an ILB App Service environment or an ILB App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) (Het standaard-SSL-certificaat configureren voor een ILB App Service-omgeving of een ILB App Service-omgeving v2) | Hiermee wordt het standaard-TLS/SSL-certificaat geconfigureerd voor een ILB App Service-omgeving of een ILB App Service-omgeving v2. |
| | |
