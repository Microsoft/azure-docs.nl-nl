---
title: Toegangssleutel voor Azure SignalR Service roteren
description: In dit artikel leest u waarom de klant de toegangssleutels regelmatig moet roteren en hoe dit kan worden gedaan via de gebruikersinterface van de Azure-portal en de Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1f80742d48fc46c8c5e337794100ca5901818c88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184386"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Toegangssleutel voor Azure SignalR Service roteren

Elk Azure SignalR Service-exemplaar heeft een paar toegangssleutels: de Primaire en de Secundaire sleutel. Deze worden gebruikt om SignalR-klanten te verifiëren wanneer aanvragen bij de service worden gedaan. De sleutels zijn gekoppeld aan de eindpunt-URL van het exemplaar. Het is verstandig uw sleutels te beveiligen en regelmatig te roteren. U krijgt twee toegangssleutels, zodat u met één sleutel verbonden kunt blijven terwijl u de andere sleutel opnieuw genereert.

## <a name="why-rotate-access-keys"></a>Waarom moet u toegangssleutels roteren?

Uit veiligheidsoverwegingen en nalevingsvereisten moet u uw toegangssleutels regelmatig roteren.

## <a name="regenerate-access-keys"></a>Toegangssleutels regenereren

1. Ga naar de [Azure Portal](https://portal.azure.com/)en meld u aan met uw referenties.

1. Zoek de sectie **Sleutels** in het Azure SignalR Service-exemplaar met de sleutels die u opnieuw wilt genereren.

1. Klik in het navigatiemenu op **Sleutels**.

1. Selecteer **Primaire sleutel opnieuw genereren** of **Secundaire sleutel opnieuw genereren**.

   Er worden een nieuwe sleutel en bijbehorende verbindingsreeks gemaakt en weergegeven.

   ![Sleutels opnieuw genereren](media/signalr-howto-key-rotation/regenerate-keys.png)

U kunt sleutels ook opnieuw genereren met behulp van de [Azure CLI](/cli/azure/signalr/key#az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Configuraties bijwerken met nieuwe verbindingsreeksen

1. Kopieer de opnieuw gegenereerde verbindingsreeks.

1. Werk alle configuraties zo bij dat deze gebruikmaken van de nieuwe verbindingsreeks.

1. Start de toepassing opnieuw op, indien nodig.

## <a name="forced-access-key-regeneration"></a>Toegangssleutels geforceerd opnieuw genereren

Azure SignalR Service kan in bepaalde situaties afdwingen dat een toegangssleutel opnieuw wordt gegenereerd. Klanten worden hiervan per e-mail en via de portal op de hoogte gesteld. Als u een dergelijk bericht ontvangt of als er een fout in de service optreedt met betrekking tot een toegangssleutel, moet u de sleutels roteren volgens de instructies in deze gids.

## <a name="next-steps"></a>Volgende stappen

Het regelmatig roteren van toegangssleutels is een goede beveiligingstoepassing.

In deze gids hebt u geleerd hoe u toegangssleutels opnieuw kunt genereren. Ga door met de volgende zelfstudies over verificatie met OAuth of met Azure Functions.

> [!div class="nextstepaction"]
> [Integreren met ASP.NET core-identiteit](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Een serverloze, realtime app met verificatie bouwen](./signalr-tutorial-authenticate-azure-functions.md)