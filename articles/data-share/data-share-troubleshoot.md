---
title: Problemen met Azure Data Share oplossen
description: Meer informatie over het oplossen van problemen met uitnodigingen en fouten bij het maken of ontvangen van gegevens shares met Azure data share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: c93ce9c81ada3c30128846b43041603e132abd88
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617235"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Veelvoorkomende problemen in Azure Data Share oplossen 

In dit artikel wordt beschreven hoe u veelvoorkomende problemen met Azure data share oplost. 

## <a name="azure-data-share-invitations"></a>Uitnodigingen voor Azure-gegevens delen 

In sommige gevallen wordt er een lege lijst met uitnodigingen weer gegeven wanneer een nieuwe gebruiker op **uitnodiging accepteren** klikt vanuit de uitnodiging voor het verzenden van e-mail. 

![Geen uitnodigingen](media/no-invites.png)

Dit kan een van de volgende oorzaken hebben:

* **De Azure Data Share-service is niet geregistreerd als een resourceprovider van een Azure-abonnement in de Azure-tenant.** Dit probleem treedt op als er geen Data Share-resource aanwezig is in uw Azure-tenant. Wanneer u een Azure Data Share-resource maakt, wordt de resourceprovider automatisch geregistreerd in uw Azure-abonnement. U kunt de Data Share-service ook handmatig registreren door de volgende stappen uit te voeren. U hebt de rol Azure-inzender nodig om deze stappen te voltooien.

    1. Navigeer in Azure Portal naar **Abonnementen**
    1. Selecteer het abonnement dat u wilt gebruiken voor het maken van een Azure Data Share-resource
    1. Klik op **Resourceproviders**
    1. Zoek naar **Microsoft.DataShare**
    1. Klik op **Registreren** 

    U moet de [rol Azure Inzender](../role-based-access-control/built-in-roles.md#contributor) hebben voor het Azure-abonnement om deze stappen uit te voeren. 

* **Er wordt een uitnodiging verzonden naar uw e-mailalias in plaats van uw e-mailadres voor aanmelding bij Azure.** Als u de Azure Data Share-service hebt geregistreerd of al een Data Share-resource hebt gemaakt in de Azure-tenant, maar de uitnodiging nog steeds niet ziet, kan het zijn dat de provider uw e-mailalias heeft ingevoerd als ontvanger in plaats van uw e-mailadres voor aanmelding bij Azure. Neem contact op met uw gegevensprovider en vraag na of de uitnodiging is verzonden naar uw e-mailadres voor aanmelding bij Azure en niet uw e-mailalias.

* **De uitnodiging is al geaccepteerd.** Via de koppeling in het e-mailbericht gaat u naar de Data Share-uitnodigingspagina in Azure Portal, waar alleen openstaande uitnodigingen worden weergegeven. Als u de uitnodiging al hebt geaccepteerd, wordt deze niet meer weergegeven op de Data Share-uitnodigingspagina. Ga naar uw Data Share-resource die u hebt gebruikt om de uitnodiging te accepteren om ontvangen shares weer te geven en de instelling van uw Azure Data Explorer-doelcluster te configureren.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fout bij het maken of ontvangen van een nieuwe share

Kan geen gegevens sets toevoegen

Fout bij het toewijzen van gegevens sets

' Kan geen gegevens share bron x toegang verlenen tot y '

U hebt niet de juiste machtigingen voor x

"Er kunnen geen schrijf machtigingen voor het Azure data share-account worden toegevoegd aan een of meer van de geselecteerde resources"

Als u een van de bovenstaande fouten ontvangt tijdens het maken van een nieuwe share, het toevoegen van gegevens sets of toewijzings gegevens sets, kan dit worden veroorzaakt door onvoldoende machtigingen voor de Azure-gegevens opslag. Zie de [rollen en vereisten](concepts-roles-permissions.md) voor de vereiste machtigingen. 

U hebt schrijf machtigingen nodig om gegevens te delen of te ontvangen van een Azure-gegevens archief, dat meestal voor komt in de rol **Inzender** . 

Als dit de eerste keer is dat u gegevens uit het Azure-gegevens archief deelt of ontvangt, hebt u ook *micro soft. Authorization/Role Assignments/write* Permission, die meestal voor komt in de rol van **eigenaar** . Zelfs als u de Azure-gegevens opslag resource hebt gemaakt, wordt de eigenaar van de resource niet automatisch door de bron. Met de juiste machtigingen verleent de Azure data share-service automatisch de beheerde identiteits toegang van de gegevens share bron aan het gegevens archief. Het kan enkele minuten duren voordat dit proces is doorgevoerd. Probeer het over enkele minuten opnieuw als u fouten ondervindt als gevolg van deze vertraging.

Voor delen op basis van SQL zijn aanvullende machtigingen vereist. Zie [share van SQL-bronnen](how-to-share-from-sql.md) voor een gedetailleerde lijst met vereisten.

## <a name="snapshot-failed"></a>De moment opname is mislukt
De moment opname kan om verschillende redenen mislukken. U kunt een gedetailleerd fout bericht vinden door te klikken op de begin tijd van de moment opname en vervolgens op de status van elke gegevensset. Hier volgen enkele veelvoorkomende redenen waarom de moment opname is mislukt:

* Gegevens share heeft geen machtiging voor het lezen van de brongegevens opslag of het schrijven naar het doel gegevens archief. Zie de [rollen en vereisten](concepts-roles-permissions.md) voor gedetailleerde machtigings vereisten. Als dit de eerste keer is dat u een moment opname maakt, kan het enkele minuten duren voordat de gegevens share bron toegang krijgt tot de Azure-gegevens opslag. Wacht een paar minuten en probeer het opnieuw.
* De verbinding van de gegevens share met het bron-of doel gegevens archief wordt geblokkeerd door de firewall.
* De gedeelde gegevensset of het bron-of doel gegevens archief wordt verwijderd.

Voor het opslag account zijn de volgende extra oorzaken van fouten in de moment opname.

* Het bestand wordt bijgewerkt in de bron terwijl er een moment opname wordt gemaakt. Dit kan leiden tot een 0-byte bestand bij het doel. Volgende moment opname nadat de update is voltooid op de bron moet slagen.

Voor SQL-bronnen zijn de volgende extra oorzaken van momentopname fouten. 

* Het bron-of doel-SQL-script om een gegevens share-machtiging te verlenen, wordt niet uitgevoerd. Of voor Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW), wordt het uitgevoerd met behulp van SQL-verificatie in plaats van Azure Active Directory-verificatie.  
* Het bron-of doel-SQL-gegevens archief is onderbroken.
* SQL-gegevens typen worden niet ondersteund door het momentopname proces of het doel gegevens archief. Raadpleeg de [share van SQL-bronnen](how-to-share-from-sql.md#supported-data-types) voor meer informatie.
* Het bron-of doel-SQL-gegevens archief is vergrendeld door andere processen. Azure-gegevens share is niet van toepassing op vergren delingen voor de bron-en doel-SQL-gegevens opslag. Bestaande vergren delingen op het bron-en doel-SQL-gegevens archief veroorzaken echter een moment opname.
* Er wordt naar de doel-SQL-tabel verwezen door een foreign key-beperking. Als er tijdens de moment opname een doel tabel met dezelfde naam bestaat, wordt de Azure-gegevens share Table neergezet en wordt er een nieuwe tabel gemaakt. Als er wordt verwezen naar de doel-SQL-tabel met een foreign key-beperking, kan de tabel niet worden verwijderd.
* Het CSV-doel bestand wordt gegenereerd, maar er kunnen geen gegevens in Excel worden gelezen. Dit kan gebeuren wanneer de bron SQL-tabel gegevens bevat met niet-Engelse tekens. Selecteer in Excel het tabblad gegevens ophalen en kies het CSV-bestand. Selecteer bestand oorsprong als 65001: Unicode (UTF-8) en laad gegevens.

## <a name="snapshot-issue-after-updating-snapshot-schedule"></a>Momentopname probleem na het bijwerken van het momentopname schema
Nadat de gegevens provider het momentopname schema voor de verzonden share heeft bijgewerkt, moet de gegevens verbruiker het vorige momentopname schema uitschakelen en het bijgewerkte schema opnieuw inschakelen voor de ontvangen share. 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md). 

Ga door naar de zelf studie [gegevens accepteren en ontvangen](subscribe-to-data-share.md) voor meer informatie over het ontvangen van gegevens.