---
title: AVERE vFXT-opslag configureren-Azure
description: Meer informatie over het toevoegen van een back-end-opslag systeem voor een cluster in avere vFXT voor Azure. Als u een Azure Blob-container met het cluster hebt gemaakt, kunt u deze gebruiken.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001521"
---
# <a name="configure-storage"></a>Opslag configureren

Met deze stap stelt u een back-end-opslag systeem in voor uw vFXT-cluster.

> [!TIP]
> Als u een nieuwe Azure Blob-container hebt gemaakt samen met het avere vFXT-cluster, is die container al geconfigureerd en klaar voor gebruik.

Volg deze instructies als u geen nieuwe BLOB-container hebt gemaakt met uw cluster of als u een extra hardware-of cloud-gebaseerd opslag systeem wilt toevoegen.

Er zijn twee hoofd taken:

1. [Maak een kern bestand](#create-a-core-filer)dat uw vFXT-cluster verbindt met een bestaand opslag systeem of een Azure Storage-account container.

1. [Maak een naam ruimte koppeling](#create-a-junction)die het pad definieert dat clients zullen koppelen.

In deze stappen wordt gebruikgemaakt van het configuratie scherm van AVERE. Lees [toegang tot het vFXT-cluster](avere-vfxt-cluster-gui.md) voor meer informatie over het gebruik ervan.

## <a name="create-a-core-filer"></a>Een kern bestand maken

' Kern bestand ' is een vFXT-term voor een back-end-opslag systeem. De opslag kan een NAS-apparaat zijn zoals NetApp of Isilon, of het kan een object archief in de Cloud zijn. Meer informatie over kern bestanden vindt u in de [hand leiding avere cluster Settings (Engelstalig](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)).

Als u een kern bestand wilt toevoegen, kiest u een van de twee belangrijkste typen kern bestanden:

* [NAS core-bestand](#nas-core-filer) -beschrijft hoe een NAS-kern bestand moet worden toegevoegd
* [Azure Storage Cloud core filer](#azure-blob-storage-cloud-core-filer) : beschrijft hoe u een Azure Blob Storage-container toevoegt als een Cloud kern bestand

### <a name="nas-core-filer"></a>NAS-kern bestand

Een NAS core-bestand kan een on-premises NetApp-of Isilon-apparaat zijn of een NAS-eind punt in de Cloud. Het opslag systeem moet een betrouw bare snelle verbinding hebben met het avere vFXT-cluster, bijvoorbeeld een 1 Gbps ExpressRoute-verbinding (geen VPN), en moet het cluster hoofd toegang geven tot de NAS-exports die worden gebruikt.

Voer de volgende stappen uit om een NAS-kern bestand toe te voegen:

1. Klik in het configuratie scherm van AVERE op het tabblad **instellingen** bovenaan.

1. Klik op **core filer**  >  **kern bestanden beheren** aan de linkerkant.

1. Klik op **Create**.

   ![Scherm afbeelding van de pagina nieuwe kern bestand toevoegen met een cursor boven de knop maken](media/avere-vfxt-add-core-filer-start.png)

1. Vul de vereiste gegevens in de wizard in:

   * Geef uw kern bestand een naam.
   * Geef een Fully Qualified Domain Name (FQDN) op, indien beschikbaar. Geef anders een IP-adres of een hostnaam op die wordt omgezet in uw kern bestand.
   * Kies uw bestands groep in de lijst. Als u dit niet zeker weet, kiest u **Overige**.

     ![Scherm afbeelding van de pagina nieuwe kern bestand toevoegen met de naam van de kern bestand en de Fully Qualified Domain Name](media/avere-vfxt-add-core-filer.png)
  
   * Klik op **volgende** en kies een cache beleid.
   * Klik op **bestand toevoegen**.
   * Zie [een nieuwe NAS-kern bestand toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) in de avere-hand leiding voor cluster instellingen voor meer gedetailleerde informatie.

Ga vervolgens verder met het [maken van een verbinding](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage Cloud-kern bestand

Als u Azure Blob Storage wilt gebruiken als de back-end-opslag van uw vFXT-cluster, hebt u een lege container nodig om toe te voegen als een kern bestand.

Voor het toevoegen van Blob-opslag aan uw cluster zijn de volgende taken vereist:

* Een opslag account maken (stap 1, hieronder)
* Een lege BLOB-container maken (stap 2-3)
* Voeg de toegangs sleutel voor opslag toe als een Cloud referentie voor het vFXT-cluster (stap 4-6)
* Voeg de BLOB-container toe als een kern bestand voor het vFXT-cluster (stap 7-9)
* Maak een naam ruimte koppeling die clients gebruiken om de kern bestand te koppelen ([een verbinding maken](#create-a-junction), hetzelfde voor hardware-en Cloud opslag)

> [!TIP]
> Als u een nieuwe BLOB-container maakt wanneer u een avere vFXT voor Azure-cluster maakt, wordt de container door de implementatie sjabloon automatisch als een kern bestand geconfigureerd. (Dit geldt ook als u het script voor maken gebruikt dat op aanvraag beschikbaar is.) U hoeft de kern bestand niet later opnieuw te configureren.
>
> Het hulp programma voor het maken van clusters voert deze configuratie taken voor u uit:
>
> * Hiermee maakt u een nieuwe BLOB-container in het gegeven opslag account
> * Hiermee wordt de container gedefinieerd als een kern bestand
> * Maakt een naam ruimte koppeling naar de container
> * Hiermee maakt u een opslag service-eind punt in het virtuele netwerk van het cluster

Voer de volgende stappen uit om Blob-opslag toe te voegen na het maken van het cluster.

1. Maak een opslag account voor algemeen gebruik v2 met de volgende instellingen:

   * **Abonnement** : hetzelfde als het vFXT-cluster
   * **Resource groep** : hetzelfde als de vFXT-cluster groep (optioneel)
   * **Locatie** : hetzelfde als het vFXT-cluster
   * **Prestaties** -standaard (Premium-opslag wordt niet ondersteund)
   * **Type account** -algemeen gebruik v2 (StorageV2)
   * **Replicatie** -lokaal redundante opslag (LRS)
   * **Access-laag** -Hot
   * **Beveiligde overdracht vereist** : Schakel deze optie uit (niet-standaard waarde)
   * **Virtuele netwerken** -niet vereist

   U kunt de Azure Portal gebruiken of klikken op de knop implementeren naar Azure hieronder.

   [![knop voor het maken van een opslag account](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Nadat het account is gemaakt, bladert u naar de pagina voor het opslag account.

   ![Nieuw opslag account in Azure Portal](media/avere-vfxt-new-storage-acct.png)

1. Een nieuwe BLOB-container maken: Klik op de pagina overzicht op **containers** en klik vervolgens op **+ container**. Gebruik een wille keurige container naam en zorg ervoor dat de toegang is ingesteld op **privé**.

   ![De pagina opslag-blobs met de knop + container omcirkeld en er wordt een nieuwe container gemaakt op een pop-uppagina](media/avere-vfxt-new-blob.png)

1. U kunt de sleutel van het Azure Storage-account ophalen door te klikken op **toegangs sleutels** onder **instellingen**. Kopieer een van de sleutels die worden gegeven.

   ![Azure Portal GUI voor het kopiëren van de sleutel](media/avere-vfxt-copy-storage-key.png)

1. Open het configuratie scherm van AVERE voor uw cluster. Klik op **instellingen** en open vervolgens  >  de referenties van de cluster-**Cloud** in het navigatie deel venster aan de linkerkant. Klik op de pagina Cloud referenties op **referentie toevoegen**.

   ![Klik op de knop referentie toevoegen op de pagina configuratie van Cloud referenties](media/avere-vfxt-new-credential-button.png)

1. Vul de volgende gegevens in om een referentie te maken voor de Cloud core Filer:

   | Veld | Waarde |
   | --- | --- |
   | Referentienaam | een beschrijvende naam |
   | Servicetype | (Selecteer Azure Storage toegangs sleutel) |
   | Tenant | naam van opslagaccount |
   | Abonnement | subscription ID |
   | Toegangs sleutel voor opslag | Sleutel van het Azure-opslag account (gekopieerd in de vorige stap) |

   Klik op **Submit**

   ![Het formulier voor de Cloud referentie in het configuratie scherm van AVERE is voltooid](media/avere-vfxt-new-credential-submit.png)

1. Maak vervolgens de kern bestand. Klik aan de linkerkant van het configuratie scherm van AVERE op **kern bestandiseren**  >   **kern bestanden beheren**.

1. Klik op de knop **maken** op de pagina instellingen voor **kern bestanden beheren** .

1. Vul de wizard in:

   * Selecteer de **Cloud** van het type filer.
   * Geef de nieuwe kern bestand een naam en klik op **volgende**.
   * Accepteer het standaard cache beleid en ga door naar de derde pagina.
   * Kies in **Service type** de optie **Azure Storage**.
   * Kies de referentie die u eerder hebt gemaakt.
   * **Bucket inhoud** instellen op **leeg**
   * **Certificaat verificatie** wijzigen in **uitgeschakeld**
   * **Compressie modus** wijzigen in **geen**
   * Klik op **Volgende**.
   * Voer op de vierde pagina de naam van de container in **Bucket naam** in als *storage_account_name* / *container_name*.
   * U kunt ook het **versleutelings type** instellen op **geen**.  Azure Storage is standaard versleuteld.
   * Klik op **bestand toevoegen**.

   Lees [een nieuwe Cloud core filer toevoegen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) in de avere-cluster configuratie handleiding voor meer gedetailleerde informatie.

De pagina wordt vernieuwd of u kunt de pagina vernieuwen om uw nieuwe kern bestand weer te geven.

Vervolgens moet u [een verbinding maken](#create-a-junction).

## <a name="create-a-junction"></a>Een verbinding maken

Een verbinding is een pad dat u voor clients maakt. Clients koppelen het pad en ontvangen op de bestemming die u kiest.

U kunt bijvoorbeeld maken om toe te `/vfxt/files` wijzen aan uw NetApp core filer `/vol0/data` export en de `/project/resources` submap.

Meer informatie over koppelingen vindt u in de [sectie naam ruimte van de avere-cluster configuratie handleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Volg deze stappen in de interface van het configuratie scherm van AVERE:

* Klik   >  in de linkerbovenhoek op vserver-**naam ruimte** .
* Geef een naam ruimte-pad op dat begint met/(slash), zoals ``/vfxt/data`` .
* Kies uw kern bestand.
* Kies de kern bestands export.
* Klik op **Volgende**.

  ![Scherm opname van de pagina ' nieuwe verbinding toevoegen ' met de velden die zijn voltooid voor Junction, kern bestand en exporteren](media/avere-vfxt-add-junction.png)

De verbinding wordt na een paar seconden weer gegeven. Maak indien nodig extra-koppelingen.

Nadat de verbinding tot stand is gebracht, gebruiken clients het pad van de naam ruimte voor toegang tot de bestanden van het opslag systeem.

## <a name="next-steps"></a>Volgende stappen

* [Het Avere vFXT-cluster koppelen](avere-vfxt-mount-clients.md)
* Informatie over efficiënte manieren om [gegevens naar een nieuwe BLOB-container te verplaatsen](avere-vfxt-data-ingest.md)
