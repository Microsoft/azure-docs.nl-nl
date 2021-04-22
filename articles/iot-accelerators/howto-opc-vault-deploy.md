---
title: De OPC Vault-certificaatbeheerservice implementeren - Azure | Microsoft Docs
description: De OPC Vault-certificaatbeheerservice vanaf het begin implementeren.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b20f6318c2e6be701446e29ab93598752e93d287
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870279"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>De OPC Vault-certificaatbeheerservice bouwen en implementeren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In dit artikel wordt uitgelegd hoe u de OPC Vault-certificaatbeheerservice in Azure implementeert.

> [!NOTE]
> Zie de GitHub [OPC Vault-opslagplaats](https://github.com/Azure/azure-iiot-opc-vault-service)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

### <a name="install-required-software"></a>Vereiste software installeren

Momenteel is de build- en implementatiebewerking beperkt tot Windows.
De voorbeelden zijn allemaal geschreven voor C# .NET Standard, die u nodig hebt om de service en voorbeelden voor implementatie te bouwen.
Alle hulpprogramma's die u nodig hebt voor .NET Standard worden geleverd met de .NET Core-hulpprogramma's. Zie [Aan de slag met .NET Core.](/dotnet/articles/core/getting-started)

1. [Installeer .NET Core 2.1+][dotnet-install].
2. [Installeer Docker][docker-url] (optioneel, alleen als de lokale Docker-build vereist is).
4. Installeer de [Azure-opdrachtregelprogramma's voor PowerShell.][powershell-install]
5. Meld u aan voor een [Azure-abonnement.][azure-free]

### <a name="clone-the-repository"></a>De opslagplaats klonen

Als u dit nog niet hebt gedaan, kloont u deze GitHub-opslagplaats. Open een opdrachtprompt of terminal en voer het volgende uit:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

U kunt de repo ook rechtstreeks klonen in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>De Azure-service bouwen en implementeren in Windows

Een PowerShell-script biedt een eenvoudige manier om de OPC Vault-microservice en de toepassing te implementeren.

1. Open een PowerShell-venster in de hoofdmap van de repo. 
3. Ga naar de map `cd deploy` implementeren.
3. Kies een naam die waarschijnlijk geen conflict veroorzaakt met `myResourceGroup` andere geïmplementeerde webpagina's. Zie de sectie 'Websitenaam al in gebruik' verder in dit artikel.
5. Start de implementatie met `.\deploy.ps1` voor interactieve installatie of voer een volledige opdrachtregel in:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Als u van plan bent om met deze implementatie te ontwikkelen, voegt u toe om de Swagger-gebruikersinterface in teschakelen en om builds voor foutopsporing `-development 1` te implementeren.
6. Volg de instructies in het script om u aan te melden bij uw abonnement en aanvullende informatie op te geven.
9. Na een geslaagde build en implementatie ziet u het volgende bericht:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Zie de sectie 'Problemen met implementatiefouten oplossen' verderop in het artikel in het geval van problemen.

8. Open uw favoriete browser en open de toepassingspagina: `https://myResourceGroup.azurewebsites.net`
8. Geef de web-app en de OPC Vault-microservice een paar minuten de tijd om op te warmen na de implementatie. De startpagina van het web reageert mogelijk pas na het eerste gebruik, tot een minuut lang, totdat u de eerste antwoorden krijgt.
11. Als u de Swagger-API wilt bekijken, opent u: `https://myResourceGroup-service.azurewebsites.net`
13. Als u een lokale GDS-server wilt starten met dotnet, start u `.\myResourceGroup-gds.cmd` . Start met `.\myResourceGroup-dockergds.cmd` Docker.

Het is mogelijk om een build opnieuw te gebruiken met exact dezelfde instellingen. Let erop dat met een dergelijke bewerking alle toepassingsgeheimen worden vernieuwd en dat sommige instellingen in de registraties van de toepassing Azure Active Directory (Azure AD) opnieuw kunnen worden ingesteld.

Het is ook mogelijk om alleen de binaire bestanden van de web-app opnieuw te installeren. Met de parameter `-onlyBuild 1` worden nieuwe ZIP-pakketten van de service en de app geïmplementeerd in de webtoepassingen.

Nadat de implementatie is geslaagd, kunt u de services gaan gebruiken. Zie [De OPC Vault-certificaatbeheerservice beheren.](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>De services uit het abonnement verwijderen

Dit doet u als volgt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar de resourcegroep waarin de service is geïmplementeerd.
3. Selecteer **Resourcegroep verwijderen** en bevestig dit.
4. Na enige tijd worden alle geïmplementeerde serviceonderdelen verwijderd.
5. Ga naar **Azure Active Directory** > **App-registraties**.
6. Er moeten drie registraties worden vermeld voor elke geïmplementeerde resourcegroep. De registraties hebben de volgende namen: `resourcegroup-client` `resourcegroup-module` , , `resourcegroup-service` . Verwijder elke registratie afzonderlijk.

Nu worden alle geïmplementeerde onderdelen verwijderd.

## <a name="troubleshooting-deployment-failures"></a>Implementatiefouten oplossen

### <a name="resource-group-name"></a>Naam van de resourcegroep

Gebruik een korte en eenvoudige naam voor de resourcegroep. De naam wordt ook gebruikt om resources een naam te geven en het voorvoegsel van de service-URL. Het moet dus voldoen aan de naamgevingsvereisten voor resources.  

### <a name="website-name-already-in-use"></a>Websitenaam die al in gebruik is

Het is mogelijk dat de naam van de website al in gebruik is. U moet een andere resourcegroepnaam gebruiken. De hostnamen die worden gebruikt door het implementatiescript zijn: https: \/ /resourcegroupname.azurewebsites.net en https: \/ /resourgroupname-service.azurewebsites.net.
Andere namen van services worden gebouwd door de combinatie van korte naamhashes en zullen waarschijnlijk niet conflicteren met andere services.

### <a name="azure-ad-registration"></a>Azure AD-registratie 

Het implementatiescript probeert drie Azure AD-toepassingen te registreren in Azure AD. Afhankelijk van uw machtigingen in de geselecteerde Azure AD-tenant, kan deze bewerking mislukken. Er zijn twee opties:

- Als u een Azure AD-tenant hebt gekozen uit een lijst met tenants, start u het script opnieuw op en kiest u een andere tenant in de lijst.
- U kunt ook een persoonlijke Azure AD-tenant implementeren in een ander abonnement. Start het script opnieuw op en selecteer om het te gebruiken.

## <a name="deployment-script-options"></a>Opties voor implementatiescripts

Het script heeft de volgende parameters:


```
-resourceGroupName
```

Dit kan de naam zijn van een bestaande of een nieuwe resourcegroep.

```
-subscriptionId
```


Dit is de abonnements-id waar resources worden geïmplementeerd. Dit is niet verplicht.

```
-subscriptionName
```


U kunt ook de naam van het abonnement gebruiken.

```
-resourceGroupLocation
```


Dit is de locatie van een resourcegroep. Als deze parameter is opgegeven, wordt geprobeerd een nieuwe resourcegroep op deze locatie te maken. Deze parameter is ook optioneel.


```
-tenantId
```


Dit is de Azure AD-tenant die moet worden gebruikt. 

```
-development 0|1
```

Dit is om te implementeren voor ontwikkeling. Gebruik de build voor foutopsporing en stel de ASP.NET in op Ontwikkeling. Maak `.publishsettings` voor importeren in Visual Studio 2017, zodat de app en de service rechtstreeks kunnen worden geïmplementeerd. Deze parameter is ook optioneel.

```
-onlyBuild 0|1
```

Dit is om alleen de web-apps opnieuw te bouwen en te herbouwen en om de Docker-containers opnieuw te bouwen. Deze parameter is ook optioneel.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://dotnet.microsoft.com/download

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Vault kunt implementeren, kunt u het volgende doen:

> [!div class="nextstepaction"]
> [OPC Vault beheren](howto-opc-vault-manage.md)