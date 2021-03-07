---
title: Gebruik de Azure Portal om activerings fouten te oplossen die betrekking hebben op Azure Stack Edge Pro met GPU | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen met de activering van Azure Stack Edge Pro GPU en de sleutel kluis kunt oplossen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442142"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Activerings problemen op uw Azure Stack Edge Pro GPU-apparaat oplossen 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u activerings problemen oplost op uw Azure Stack Edge Pro GPU-apparaat. 


## <a name="activation-errors"></a>Activerings fouten

De volgende tabel bevat een overzicht van de fouten met betrekking tot het activeren van apparaten en de bijbehorende aanbevolen oplossing.

| Fout bericht| Aanbevolen oplossing |
|------------------------------------------------------|--------------------------------------|
| Als de Azure Key Vault gebruikt voor activering wordt verwijderd voordat het apparaat wordt geactiveerd met de activerings sleutel, wordt deze fout weer gegeven. <br> ![Sleutel kluis fout 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Als de sleutel kluis is verwijderd, kunt u de sleutel kluis herstellen als de kluis zich in de duur van de schone beveiliging bevindt. Volg de stappen in [een sleutel kluis herstellen](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). <br>Als de duur voor het leegmaken van de beveiliging is verstreken, kan de sleutel kluis niet worden hersteld. Neem contact op met Microsoft Ondersteuning voor volgende stappen. |
| Als de Azure Key Vault wordt verwijderd nadat het apparaat is geactiveerd en u vervolgens een bewerking probeert uit te voeren waarvoor versleuteling is vereist, bijvoorbeeld: **gebruiker toevoegen**, **share toevoegen**, **berekening configureren**, wordt deze fout weer gegeven. <br> ![Sleutel kluis fout 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Als de sleutel kluis is verwijderd, kunt u de sleutel kluis herstellen als de kluis zich in de duur van de schone beveiliging bevindt. Volg de stappen in een sleutel kluis herstellen. <br>Als de duur voor het leegmaken van de beveiliging is verstreken, kan de sleutel kluis niet worden hersteld. Neem contact op met Microsoft Ondersteuning voor volgende stappen. |
| Als de sleutel voor de integriteit van het kanaal in de Azure Key Vault wordt verwijderd en u vervolgens probeert om bewerkingen uit te voeren waarvoor versleuteling is vereist, bijvoorbeeld: **gebruiker toevoegen**, **share toevoegen**, **Compute configureren** -vervolgens ontvangt u deze fout. <br> ![Sleutel kluis fout 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Als de integriteits sleutel van het kanaal in de sleutel kluis wordt verwijderd, maar deze nog steeds binnen de duur van het leegmaken ligt, volgt u de stappen in [ongedaan maken sleutel kluis sleutel verwijderen](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Als de duur voor het leegmaken van de beveiliging is verstreken, en als u de sleutel hebt gemaakt, kunt u herstellen vanuit de back-up, anders kan de sleutel niet worden hersteld. Neem contact op met Microsoft Ondersteuning voor volgende stappen. |
| Als de activerings sleutel niet kan worden gegenereerd vanwege een fout, wordt deze fout weer gegeven. Meer informatie is te vinden in de melding. <br> ![Sleutel kluis fout 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Zorg ervoor dat de poorten en Url's die zijn opgegeven in de [Access-Azure Key Vault achter een firewall](../key-vault/general/access-behind-firewall.md) zijn geopend op de firewall om toegang te krijgen tot de Key Vault. Wacht een paar minuten en voer de bewerking opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| Als de gebruiker alleen-lezen machtigingen heeft, mag de gebruiker geen activerings sleutel genereren en deze fout wordt weer gegeven. <br> ![Sleutel kluis fout 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Dit kan zijn omdat u niet de juiste toegang of  *micro soft hebt. de kluis* is niet geregistreerd.<li>Zorg ervoor dat u toegang hebt tot de eigenaar of Inzender op het niveau van de resource groep die wordt gebruikt voor uw Azure Stack Edge-resource.</li><li>Zorg ervoor dat de resource provider micro soft.-kluis is geregistreerd. Als u een resource provider wilt registreren, gaat u naar het abonnement dat wordt gebruikt voor Azure Stack Edge-resource. Ga naar **resource providers**, zoek naar *micro soft.-kluis* en selecteer en **registratie**.</li> |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met apparaten](azure-stack-edge-gpu-troubleshoot.md).
