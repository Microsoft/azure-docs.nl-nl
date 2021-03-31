---
title: Een Azure-bestandsshare koppelen via SMB met macOS | Microsoft Docs
description: Meer informatie over het koppelen van een Azure-bestands share over SMB met macOS met Finder of Terminal. Azure Files  is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326062"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Een Azure-bestandsshare koppelen via SMB met macOS
[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestands shares kunnen worden gekoppeld met het standaard protocol SMB 3 van de industrie door macOS High Sierra 10.13 +. Dit artikel behandelt twee verschillende manieren om een Azure-bestandsshare te koppelen op macOS: met de Finder-gebruikersinterface en met Terminal.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Vereisten voor het koppelen van een Azure-bestandsshare op macOS
* **Naam van opslag account**: als u een Azure-bestands share wilt koppelen, hebt u de naam van het opslag account nodig.

* **Sleutel van het opslag account**: voor het koppelen van een Azure-bestands share hebt u de primaire (of secundaire) opslag sleutel nodig. SAS-sleutels worden momenteel niet ondersteund voor koppelen.

* **Zorg ervoor dat poort 445 is geopend**: SMB communiceert via TCP-poort 445. Controleer op de clientcomputer (Mac) of uw firewall TCP-poort 445 niet blokkeert.

## <a name="mount-an-azure-file-share-via-finder"></a>Een Azure-bestandsshare koppelen via Finder
1. **Open Finder**: Finder is standaard geopend op Mac OS, maar u kunt controleren of het de geselecteerde toepassing is door te klikken op het 'gezichtspictogram van Mac OS' op de dock:  
    ![Het gezichtspictogram van Mac OS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecteer ' verbinding maken met server ' in het menu ' go '**: gebruik het UNC-pad in de vereisten, converteer het begin dubbele back slash ( `\\` ) naar `smb://` en alle andere backslashes () `\` om schuine strepen () door te sturen `/` . De link moet er als volgt uitzien: ![het dialoogvenster 'Verbinden met server'](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Gebruik de naam en sleutel van het opslagaccount wanneer u wordt gevraagd om een gebruikersnaam en wachtwoord**: wanneer u klikt op 'Verbinden' in het dialoogvenster 'Verbinden met server', wordt u gevraagd om de gebruikersnaam en het wachtwoord (hier wordt uw macOS-gebruikersnaam automatisch ingevuld). U hebt de mogelijkheid om de naam/sleutel van het opslagaccount in uw macOS-sleutelhanger op te slaan.

4. **Gebruik de Azure-bestands share naar wens**: nadat u de share naam en de sleutel van het opslag account hebt vervangen door voor de gebruikers naam en het wacht woord, wordt de share gekoppeld. U kunt deze gebruiken zoals u een lokale map/bestandsshare zou gebruiken. Zo kunt u bestanden naar de bestandsshare slepen en neerzetten:

    ![Een momentopname van een gekoppelde Azure-bestandsshare](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Een Azure-bestandsshare koppelen via Terminal
1. Vervang `<storage-account-name>` , `<storage-account-key>` en door `<share-name>` de juiste waarden voor uw omgeving.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Gebruik de Azure-bestands share naar wens**: de Azure-bestands share wordt gekoppeld op het koppel punt dat is opgegeven met de vorige opdracht.  

    ![Een momentopname van de gekoppelde Azure-bestandsshare](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Volgende stappen
* [Uw Mac verbinden met gedeelde computers en servers-Apple-ondersteuning](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)