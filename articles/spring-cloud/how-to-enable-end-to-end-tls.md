---
title: End-to-end-Transport Layer Security inschakelen
titleSuffix: Azure Spring Cloud
description: End-to-end-Transport Layer Security inschakelen voor een toepassing.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227803"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>End-to-end TLS inschakelen voor een toepassing

In dit onderwerp wordt beschreven hoe u end-to-end SSL/TLS kunt gebruiken voor het beveiligen van verkeer van een ingangs controller naar toepassingen die ondersteuning bieden voor HTTPS. Nadat u end-to-end TLS hebt ingeschakeld en een certificaat van de sleutel kluis hebt geladen, worden alle communicaties in azure lente-Cloud beveiligd met TLS.

   ![Grafiek van de communicatie die wordt beveiligd door TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Vereisten 

- Een geïmplementeerd Azure Spring Cloud-exemplaar. Volg onze [quickstart voor het implementeren via de Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) om aan de slag te gaan.
- Als u niet bekend bent met end-to-end TLS, raadpleegt u het [end-to-end TLS](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl)-voor beeld.
- Als u de vereiste certificaten veilig in veer boot-Apps wilt laden, kunt u gebruik maken van de [installatie starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates)van de modus voor het starten van een kluis.


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>End-to-end TLS inschakelen voor een bestaande app

Gebruik de opdracht `az spring-cloud app update --enable-end-to-end-tls` om end-to-end TLS in of uit te scha kelen voor een app.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>End-to-end TLS inschakelen wanneer u een aangepast domein bindt

Gebruik de opdracht `az spring-cloud app custom-domain update --enable-end-to-end-tls` of `az spring-cloud app custom-domain bind --enable-end-to-end-tls` om end-to-end TLS voor een app in of uit te scha kelen.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>End-to-end TLS-status verifiëren

Gebruik de opdracht `az spring-cloud app show` om de waarde van te controleren `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Volgende stappen
* [Toegang tot configuratie server en service register](how-to-access-data-plane-azure-ad-rbac.md)
