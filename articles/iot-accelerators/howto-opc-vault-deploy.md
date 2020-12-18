---
title: De OPC kluis Certificate Management-service implementeren-Azure | Microsoft Docs
description: Hoe u de OPC kluis Certificate Management-service helemaal volledig implementeert.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 98dc9345d2c8b392fd094458b612857d6d454739
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071486"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>De OPC kluis Certificate Management-service bouwen en implementeren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In dit artikel wordt uitgelegd hoe u de OPC kluis Certificate Management-service in azure implementeert.

> [!NOTE]
> Zie de GitHub [OPC kluis-opslag plaats](https://github.com/Azure/azure-iiot-opc-vault-service)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

### <a name="install-required-software"></a>Vereiste software installeren

De bewerking voor het maken en implementeren van het pakket is momenteel beperkt tot Windows.
De voor beelden zijn allemaal geschreven voor C# .NET Standard, die u nodig hebt om de service en voor beelden te maken voor implementatie.
Alle hulpprogram ma's die u nodig hebt voor .NET Standard, worden geleverd met de .NET core-hulpprogram ma's. Zie [aan de slag met .net core](/dotnet/articles/core/getting-started).

1. [Installeer .net Core 2.1 +][dotnet-install].
2. [Installeer docker][docker-url] (optioneel, alleen als de lokale docker-build is vereist).
4. Installeer de [Azure-opdracht regel Programma's voor Power shell][powershell-install].
5. Meld u aan voor een [Azure-abonnement][azure-free].

### <a name="clone-the-repository"></a>De opslagplaats klonen

Als u dit nog niet hebt gedaan, moet u deze GitHub-opslag plaats klonen. Open een opdracht prompt of Terminal en voer de volgende handelingen uit:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

U kunt de opslag plaats ook rechtstreeks klonen in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>De Azure-service in Windows bouwen en implementeren

Een Power shell-script biedt een eenvoudige manier om de OPC-kluis micro service en de toepassing te implementeren.

1. Open een Power shell-venster in de hoofdmap opslag plaats. 
3. Ga naar de map Deploy `cd deploy` .
3. Kies een naam `myResourceGroup` die waarschijnlijk geen conflict veroorzaakt met andere geïmplementeerde webpagina's. Zie de sectie "website naam al in gebruik" verderop in dit artikel.
5. Start de implementatie met `.\deploy.ps1` voor interactieve installatie of voer een volledige opdracht regel in:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Als u van plan bent om te ontwikkelen met deze implementatie, voegt u toe `-development 1` om de Swagger-gebruikers interface in te scha kelen en om debug-builds te implementeren.
6. Volg de instructies in het script om u aan te melden bij uw abonnement en om aanvullende informatie te geven.
9. Nadat de bewerking is voltooid, wordt het volgende bericht weer gegeven:
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
   > In het geval van problemen raadpleegt u de sectie ' problemen met implementatie fouten oplossen ' verderop in dit artikel.

8. Open uw favoriete browser en open de toepassings pagina: `https://myResourceGroup.azurewebsites.net`
8. Geef de web-app en de OPC-kluis micro service een paar minuten om na de implementatie te warmten. De start pagina van het web reageert mogelijk niet meer bij het eerste gebruik, Maxi maal een minuut, totdat u de eerste reacties krijgt.
11. Als u de Swagger API wilt bekijken, opent u: `https://myResourceGroup-service.azurewebsites.net`
13. Start om een lokale GDS-server te starten met DotNet `.\myResourceGroup-gds.cmd` . Start met docker `.\myResourceGroup-dockergds.cmd` .

Het is mogelijk om een build opnieuw te implementeren met precies dezelfde instellingen. Houd er rekening mee dat een dergelijke bewerking alle toepassings geheimen vernieuwt en mogelijk sommige instellingen in de toepassings registraties van de Azure Active Directory (Azure AD) opnieuw kan instellen.

Het is ook mogelijk alleen de binaire bestanden van de web-app te implementeren. Met de para meter `-onlyBuild 1` worden nieuwe ZIP-pakketten van de service en de app geïmplementeerd naar de webtoepassingen.

Nadat de implementatie is voltooid, kunt u beginnen met het gebruik van de services. Zie [de OPC-kluis certificaat beheer service beheren](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>De services verwijderen uit het abonnement

Dit doet u als volgt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar de resource groep waarin de service is geïmplementeerd.
3. Selecteer **Resourcegroep verwijderen** en bevestig dit.
4. Na enkele ogen blikken worden alle geïmplementeerde service onderdelen verwijderd.
5. Ga naar **Azure Active Directory** > **App-registraties**.
6. Er moeten drie registraties worden vermeld voor elke geïmplementeerde resource groep. De registraties hebben de volgende namen: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Verwijder elke registratie afzonderlijk.

Nu worden alle geïmplementeerde onderdelen verwijderd.

## <a name="troubleshooting-deployment-failures"></a>Implementatie fouten oplossen

### <a name="resource-group-name"></a>Naam van de resourcegroep

Gebruik een korte en eenvoudige naam voor de resource groep. De naam wordt ook gebruikt voor het benoemen van resources en het voor voegsel van de service-URL. Daarom moet het voldoen aan de vereisten voor de naamgeving van resources.  

### <a name="website-name-already-in-use"></a>De naam van de website wordt al gebruikt

Het is mogelijk dat de naam van de website al in gebruik is. U moet een andere naam voor de resource groep gebruiken. De hostnamen die worden gebruikt door het implementatie script zijn: https://resourcegroupname.azurewebsites.net en https://resourgroupname-service.azurewebsites.net.
Andere namen van services worden gebouwd op basis van de combi natie van korte naam-hashes en zijn waarschijnlijk niet strijdig met andere services.

### <a name="azure-ad-registration"></a>Azure AD-registratie 

Het implementatie script probeert drie Azure AD-toepassingen in azure AD te registreren. Afhankelijk van uw machtigingen in de geselecteerde Azure AD-Tenant kan deze bewerking mislukken. Er zijn twee opties:

- Als u een Azure AD-Tenant uit een lijst met tenants hebt gekozen, start u het script opnieuw en kiest u een ander in de lijst.
- U kunt ook een persoonlijke Azure AD-Tenant implementeren in een ander abonnement. Start het script opnieuw en selecteer om het te gebruiken.

## <a name="deployment-script-options"></a>Opties voor implementatie script

Het script heeft de volgende para meters:


```
-resourceGroupName
```

Dit kan de naam zijn van een bestaande of een nieuwe resource groep.

```
-subscriptionId
```


Dit is de abonnements-ID waar resources worden geïmplementeerd. Dit is niet verplicht.

```
-subscriptionName
```


U kunt ook de naam van het abonnement gebruiken.

```
-resourceGroupLocation
```


Dit is een locatie voor de resource groep. Als deze para meter wordt opgegeven, wordt geprobeerd een nieuwe resource groep te maken op deze locatie. Deze para meter is ook optioneel.


```
-tenantId
```


Dit is de Azure AD-Tenant die moet worden gebruikt. 

```
-development 0|1
```

Dit is om te implementeren voor ontwikkeling. Gebruik Build debug en stel de ASP.NET-omgeving in op Development. Maken `.publishsettings` voor import in Visual Studio 2017, zodat de app en de service rechtstreeks kunnen worden geïmplementeerd. Deze para meter is ook optioneel.

```
-onlyBuild 0|1
```

Dit is het opnieuw samen stellen en opnieuw implementeren van alleen de web-apps en het opnieuw samen stellen van de docker-containers. Deze para meter is ook optioneel.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een nieuwe OPC-kluis kunt implementeren, hebt u de volgende opties:

> [!div class="nextstepaction"]
> [OPC-kluis beheren](howto-opc-vault-manage.md)