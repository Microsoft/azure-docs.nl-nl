---
title: Problemen met Azure Data Share oplossen
description: Meer informatie over het oplossen van problemen met uitnodigingen en fouten bij het maken of ontvangen van gegevens shares in azure data share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964504"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Veelvoorkomende problemen in azure data share oplossen 

In dit artikel wordt uitgelegd hoe u veelvoorkomende problemen in azure data share oplost. 

## <a name="azure-data-share-invitations"></a>Uitnodigingen voor Azure-gegevens delen 

In sommige gevallen, wanneer nieuwe gebruikers uitnodiging voor een e-mail uitnodiging **accepteren** selecteren, zien ze mogelijk een lege lijst met uitnodigingen. 

:::image type="content" source="media/no-invites.png" alt-text="Scherm opname met een lege lijst met uitnodigingen.":::

Dit probleem kan een van de volgende oorzaken hebben:

* **De Azure data share-service is niet geregistreerd als resource provider van een Azure-abonnement in de Azure-Tenant.** Dit probleem treedt op wanneer uw Azure-Tenant geen resource voor gegevens delen heeft. 

    Wanneer u een Azure Data Share-resource maakt, wordt de resourceprovider automatisch geregistreerd in uw Azure-abonnement. U kunt de data share-service hand matig registreren met behulp van de volgende stappen. U hebt de [rol Inzender](../role-based-access-control/built-in-roles.md#contributor) voor het Azure-abonnement nodig om deze stappen uit te voeren. 

    1. Ga in de Azure-portal naar **Abonnementen**.
    1. Selecteer het abonnement dat u wilt gebruiken om de Azure-gegevens share bron te maken.
    1. **Resource providers** selecteren.
    1. Zoek naar **micro soft. DataShare**.
    1. Selecteer **Registreren**.

* **De uitnodiging wordt verzonden naar uw e-mail alias in plaats van uw e-mail adres voor aanmelding bij Azure.** Als u de Azure data share-service al hebt geregistreerd of een gegevens share bron hebt gemaakt in de Azure-Tenant, maar u de uitnodiging nog steeds niet ziet, wordt uw e-mail alias mogelijk vermeld als de ontvanger. Neem contact op met uw gegevens provider en zorg ervoor dat de uitnodiging wordt verzonden naar uw e-mail adres voor Azure-aanmelding en niet uw e-mail alias.

* **De uitnodiging is al geaccepteerd.** Via de koppeling in het e-mail bericht gaat u naar de pagina **uitnodigingen voor gegevens delen** in de Azure Portal. Op deze pagina worden alleen uitnodigingen in behandeling weer gegeven. Geaccepteerde uitnodigingen worden niet weer gegeven op de pagina. Als u ontvangen shares wilt weer geven en de Azure Data Explorer-doel instelling wilt configureren, gaat u naar de gegevens share resource die u hebt gebruikt om de uitnodiging te accepteren.

## <a name="creating-and-receiving-shares"></a>Shares maken en ontvangen

De volgende fouten kunnen worden weer gegeven wanneer u een nieuwe share maakt, gegevens sets toevoegt of gegevens sets kaart:

* Kan geen gegevens sets toevoegen.
* Kan geen gegevens sets toewijzen.
* Kan de resource x-toegang tot de gegevens share niet aan y toewijzen.
* U hebt niet de juiste machtigingen voor x.
* Er kunnen geen schrijf machtigingen voor het Azure data share-account worden toegevoegd aan een of meer van de geselecteerde resources.

U ziet mogelijk een van deze fouten als u onvoldoende machtigingen hebt voor de Azure-gegevens opslag. Zie voor meer informatie [rollen en vereisten](concepts-roles-permissions.md). 

U hebt de schrijf machtiging nodig om gegevens te delen of te ontvangen van een Azure-gegevens archief. Deze machtiging maakt meestal deel uit van de rol Inzender. 

Als u gegevens deelt of gegevens van de Azure-gegevens opslag voor het eerst wilt ontvangen, hebt u ook de machtigingen *micro soft. Authorization/roltoewijzingen/schrijven* nodig. Deze machtiging maakt meestal deel uit van de rol eigenaar. Zelfs als u de Azure-gegevens opslag resource hebt gemaakt, bent u niet noodzakelijkerwijs de eigenaar van de resource. 

Wanneer u de juiste machtigingen hebt, zorgt de Azure data share-service er automatisch voor dat de beheerde identiteit van de gegevens share bron wordt geopend voor toegang tot het gegevens archief. Dit proces kan enkele minuten duren. Probeer het over enkele minuten opnieuw als u problemen ondervindt door deze vertraging.

Voor delen op basis van SQL zijn extra machtigingen vereist. Zie [delen vanuit SQL-bronnen](how-to-share-from-sql.md)voor meer informatie over vereisten.

## <a name="snapshots"></a>Momentopnamen
Een moment opname kan om verschillende redenen mislukken. Open een gedetailleerd fout bericht door de begin tijd van de moment opname en vervolgens de status van elke gegevensset te selecteren. 

Moment opnamen mislukken meestal om de volgende redenen:

* Gegevens share heeft geen machtiging voor het lezen van het brongegevens archief of het schrijven naar het doel gegevens archief. Zie voor meer informatie [rollen en vereisten](concepts-roles-permissions.md). Als u voor het eerst een moment opname maakt, kan het zijn dat de gegevens share bron enkele minuten nodig heeft om toegang te krijgen tot de Azure-gegevens opslag. Probeer het na een paar minuten opnieuw.
* De verbinding van de gegevens share met het bron gegevens archief of het doel gegevens archief wordt geblokkeerd door een firewall.
* Er is een gedeelde gegevensset, brongegevens opslag of doel gegevens opslag verwijderd.

Voor opslag accounts kan een moment opname mislukken omdat een bestand wordt bijgewerkt in de bron terwijl de moment opname plaatsvindt. Als gevolg hiervan kan een 0-byte-bestand worden weer gegeven bij het doel. Na de update op de bron moeten moment opnamen slagen.

Voor SQL-bronnen kan een moment opname om de volgende redenen mislukken:

* Het bron-SQL-script of SQL-doel script dat de machtiging voor gegevens delen verleent, is niet uitgevoerd. Of voor Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL Data Warehouse), wordt het script uitgevoerd met behulp van SQL-verificatie in plaats van Azure Active Directory-verificatie.  
* Het brongegevens archief of het doel-SQL-gegevens archief is onderbroken.
* Het momentopname proces of het doel gegevens archief biedt geen ondersteuning voor SQL-gegevens typen. Zie [delen vanuit SQL-bronnen](how-to-share-from-sql.md#supported-data-types)voor meer informatie.
* De brongegevens opslag of het doel-SQL-gegevens archief is vergrendeld door andere processen. Deze gegevens archieven worden niet vergrendeld door de Azure-gegevens share. Maar bestaande vergren delingen op deze gegevens archieven kunnen ervoor zorgen dat een moment opname mislukt.
* Er wordt naar de doel-SQL-tabel verwezen door een foreign key-beperking. Als een doel tabel in een moment opname dezelfde naam heeft als een tabel in de bron gegevens, wordt de tabel door Azure data share neergezet en wordt er een nieuwe tabel gemaakt. Als er wordt verwezen naar de doel-SQL-tabel met een foreign key-beperking, kan de tabel niet worden verwijderd.
* Er wordt een CSV-doel bestand gegenereerd, maar de gegevens kunnen niet worden gelezen in Excel. Dit probleem kan worden weer gegeven wanneer de SQL-bron tabel gegevens bevat die niet-Engelse tekens bevatten. Selecteer in Excel het tabblad **gegevens ophalen** en kies het CSV-bestand. Selecteer de bestands oorsprong **65001: Unicode (UTF-8)** en laad vervolgens de gegevens.

## <a name="updated-snapshot-schedules"></a>Bijgewerkte planningen voor moment opnamen
Nadat de gegevens provider het momentopname schema voor de verzonden share heeft bijgewerkt, moet de gegevens verbruiker het vorige momentopname schema uitschakelen. Schakel vervolgens het bijgewerkte schema voor de moment opname in voor de ontvangen share. 

## <a name="next-steps"></a>Volgende stappen

Als u wilt weten hoe u gegevens kunt delen, gaat u verder met de zelf studie [gegevens delen](share-your-data.md) . 

Ga door naar de zelf studie [gegevens accepteren en ontvangen](subscribe-to-data-share.md) voor meer informatie over het ontvangen van gegevens.