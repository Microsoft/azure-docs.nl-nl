---
title: Informatie over het bijwerken van het apparaat voor IoT Hub verificatie en autorisatie | Microsoft Docs
description: Meer informatie over de update van het apparaat voor IoT Hub gebruik van Azure RBAC om verificatie en autorisatie te bieden voor gebruikers en service-Api's.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558394"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Op rollen gebaseerd toegangs beheer (RBAC) van Azure en updates van het apparaat

Bij het bijwerken van het apparaat wordt gebruikgemaakt van Azure RBAC om verificatie en autorisatie te bieden voor gebruikers en service-Api's.

## <a name="configure-access-control-roles"></a>Toegangs beheer rollen configureren

Om ervoor te zorgen dat andere gebruikers en toepassingen toegang hebben tot de update van het apparaat, moeten gebruikers of toepassingen toegang krijgen tot deze resource. Dit zijn de rollen die worden ondersteund door update van het apparaat

|   Naam rol   | Beschrijving  |
| :--------- | :---- |
|  Beheerder apparaat bijwerken | Heeft toegang tot alle resources voor het bijwerken van apparaten  |
|  Apparaat bijwerken lezer| Kan alle updates en implementaties weer geven |
|  Beheerder van de inhoud van het apparaat bijwerken | Kan updates weer geven, importeren en verwijderen  |
|  Lezer inhoud apparaat bijwerken | Kan updates weer geven  |
|  Beheerder voor update-implementaties van apparaten | Kan de implementatie van updates op apparaten beheren|
|  Lezer voor update-implementaties van apparaat| Kan implementaties van updates op apparaten weer geven |

Een combi natie van rollen kan worden gebruikt om het juiste toegangs niveau te bieden. Een ontwikkelaar kan bijvoorbeeld updates importeren en beheren met de rol inhouds beheerder voor het bijwerken van het apparaat, maar moet een rol voor het bijwerken van de update van het apparaat hebben om de voortgang van een update weer te geven. Daarentegen kan een oplossings operator met de rol apparaat update Reader alle updates weer geven, maar moet de beheerdersrol update-implementaties van het apparaat gebruiken om een specifieke update op apparaten te implementeren.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Verificatie bij de updates van REST-Api's voor het apparaat voor publicatie en beheer

Bij het bijwerken van apparaten wordt ook Azure AD gebruikt voor verificatie voor het publiceren en beheren van inhoud via service-Api's. Om aan de slag te gaan, moet u een client toepassing maken en configureren.

### <a name="create-client-azure-ad-app"></a>Client Azure AD-app maken

Als u een toepassing of service met Azure AD wilt integreren, moet u eerst een toepassing [registreren](../active-directory/develop/quickstart-register-app.md) bij Azure AD. De installatie van de client toepassing is afhankelijk van de gebruikte autorisatie stroom.  Hieronder vindt u meer informatie over het gebruik van de update REST Api's van het apparaat.

* Client authenticatie instellen: ' omleidings-Uri's voor native of webclient '.
* API-machtigingen instellen: voor het bijwerken van het apparaat voor IoT Hub wordt het volgende getoond:
  * Gedelegeerde machtigingen: ' user_impersonation '
  * **Optioneel**, toestemming verlenen aan beheerder

[Volgende stappen: resources voor het bijwerken van apparaten maken en rollen voor toegangs beheer configureren](./create-device-update-account.md)