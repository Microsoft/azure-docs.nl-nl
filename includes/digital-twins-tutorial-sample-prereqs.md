---
author: baanders
description: inclusief bestand voor Azure Digital Twins-zelfstudies, vereisten voor het voorbeeldproject
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103621943"
---
## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze zelf studie wilt uitvoeren, moet u eerst de volgende vereisten volt ooien. 

Als u geen abonnement op Azure hebt, **maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** voordat u begint.

### <a name="get-required-resources"></a>Vereiste resources ophalen

Als u deze zelf studie wilt volt ooien, **installeert u [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), versie 16,5 of hoger** op uw ontwikkel computer. Als u al een oudere versie hebt geïnstalleerd, kunt u de app *Visual Studio Installer* op uw machine openen en de prompts volgen om uw installatie bij te werken.

>[!NOTE]
> Zorg ervoor dat uw installatie van Visual Studio 2019 de **[werk belasting Azure Development](/dotnet/azure/configure-visual-studio)** bevat. Met deze workload kan een toepassing Azure functions publiceren en andere Azure-ontwikkel taken uitvoeren.

De zelfstudie is gebaseerd op een voorbeeldproject dat is geschreven in C#. Het voorbeeld is hier te vinden: [End-to-end-voorbeelden voor Azure Digital Twins](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Down load het voorbeeld project** op uw machine door te navigeren naar de voorbeeld koppeling en de knop *door de code bladeren* te selecteren onder de titel. Hiermee gaat u naar de GitHub-opslag plaats voor de voor beelden, die u als een kunt downloaden *. ZIP* door de *code* knop te selecteren en de *zip te downloaden*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Weer gave van de opslag plaats van Digital-apparaatdubbels-samples op GitHub. De knop code is geselecteerd en er wordt een klein dialoog venster geproduceerd waarin de knop voor het downloaden van een ZIP is gemarkeerd." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Hiermee wordt een gedownload *. ZIP* -map naar de computer als **digital-twins-samples-master.zip**. Pak de map uit en extraheer de bestanden.

### <a name="prepare-an-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar voorbereiden

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
