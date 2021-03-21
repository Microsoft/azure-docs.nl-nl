---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: 324fde5198fe3469e3979cf5cd102715118a49aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467283"
---
Form Recognizer werkt met invoerdocumenten die aan de volgende vereisten voldoen:

* De indeling moet JPEG, PNG, PDF (tekst of gescand) of TIFF zijn. PDF-bestanden met ingesloten tekst hebben de voorkeur omdat hierbij geen fouten optreden met locatie en extractie van tekens.
* Grootte van bestand moet 50 MB of minder zijn.
* De afmetingen van afbeeldingen moeten tussen 50 x 50 en 10000 x 10000 pixels liggen.
* PDF-afmetingen mogen maximaal 17 x 17 inch zijn, wat overeenkomt met de papierformaten Legal of A3 en kleiner.
* Voor PDF en TIFF worden alleen de eerste 200 pagina's verwerkt (met een abonnement in de prijscategorie Gratis worden alleen de eerste twee pagina's verwerkt).
* De totale grootte van de set met trainingsgegevens mag niet meer bedragen dan 500 pagina's.
* Als uw PDF's met een wachtwoord zijn vergrendeld, moet u de vergrendeling verwijderen voordat u ze verzendt.
* Als formulieren worden gescand van papieren documenten, moeten de formulieren scans van hoge kwaliteit zijn.
* Tekst moet het Latijnse alfabet (Engelse tekens) gebruiken.
* De service kan alleen zonder tussenkomst leren (zonder gelabelde gegevens) als de gegevens sleutels en waarden bevatten.
* De service kan alleen zonder tussenkomst leren (zonder gelabelde gegevens) als er boven of links van de waarden sleutels worden weergegeven. Deze mogen niet onder of rechts van de waarden worden weergegeven.
