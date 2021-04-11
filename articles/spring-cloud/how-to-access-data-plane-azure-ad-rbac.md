---
title: Toegang tot configuratie server en service register
titleSuffix: Azure Spring Cloud
description: Toegang krijgen tot de register eindpunten van de configuratie server en service met Azure Active Directory toegangs beheer op basis van rollen.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 23e24e562ea6fa10eee82c54c9ab2a701dd10351
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169948"
---
# <a name="access-config-server-and-service-registry"></a>Toegang tot configuratie server en service register

In dit artikel wordt uitgelegd hoe u toegang krijgt tot de bron van de lente-Cloud configuratie server en de lente-Cloud service die wordt beheerd door Azure lente-Cloud met behulp van Azure Active Directory (op rollen gebaseerd toegangs beheer op basis van functies (Azure AD).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Rol toewijzen aan Azure AD-gebruiker/-groep, MSI of Service-Principal

Als u Azure AD en RBAC wilt gebruiken, moet u de rol *Azure lente voor Cloud gegevens lezer* toewijzen aan een gebruiker, groep of Service-Principal met behulp van de volgende procedure:

1. Ga naar de overzichts pagina van het service-exemplaar.

2. Klik op **Access Control (IAM)** om de Blade toegangs beheer te openen.

3. Klik op de knop **toevoegen** en **Voeg roltoewijzingen toe** (er is mogelijk een autorisatie vereist om toe te voegen).

4. Zoek en selecteer de *Azure lente-gegevens lezer* voor de Cloud onder **rol**.
5. Toegang tot `User, group, or service principal` of op `User assigned managed identity` basis van het gebruikers type toewijzen. Zoek en selecteer gebruiker.  
6. Klik op `Save`

   ![toewijzing-rol](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Configuratie server-en service register eindpunten openen

Nadat de Azure lente-rol voor de Cloud gegevens lezer is toegewezen, hebben klanten toegang tot de lente-Cloud configuratie server en de veer register eindpunten in de Cloud service. Gebruik de volgende procedures:

1. Een toegangs Token ophalen. Nadat een Azure AD-gebruiker de rol van Azure lente voor Cloud gegevens lezer heeft toegewezen, kunnen klanten de volgende opdrachten gebruiken om zich aan te melden bij Azure CLI met de gebruiker, Service-Principal of beheerde identiteit om een toegangs token te verkrijgen. Zie [Azure cli verifiëren](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)voor meer informatie. 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Het eind punt opstellen. We ondersteunen standaard eindpunten van de lente-Cloud configuratie server en de lente-Cloud service die wordt beheerd door de Azure lente-Cloud. Zie [Production Ready endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)(Engelstalig) voor meer informatie. Klanten kunnen ook een volledige lijst met ondersteunde eind punten ophalen van de bron van de lente-Cloud configuratie server en het Cloud service-REGI ster dat wordt beheerd door Azure lente-Cloud door toegang te krijgen tot eind punten:

    * *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/*
    * *https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/* 

3. Open het samengestelde eind punt met het toegangs token. Plaats het toegangs token in een header om autorisatie te bieden.  Alleen de methode GET wordt ondersteund.

    Bijvoorbeeld: toegang tot een eind punt zoals *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health* om de status van Eureka te zien.

    Als het antwoord 401 niet- *geautoriseerd* is, controleert u of de rol is toegewezen.  Het duurt enkele minuten voordat de rol van kracht wordt of Controleer of het toegangs token niet is verlopen.

## <a name="next-steps"></a>Volgende stappen
* [Azure CLI verifiëren](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Gereedgemelde eind punten voor productie](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Zie ook
* [Rollen en machtigingen maken](how-to-permissions.md)
