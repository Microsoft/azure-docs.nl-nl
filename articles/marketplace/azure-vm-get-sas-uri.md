---
title: Een SAS-URI voor een VM-installatie kopie genereren-Azure Marketplace
description: Genereer een SAS-URI (Shared Access Signature) voor een virtuele harde schijf (VHD) in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 1/5/2021
ms.openlocfilehash: 560699296b8cae83413c36820106eedf7fef7414
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914158"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Een SAS-URI voor een VM-installatie kopie genereren

Tijdens het publicatie proces moet u een SAS-URI (Shared Access Signature) opgeven voor elke VHD die is gekoppeld aan uw plannen (voorheen Sku's genoemd). Tijdens het certificerings proces moet micro soft toegang hebben tot deze Vhd's. U voert deze URI in op het tabblad **plannen** in partner centrum.

Voor het genereren van SAS-Uri's voor uw Vhd's gelden de volgende vereisten:

- Ze bieden alleen ondersteuning voor onbeheerde Vhd's.
- Alleen lijst-en lees machtigingen zijn vereist. Geef geen toegang voor schrijven of verwijderen op.
- De duur voor toegang (verval datum) moet mini maal drie weken zijn vanaf het moment dat de SAS-URI wordt gemaakt.
- Als u wilt beveiligen tegen UTC-tijd wijzigingen, stelt u de begin datum in op één dag voor de huidige datum. Als de huidige datum bijvoorbeeld 16 juni 2020 is, selecteert u 6/15/2020.

## <a name="generate-the-sas-address"></a>Het SAS-adres genereren

Er zijn twee algemene hulpprogram ma's voor het maken van een SAS-adres (URL):

1. **Azure Storage Explorer** – beschikbaar op de Azure Portal.
2. **Azure cli** : aanbevolen voor niet-Windows-besturings systemen en geautomatiseerde of continue integratie omgevingen.

### <a name="using-tool-1-azure-storage-explorer"></a>Hulp programma 1 gebruiken: Azure Storage Explorer

1. Ga naar uw **opslag account**.
1. Open **Storage Explorer**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Venster opslag account.":::

3. Klik in de **container** met de rechter muisknop op het VHD-bestand en selecteer **hand tekening voor share toegang ophalen**.
4. Voer in het dialoog venster **Shared Access Signature** de volgende velden in:

    1. Begin tijd – machtigings begindatum voor toegang tot de virtuele harde schijf. Geef een datum op die één dag voor de huidige datum valt.
    2. Verloop tijd – verval datum van machtiging voor toegang tot de VHD. Geef een datum op die ten minste drie weken na de huidige datum valt.
    3. Machtigingen: Selecteer de machtigingen lezen en lijst.
    4. Container niveau: Schakel het selectie vakje Genereer de URI op container niveau van de Shared Access-hand tekening genereren in.

    ![Het dialoog venster Shared Access Signature.](media/vm/create-sas-uri-storage-explorer.png)

5. Selecteer **maken** om de bijbehorende SAS-URI voor deze VHD te maken.
6. Kopieer de URI en sla deze op in een tekst bestand op een veilige locatie. Deze gegenereerde SAS-URI is voor toegang op container niveau. Als u deze specifiek wilt maken, bewerkt u het tekst bestand om de naam van de VHD toe te voegen.
7. Plaats de naam van de VHD achter de vhd's-teken reeks in de SAS-URI (neem een slash op). De uiteindelijke SAS-URI moet er als volgt uitzien:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Herhaal deze stappen voor elke VHD in de plannen die u wilt publiceren.

### <a name="using-tool-2-azure-cli"></a>Hulp programma 2: Azure CLI gebruiken

1. Down load en Installeer [Microsoft Azure LC](/cli/azure/install-azure-cli)I. Er zijn versies beschikbaar voor Windows, macOS en verschillende distributies van Linux.
2. Maak een Power shell-bestand (. ps1-bestands extensie), kopieer de volgende code en sla deze lokaal op.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Bewerk het bestand om de volgende parameter waarden te gebruiken. Geef datums op in UTC-datum notatie, zoals 2020-04-01T00:00:00Z.

    - account naam: de naam van uw Azure Storage-account.
    - account sleutel: uw Azure Storage-account sleutel.
    - Start datum: de start datum van machtigingen voor toegang tot de VHD. Geef een datum op van één dag voor de huidige datum.
    - verloop datum: de verval datum van de machtiging voor de toegang tot de virtuele harde schijf. Geef een datum op die ten minste drie weken na de huidige datum valt.

    Hier volgt een voor beeld van de juiste parameter waarden (op het moment van schrijven):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Sla de wijzigingen op.
2. Gebruik een van de volgende methoden om dit script uit te voeren met beheerders bevoegdheden voor het maken van een SAS-connection string voor toegang op container niveau:

    - Voer het script uit vanaf de-console. Klik in Windows met de rechter muisknop op het script en selecteer **als administrator uitvoeren**.
    - Voer het script uit vanuit een Power shell-script editor, zoals [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). In dit scherm wordt het maken van een SAS-connection string in deze editor weer gegeven:

    [![een SAS-connection string maken in de Power shell-editor](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Kopieer de SAS-connection string en sla deze op in een tekst bestand op een veilige locatie. Bewerk deze teken reeks om de VHD-locatie gegevens toe te voegen om de definitieve SAS-URI te maken.
7. Ga in het Azure Portal naar de Blob-opslag met de VHD die is gekoppeld aan de nieuwe URI.
8. Kopieer de URL van het eind punt van de BLOB-service:

    ![De URL van het eind punt van de BLOB-service wordt gekopieerd.](media/vm/create-sas-uri-blob-endpoint.png)

9. Bewerk het tekst bestand met de SAS-connection string uit stap 6. Maak de volledige SAS-URI met de volgende indeling:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>De SAS-URI controleren

Controleer de SAS-URI voordat u deze publiceert in het partner centrum om te voor komen dat er problemen zijn met de SAS-URI na verzen ding van de aanvraag. Dit proces is optioneel, maar wordt aanbevolen.

- De URI bevat de bestands naam van de VHD-installatie kopie, inclusief de bestandsnaam extensie `.vhd` .
- `Sp=rl` wordt weer gegeven in de buurt van de URI. In deze teken reeks wordt de lees-en lijst toegang weer gegeven.
- Wanneer `sr=c` wordt weer gegeven, betekent dit dat toegang op container niveau is opgegeven.
- Kopieer en plak de URI in een browser om de BLOB te testen (u kunt de bewerking annuleren voordat de down load is voltooid).

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt, raadpleegt u [fout berichten](azure-vm-sas-failure-messages.md)van de VM-SAS.
- [Aanmelden bij het partner centrum](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Een aanbieding voor een virtuele machine maken op Azure Marketplace](azure-vm-create.md)
