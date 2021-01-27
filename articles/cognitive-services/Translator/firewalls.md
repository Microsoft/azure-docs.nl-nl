---
title: Vertalen achter firewalls-Translator
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator kan worden vertaald achter firewalls met behulp van de domein naam of IP-filtering.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: f5dd72328180574809c812d670f8165ad84963ae
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897744"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Achter IP-firewalls vertalen met Translator

Translator kan worden omgezet achter firewalls met behulp van de domein naam of IP-filtering. Het filteren van domein namen is de voorkeurs methode. Het is **niet raadzaam om** micro soft Translator uit te voeren achter een door IP gefilterde firewall. De installatie wordt waarschijnlijk zonder voorafgaande kennisgeving in de toekomst beëindigd.

## <a name="translator-ip-addresses"></a>NAT-IP-adressen
De IP-adressen voor api.cognitive.microsofttranslator.com-Translator vanaf 21 augustus 2019:

* **Azië en Stille Oceaan:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Noord-Amerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Achter IP-firewalls vertalen in Translator](reference/v3-0-translate.md)
