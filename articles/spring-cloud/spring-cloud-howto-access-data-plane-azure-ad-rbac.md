---
title: Gegevens vlak benaderen met Azure AD, RBAC
description: Toegang tot data-vlak met Azure Active Directory op rollen gebaseerd toegangs beheer.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220119"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Toegang tot het gegevens vlak met Azure Active Directory en op rollen gebaseerde Access Control

In dit artikel wordt uitgelegd hoe u toegang krijgt tot de Azure lente Cloud config server en Service Registry-eind punten met behulp van Azure Active Directory (AAD) op rollen gebaseerd toegangs beheer (RBAC).

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Rol toewijzen aan AAD-gebruiker/-groep, MSI of Service-Principal

Als u AAD en RBAC wilt gebruiken, moet u de rol *Azure lente voor Cloud gegevens lezer* toewijzen aan een gebruiker, groep of Service-Principal met behulp van de volgende procedure:

1. Ga naar de overzichts pagina van het service-exemplaar.

2. Klik op **Access Control (IAM)** om de Blade toegangs beheer te openen.

3. Klik op de knop **toevoegen** en **Voeg roltoewijzingen toe** (er is mogelijk een autorisatie vereist om toe te voegen).

4. Zoek en selecteer de *Azure lente-gegevens lezer* voor de Cloud onder **rol**.
5. Toegang tot `User, group, or service principal` of op `User assigned managed identity` basis van het gebruikers type toewijzen. Zoek en selecteer gebruiker.  
6. Klik op `Save`

   ![toewijzing-rol](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Access Data-vlak

Nadat een AAD-gebruiker de rol van *Azure lente voor Cloud gegevens lezer* heeft toegewezen, kunnen klanten zich aanmelden bij Azure CLI met gebruiker, Service-Principal of beheerde identiteit.  Zie [Azure cli verifiëren](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) voor het verkrijgen van een toegangs token.  Gebruik vervolgens de volgende opdrachten.

```azurecli
az login
az account get-access-token
```

De CLI ondersteunt momenteel de standaard-eind punten van de configuratie server en het service register. Zie [Production Ready endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)(Engelstalig) voor meer informatie. 

Klanten kunnen ook een volledige lijst met ondersteunde eind punten van de configuratie server en het service register verkrijgen door toegang te krijgen tot eind punten:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

Het toegangs token in de header biedt autorisatie. Alleen de methode GET wordt ondersteund.

Bijvoorbeeld: toegang tot een eind punt zoals *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* om de status van Eureka te zien.

De verschillende hoofd-eind punten worden hieronder weer gegeven op basis van verschillende typen Clouds.

| Cloud          | Hoofd eindpunt              |
| -------------- | -------------------------- |
| Openbaar         | svc.azuremicroservices.io  |
| Moon cake/China | svc.microservices.azure.cn |

Als het antwoord 401 niet- *geautoriseerd* is, controleert u of de rol is toegewezen.  Het duurt enkele minuten voordat de rol van kracht wordt of Controleer of het toegangs token niet is verlopen.

## <a name="next-steps"></a>Volgende stappen
* [Azure CLI verifiëren](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Gereedgemelde eind punten voor productie](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Zie ook
* [Rollen en machtigingen maken](spring-cloud-howto-permissions.md)