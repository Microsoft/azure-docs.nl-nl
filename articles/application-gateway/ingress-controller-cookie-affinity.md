---
title: Affiniteit op basis van cookies met Application Gateway inschakelen
description: Dit artikel bevat informatie over het inschakelen van op cookies gebaseerde affiniteit met een Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397413"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Affiniteit op basis van cookies met een Application Gateway inschakelen
Zoals beschreven in de [documentatie over Azure-toepassing gateway](./application-gateway-components.md#http-settings), ondersteunt Application Gateway de op cookies gebaseerde affiniteit, wat betekent dat het volgende verkeer van een gebruikers sessie naar dezelfde server kan worden doorgestuurd voor verwerking.

## <a name="example"></a>Voorbeeld
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```