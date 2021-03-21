---
title: Een taak invoer maken op basis van een HTTPS-URL
description: In dit onderwerp ziet u hoe u een Azure Media Services taak invoer maakt op basis van een HTTPS-URL.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1073a13f477894e1b35d732fd1cd6191747a62a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955984"
---
# <a name="create-a-job-input-from-an-https-url"></a>Een taak invoer maken op basis van een HTTPS-URL

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer u in Media Services v3 taken verzendt voor het verwerken van uw video's, moet u aan Media Services de locatie van de invoervideo doorgeven. Een van de opties is dat u een HTTPS-URL als taakinvoer opgeeft (zoals in dit voorbeeld). Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's. Voor een volledig voor beeld raadpleegt u dit [github voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Controleer voordat u begint met het ontwikkelen [met behulp van Media Services v3 api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort).

## <a name="net-sample"></a>.NET-voor beeld

De volgende code laat zien hoe u een taak met een HTTPS-URL-invoer maakt.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

[Een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md).
