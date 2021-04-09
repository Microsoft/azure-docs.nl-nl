---
title: Een aangepaste prijs opstaande tegel in azure-Dash boards gebruiken
description: Meer informatie over het toevoegen van een tegel met korting op een Azure-dash board voor het weer geven van statische inhoud
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774453"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Een tegel met korting op Azure-Dash boards gebruiken om aangepaste inhoud weer te geven

U kunt een tegel met korting toevoegen aan uw Azure-Dash boards om aangepaste statische inhoud weer te geven. U kunt bijvoorbeeld basis instructies, een afbeelding of een set hyper links weer geven op een tegel met korting.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Een tegel met korting toevoegen aan uw dash board

1. Selecteer **dash board** in de Azure Portal zijbalk.

   ![Scherm opname van portal zijbalk](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Selecteer in de dashboard weergave het dash board waar de tegel aangepaste prijs verlaging moet worden weer gegeven en selecteer vervolgens **bewerken**.

   ![Scherm opname van weer gave dash board bewerken](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Zoek in de **tegel galerie** de tegel met de naam **prijs** opgave en selecteer **toevoegen**. De tegel wordt toegevoegd aan het dash board en het deel venster **verlaging bewerken** wordt geopend.

1. Voer waarden in voor **titel** en **ondertitel**, die op de tegel worden weer gegeven nadat u naar een ander veld hebt verplaatst.

   ![Scherm opname van de resultaten van het invoeren van de titel en subtitel](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecteer een van de opties voor het opnemen van de inhoud van de prijs opgave: **inline bewerken** of **Voeg inhoud in met behulp** van een URL.

   - Selecteer **inline bewerken** als u de korting rechtstreeks wilt invoeren.

      ![Scherm opname van het invoeren van inline-inhoud](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selecteer **inhoud met behulp van URL invoegen** als u bestaande inhoud voor prijs verlaging wilt gebruiken die online wordt gehost.

      ![Scherm opname van invoeren van URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Voor extra beveiliging kunt u een afkortings bestand maken en dit opslaan in een [Azure Storage-account-BLOB waarvoor versleuteling is ingeschakeld](../storage/common/storage-service-encryption.md). vervolgens wijst u het bestand met de URL-optie aan. De inhoud van de prijs verlaging wordt versleuteld via de versleutelings opties van het opslag account. Alleen gebruikers met machtigingen voor het bestand kunnen de inhoud van de korting op het dash board zien. Mogelijk moet u een CORS-regel [(cross-Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) voor het opslag account instellen, zodat de Azure Portal ( _https://portal.azure.com/_ ) toegang kan krijgen tot het bestand met de korting in de blob.

1. Selecteer **gereed** om het deel venster **prijs verlaging bewerken** te sluiten. Uw inhoud wordt weer gegeven op de tegel prijs verlaging, die u kunt verg Roten of verkleinen door de greep in de rechter benedenhoek te slepen.

   ![Scherm opname van tegel met aangepaste prijs verlaging](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Mogelijkheden en beperkingen van de inhoud prijs verlaging

U kunt elke combi natie van de syntaxis voor tekst zonder opmaak en de inhoud van een HTML-bestand gebruiken op de tegel prijs verlaging. De Azure Portal gebruikt een open-source bibliotheek met de naam _gemarkeerd_ om uw inhoud te TRANSFORMEREN in HTML die wordt weer gegeven op de tegel. De HTML-code die wordt geproduceerd door _gemarkeerd_ , wordt vooraf verwerkt door de portal voordat deze wordt weer gegeven. Met deze stap zorgt u ervoor dat uw aanpassing geen invloed heeft op de beveiliging of indeling van de portal. Tijdens deze vooraf-verwerking wordt elk deel van de HTML dat een mogelijke bedreiging vormt, verwijderd. De volgende typen inhoud zijn niet toegestaan door de portal:

* Java script: `<script>` Tags en inline java script-evaluaties worden verwijderd.
* iframes- `<iframe>` Tags worden verwijderd.
* Style- `<style>` Tags worden verwijderd. Inline stijl kenmerken voor HTML-elementen worden niet officieel ondersteund. Het kan voor komen dat sommige inline-stijl elementen werken voor u, maar als deze de indeling van de portal beïnvloeden, kunnen ze op elk gewenst moment niet meer werken. De tegel prijs verlaging is bedoeld voor basis statische inhoud die gebruikmaakt van de standaard stijlen van de portal.

## <a name="next-steps"></a>Volgende stappen

* Zie [Dash boards maken en delen in de Azure portal voor het maken van](../azure-portal/azure-portal-dashboards.md) een aangepast dash board.
