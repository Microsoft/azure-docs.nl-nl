---
title: Restart-Azure Portal-Azure Database for PostgreSQL-flexibele server
description: In dit artikel wordt beschreven hoe u opnieuw opstart bewerkingen uitvoert in Azure Database for PostgreSQL via de Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90934872"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-flexibele server opnieuw opstarten

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Dit artikel bevat stapsgewijze procedures voor het opnieuw opstarten van de flexibele server. Deze bewerking is handig voor het Toep assen van statische parameter wijzigingen waarvoor opnieuw opstarten van de database server is vereist. De procedure is hetzelfde voor servers die zijn geconfigureerd met een zone redundant hoge Beschik baarheid. 

> [!IMPORTANT]
> Wanneer de configuratie met hoge Beschik baarheid is geconfigureerd, worden de primaire en stand-by-servers op hetzelfde moment opnieuw gestart.

## <a name="pre-requisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een flexibele server hebben.

## <a name="restart-your-flexible-server"></a>De flexibele server opnieuw opstarten

Voer de volgende stappen uit om de flexibele server opnieuw op te starten.

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u opnieuw wilt opstarten.

2.  Klik op **overzicht** in het linkerdeel venster en klik op **opnieuw opstarten**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Selectie opnieuw starten":::

3.  Er wordt een pop-upbericht weer gegeven.

4.  Klik op **Ja** als u wilt door gaan.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Opnieuw opstarten bevestigen":::
 
6.  Er wordt een melding weer gegeven dat de herstart bewerking is gestart.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
