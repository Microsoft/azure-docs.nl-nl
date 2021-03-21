---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75893977"
---
Wanneer u een Azure Machine Learning-werkruimte maakt of een resource die door de werkruimte wordt gebruikt, ontvangt u mogelijk een foutbericht die op een van de volgende berichten lijkt:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

De meeste resourceproviders worden automatisch geregistreerd, maar niet allemaal. Als u dit bericht ontvangt, moet u de vermelde provider registreren.

Zie [Fouten oplossen voor de registratie van de resourceprovider](../articles/azure-resource-manager/templates/error-register-resource-provider.md) voor informatie over het registreren van resourceproviders.