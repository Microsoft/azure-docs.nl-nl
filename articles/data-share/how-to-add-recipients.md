---
title: Ontvangers toevoegen in azure data share
description: Meer informatie over het toevoegen van geadresseerden aan een bestaande gegevens share in azure data share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680632"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Een ontvanger toevoegen aan uw share

U kunt een ontvanger toevoegen wanneer u een nieuwe share of een bestaande share maakt. Vanuit de Azure data share-gebruikers interface kunt u een ontvanger toevoegen met behulp van de Azure-aanmeldings-e-mail van de gebruiker.  Vanuit API kunt u een combi natie van gebruikers-of Service-Principal en Tenant-ID gebruiken. Wanneer een Tenant-ID is opgegeven, kan de uitnodiging alleen worden geaccepteerd in deze Tenant. Vanuit API kunt u ook een uitnodiging maken zonder een e-mail naar de ontvanger te verzenden. 

## <a name="add-recipient-to-an-existing-share"></a>Ontvanger toevoegen aan een bestaande share

In azure data share gaat u naar de verzonden share en selecteert u het tabblad **uitnodigingen** . Hier vindt u alle ontvangers van uitnodigingen voor deze gegevens share. Als u een nieuw item wilt toevoegen, klikt u op **ontvanger toevoegen**.

![In de scherm opname wordt de ontvanger toevoegen geselecteerd.](./media/how-to/how-to-add-recipients/add-recipient.png)

Er wordt een paneel aan de rechterkant van de pagina weergegeven. Klik op **ontvanger toevoegen** en vul vervolgens het e-mail adres van uw nieuwe ontvanger in op de lege regel. Zorg ervoor dat u de e-mail adres van de ontvanger van de geadresseerde gebruikt (de e-mail alias werkt niet). 

![Scherm afbeelding toont het deel venster ontvanger toevoegen waar u een uitnodiging kunt toevoegen en verzenden.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Klik op **toevoegen en uitnodiging verzenden**. E-mail berichten worden verzonden naar de nieuwe ontvanger (s).

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [verwijderen van een uitnodiging voor een share](how-to-delete-invitation.md).
