---
title: Delen buiten uw eigen organisatie (Azure Portal) - Quickstart over Azure Data Share
description: Leer in deze quickstart meer over het delen van gegevens met klanten en partners met behulp van Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 10/30/2020
ms.openlocfilehash: 1442720fdf48aaa7da76e181b168a04306ff3e33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96186453"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Quickstart: Gegevens delen met Azure Data Share in Azure Portal

In deze quickstart leert u hoe u een nieuwe Azure Data Share instelt om gegevens uit het opslagaccount te delen met behulp van Azure Portal.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Het e-mailadres voor Azure van de ontvanger (de e-mailalias werkt niet).
* Als de bron-Azure-gegevensopslag zich in een ander Azure-abonnement bevindt dan de opslag die u gebruikt om een Data Share-resource te maken, registreert u de [resourceprovider Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waarin de Azure-gegevensopslag zich bevindt. 

### <a name="share-from-a-storage-account"></a>Delen vanuit een opslagaccount

* Een Azure Storage-account: U kunt een gratis [Azure Storage-account](../storage/common/storage-account-create.md) aanmaken als u nog niet een hebt
* Machtiging om naar het opslagaccount te schrijven, aanwezig in *Microsoft.Storage/storageAccounts/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Machtiging om roltoewijzing toe te voegen aan het opslagaccount, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol **Eigenaar**. 

## <a name="create-a-data-share-account"></a>Een Data Share-account maken

Maak een Azure Data Share-resource in een Azure-resourcegroep.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal.

1. Zoek naar *Data Share*.

1. Selecteer **Data Share** en selecteer **Maken**.

1. Vul de basisgegevens van uw Azure Data Share-resource in met de volgende informatie. 

   **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
   |---|---|---|
   | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw gegevensshare-account.|
   | Resourcegroep | *test-resource-group* | Gebruik een bestaande resourcegroep of maak een nieuwe. |
   | Locatie | *US - oost 2* | Geef een regio op voor uw gegevensshare-account.
   | Naam | *datashareaccount* | Geef een naam op voor uw gegevensshare-account. |

1. Selecteer **Beoordelen en maken** en vervolgens **Maken** om uw gegevensshare-account in te richten. Het inrichten van een nieuw gegevensshare-account duurt doorgaans 2 minuten of minder.

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

## <a name="create-a-share"></a>Een share maken

1. Ga naar de overzichtspagina van uw gegevensshare.

   ![Uw gegevens delen](./media/share-receive-data.png "Uw gegevens delen") 

1. Selecteer **Beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.

1. Vul de details in voor uw share. Geef een naam, type share, beschrijving van de share-inhoud en gebruiksvoorwaarden (optioneel) op. 

   ![EnterShareDetails](./media/enter-share-details.png "Gegevens van share invoeren") 

1. Selecteer **Doorgaan**.

1. Selecteer **Gegevenssets toevoegen** om gegevenssets toe te voegen aan de share. 

   ![Gegevenssets toevoegen aan de share](./media/datasets.png "Gegevenssets")

1. Selecteer het type gegevensset dat u wilt toevoegen. Welke lijst met typen gegevensset wordt weergegeven, is afhankelijk van het type share (momentopname of in-place) dat u in de vorige stap hebt geselecteerd. 

   ![AddDatasets](./media/add-datasets.png "Gegevenssets toevoegen")    

1. Navigeer naar het object dat u wilt delen en selecteer Gegevenssets toevoegen. 

   ![SelectDatasets](./media/select-datasets.png "Gegevenssets selecteren")    

1. Voer op het tabblad Ontvangers de e-mailadressen in van uw gegevensgebruiker door + Ontvanger toevoegen te selecteren.

   ![AddRecipients](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **Doorgaan**.

1. Als u het type share momentopname hebt geselecteerd, kunt u het momentopnameschema configureren om updates van uw gegevens naar uw gegevensgebruiker te sturen. 

   ![EnableSnapshots](./media/enable-snapshots.png "Momentopnamen inschakelen") 

1. Selecteer een begintijd en een herhalingsinterval. 

1. Selecteer **Doorgaan**.

1. Controleer op het tabblad Beoordelen en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**.

Uw Azure-gegevensshare is nu gemaakt en de ontvanger van uw gegevensshare kan nu uw uitnodiging accepteren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resource niet langer nodig hebt, gaat u naar de pagina **Overzicht Data Share** en selecteert u **Verwijderen** om de resource te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Data Share maakt. Voor meer informatie over hoe een gegevensgebruiker een gegevensshare kan accepteren en ontvangen, gaat u door naar de zelfstudie voor het [accepteren en ontvangen van gegevens](subscribe-to-data-share.md). 
