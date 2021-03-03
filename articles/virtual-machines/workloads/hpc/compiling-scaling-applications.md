---
title: HPC-toepassingen schalen-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het schalen van HPC-toepassingen op Azure-Vm's.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d560b261e058d01040616f3c59ede60e5986c672
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666973"
---
# <a name="scaling-hpc-applications"></a>HPC-toepassingen schalen

Optimale schaal-en schaal prestaties van HPC-toepassingen op Azure vereist het afstemmen van prestaties en optimalisatie experimenten voor de specifieke werk belasting. Deze sectie en de VM-reeks-specifieke pagina's bieden algemene richt lijnen voor het schalen van uw toepassingen.

## <a name="compiling-applications"></a>Toepassingen compileren

Hoewel het niet nodig is om toepassingen met de juiste optimalisatie vlaggen te compileren, hebt u de beste prestaties op virtuele machines met HB en HC-serie.

### <a name="amd-optimizing-cc-compiler"></a>AMD-compiler voor het optimaliseren van C/C++

Het AOCC-Compileer systeem (AMD Optimization C/C++ compiler) biedt een hoog niveau aan geavanceerde optimalisaties, multithreading en processor ondersteuning, waaronder globale optimalisatie, vectorization, Inter-procedureve analyses, lussen van trans formaties en het genereren van code. AOCC compiler binaire bestanden zijn geschikt voor Linux-systemen met de glibc-versie 2,17 (GNU C Library) en hoger. De compiler suite bestaat uit een C/C++-compiler (clang), een Fortran-compiler (FLANG) en een Fortran front-end naar clang (draak ei).

### <a name="clang"></a>Clang

Clang is een C-, C++-en objectief-C-compiler verwerking, parsering, optimalisatie, code generatie, assembly en koppeling. Clang biedt ondersteuning  `-march=znver1` voor de vlag voor het genereren en afstemmen van de beste code voor de x86-architectuur op basis van Zen van AMD.

### <a name="flang"></a>FLANG

De FLANG-compiler is een recente toevoeging aan de AOCC-suite (toegevoegde versie van april 2018) en is momenteel beschikbaar voor ontwikkel aars om te downloaden en te testen. Op basis van Fortran 2008 biedt AMD een uitbrei ding van de GitHub-versie van FLANG ( https://github.com/flang-compiler/flang) . De FLANG-compiler ondersteunt alle clang-compiler opties en een extra aantal FLANG-specifieke compiler opties.

### <a name="dragonegg"></a>DragonEgg

DragonEgg is een gcc-invoeg toepassing waarmee de optimalisaties en code generators van GCC worden vervangen door die van het LLVM-project. DragonEgg die wordt geleverd met AOCC werkt met gcc-4.8. x, is getest op x86-32/x86-64 doelen en is op diverse Linux-platforms gebruikt.

GFortran is de daad werkelijke frontend voor Fortran-Program ma's die verantwoordelijk zijn voor de voor verwerking, het parseren en de semantische analyse van de GCC GIMPLE Intermediate-weer gave (IR). DragonEgg is een GNU-invoeg toepassing die is aangesloten op GFortran-compilatie stroom. Hiermee wordt de API voor de GNU-invoeg toepassing geïmplementeerd. Met de architectuur van de invoeg toepassing wordt DragonEgg het compiler stuur programma, waardoor de verschillende fases van de compilatie worden gebevorderen.  Na het volgen van de Download-en installatie-instructies kan draak ei worden aangeroepen met behulp van: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>BGA-compiler
BGA naar Community Edition ver. 17 is bevestigd om met AMD EPYC te werken. Een door BGA gecompileerde versie van STREAM levert volledige geheugen bandbreedte van het platform. De nieuwere Community Edition 18,10 (nov 2018) moet ook goed werken. Hieronder ziet u de voor beeld-CLI om optimaal te compileren met de Intel-compiler:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel-compiler
Intel-compiler-ver. 18 is bevestigd om met AMD EPYC te werken. Hieronder ziet u de voor beeld-CLI om optimaal te compileren met de Intel-compiler.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-compiler 
Voor HPC adviseert AMD GCC compiler 7,3 of nieuwer. Oudere versies, zoals 4.8.5 die deel uitmaken van RHEL/CentOS 7,4, worden niet aanbevolen. GCC 7,3 en nieuwere versies leveren aanzienlijk hogere prestaties op HPL-, HPCG-en DGEMM-tests.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Toepassingen vergroten/verkleinen 

De volgende suggesties zijn van toepassing op optimale efficiëntie, prestaties en consistentie van toepassingen:

* Pincodes voor het vastmaken van processen aan kern geheugens van 0-59 (in plaats van een automatische balans benadering). 
* Binding via Numa/core/HwThread is beter dan de standaard binding.
* Gebruik voor hybride parallelle toepassingen (OpenMP + MPI) 4 threads en 1 MPI Rank per CCX.
* Voor zuivere MPI-toepassingen kunt u experimenteren met 1-4 MPI Ranks per CCX voor optimale prestaties.
* Sommige toepassingen met extreme gevoeligheid voor geheugen bandbreedte kunnen profiteren van een beperkt aantal kernen per CCX. Voor deze toepassingen kan het gebruik van 3 of 2 kernen per CCX ertoe leiden dat geheugen bandbreedte conflicten worden verminderd en hogere prestaties of een consistente schaal baarheid worden gerealiseerd. Met name MPI Allreduce kan van deze voor delen.
* Voor grote grotere schaal runs wordt aanbevolen UD-of Hybrid RC + UD-trans porten te gebruiken. Veel MPI-bibliotheken/runtime-bibliotheken doen dit intern (zoals UCX of MVAPICH2). Controleer uw transport configuraties voor grootschalige uitvoeringen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HPC](/azure/architecture/topics/high-performance-computing/) op Azure.
