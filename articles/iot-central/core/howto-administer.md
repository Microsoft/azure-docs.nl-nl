---
title: Instellingen van Azure IoT Central-toepassing wijzigen | Microsoft Docs
description: Als beheerder kunt u uw Azure IoT Central-toepassing beheren door de toepassings naam, URL, afbeelding uploaden en een toepassing te verwijderen
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 81cd2ca1cb47e6fdfb4858df930b73c1bd10118a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091818"
---
# <a name="change-iot-central-application-settings"></a>IoT Central toepassings instellingen wijzigen



In dit artikel wordt beschreven hoe u, als beheerder, de toepassing kunt beheren door de naam en URL van de toepassing te wijzigen, een installatie kopie te uploaden en een toepassing te verwijderen in uw Azure IoT Central-toepassing.

Voor toegang tot en gebruik van de sectie **beheer** moet u de rol **beheerder** hebben voor een Azure IOT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan **de beheerdersrol** voor die toepassing.

## <a name="change-application-name-and-url"></a>Toepassings naam en-URL wijzigen

Op de pagina **Toepassings instellingen** kunt u de naam en URL van uw toepassing wijzigen en vervolgens **Opslaan** selecteren.

![Pagina Toepassings instellingen](media/howto-administer/image0-a.png)

Als uw beheerder een aangepast thema voor uw toepassing maakt, bevat deze pagina een optie om de **toepassings naam** in de gebruikers interface te verbergen. Deze optie is handig als het toepassings logo in het aangepaste thema de naam van de toepassing bevat. Zie [de Azure IOT Central-gebruikers interface aanpassen](./howto-customize-ui.md)voor meer informatie.

> [!Note]
> Als u uw URL wijzigt, kan uw oude URL worden overgenomen door een andere Azure IoT Central-klant. Als dat het geval is, kan het niet meer worden gebruikt. Wanneer u uw URL wijzigt, werkt de oude URL niet meer en moet u uw gebruikers op de hoogte stellen van de nieuwe URL die u wilt gebruiken.

## <a name="delete-an-application"></a>Een toepassing verwijderen

Gebruik de knop **verwijderen** om uw IOT Central-toepassing permanent te verwijderen. Met deze actie worden alle gegevens die aan de toepassing zijn gekoppeld, definitief verwijderd.

> [!Note]
> Als u een toepassing wilt verwijderen, moet u ook machtigingen hebben om resources te verwijderen in het Azure-abonnement dat u hebt gekozen tijdens het maken van de toepassing. Zie [Azure-rollen toewijzen om de toegang tot uw Azure-abonnements resources te beheren](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="manage-programmatically"></a>Programmatisch beheren

IoT Central Azure Resource Manager SDK-pakketten zijn beschikbaar voor knoop punt, Python, C#, Ruby, Java en go. U kunt deze pakketten gebruiken om IoT Central-toepassingen te maken, weer te geven, bij te werken of te verwijderen. De pakketten bevatten helpers voor het beheren van verificatie en het afhandelen van fouten.

Hier vindt u voor beelden van hoe u de Azure Resource Manager Sdk's kunt gebruiken op [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) .

Voor meer informatie raadpleegt u de volgende GitHub-opslag plaatsen en-pakketten:

| Taal | Opslagplaats | Pakket |
| ---------| ---------- | ------- |
| Knooppunt | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure IoT Central-toepassing beheert, is de voorgestelde volgende stap meer informatie over het [beheren van gebruikers en rollen](howto-manage-users-roles.md) in azure IOT Central.
