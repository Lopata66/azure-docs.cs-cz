---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: dfb76a14f7e177211e5e8891005544e20f19d3f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483637"
---
Můžete se účtuje na základě vašeho využití účtu úložiště Azure Storage. Všechny objekty v rámci účtu úložiště se fakturují společně jako skupina. 

Náklady na úložiště se počítají podle následujících faktorů: oblast/umístění, typ účtu, úroveň přístupu, kapacita úložiště, schématu replikace, transakce služby storage a sazbách za odchozí data.

* **Oblast** odkazuje na geografické oblasti, ve kterém se účet nachází.
* **Typ účtu** odkazuje na typ účtu úložiště, kterou používáte. 
* **Úroveň přístupu** odkazuje se vzorem využití dat, kterou jste zadali pro obecné účely verze 2 nebo účtu služby Blob storage.
* Úložiště **kapacity** odkazuje na kolik svého účtu úložiště využíváte se ukládání dat pomocí.
* **Replikace** udává, kolik kopií vašich dat se spravuje najednou a v jakých umístěních.
* **Transakce** najdete všechny operace čtení a zápisu do služby Azure Storage.
* **Odchozí přenos dat** odkazuje na všechna data přenesená z oblasti Azure. Když k datům v účtu úložiště přistupuje aplikace, která neběží ve stejné oblasti, budou se vám účtovat odchozí data. Informace o použití skupin prostředků ke skupině vaše data a služby ve stejné oblasti a omezit poplatky za výchozí přenos dat najdete v tématu [co je skupina prostředků Azure?](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group). 

Na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) najdete podrobné informace o cenách na základě typu účtu, kapacity úložiště, replikace a transakcí. Na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/) najdete podrobné informace o sazbách za odchozí data. Představu o nákladech můžete získat v [Cenové kalkulačce pro Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management).

