---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 05673885336dbfb9a70843bd0ccc4e700091ad7e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "67176704"
---
|     Foutcode     |      Foutbeschrijving     |
|--------------------|--------------------------|
|    100             | De naam van de container of share moet bestaan uit 3 tot 63 tekens.|
|    101             | De naam van de container of share mag alleen letters, cijfers of afbreekstreepjes bevatten.|
|    102             | De naam van de container of share mag alleen letters, cijfers of afbreekstreepjes bevatten.|
|    103             | De naam van de blob of het bestand bevat niet-ondersteunde tekens.|
|    104             | De naam van de blob of het bestand bevat ongeldige tekens.|
|    105             | De naam van de blob of het bestand bevat te veel segmenten (elk segment is gescheiden met een slash -/).|
|    106             | De naam van de blob of het bestand is te lang.|
|    107             | Een van de segmenten in de blob- of bestandsnaam is te lang. |
|    108             | De bestandsgrootte overschrijdt de maximale bestandsgrootte voor uploaden.    |
|    109             | De blob of het bestand is niet juist uitgelijnd.  |
|    110             | De bestandsnaam of blob met Unicode-codering is niet geldig.|
|    111             | De naam of het voorvoegsel van het bestand of de blob is een gereserveerde naam die niet wordt ondersteund (bijvoorbeeld COM1).|
|    2000            | Niet-overeenstemmende etags houdt in dat er een conflict is tussen een blok-blob in de cloud en op het apparaat. U lost dit conflict op door een van die bestanden te verwijderen: ofwel de versie in de cloud of de versie op het apparaat.    |
|    2001            | Er is een onverwacht probleem opgetreden tijdens het verwerken van een bestand nadat het bestand is geüpload.    Als deze fout zich langer dan 24 uur blijft voordoen, neemt u contact op met ondersteuning. |
|    2002            | Het bestand is al geopend in een ander proces en kan pas worden geüpload wanneer de ingang is gesloten.|
|    2003            | Het bestand kan niet worden geopend om te uploaden. Neem contact op met Microsoft Ondersteuning als u deze fout ziet.|
|    2004            | Kan geen verbinding maken met de container om gegevens naar deze container te uploaden.|
|    2005            | Kan geen verbinding maken met de container omdat de accountmachtigingen onjuist zijn of zijn verlopen. Controleer uw toegang.|
|    2006            | Kan geen gegevens uploaden naar het account omdat het account of de share is uitgeschakeld.|
|    2007            | Kan geen verbinding maken met de container omdat de accountmachtigingen onjuist zijn of zijn verlopen. Controleer uw toegang.|
|    2008            | Kan geen nieuwe gegevens toevoegen omdat de container vol is. Controleer de Azure-specificaties voor de ondersteunde containergrootten op basis van type. Azure File bijvoorbeeld biedt ondersteuning voor een maximale bestandsgrootte van slechts 5 TB.|
|    2009            | Kan de gegevens niet uploaden omdat de container die is gekoppeld aan de share niet bestaat.|    
|    2997            | Er is een onverwachte fout opgetreden. Dit is een tijdelijke fout die automatisch wordt opgelost.|
|    2998            | Er is een onverwachte fout opgetreden. Het is mogelijk dat de fout automatisch wordt opgelost, maar als het probleem zich langer dan 24 uur blijft voordoen, neem dan contact op met Microsoft Ondersteuning.|
|    16000           | Kan dit bestand niet downloaden.|
|    16001           | Kan dit bestand niet downloaden omdat het al op uw lokale systeem bestaat.|
|    16002           |Kan dit bestand niet vernieuwen omdat het niet volledig is geüpload.|

