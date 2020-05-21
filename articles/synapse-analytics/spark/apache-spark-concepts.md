---
title: Apache Spark ve službě Azure synapse Analytics – základní koncepty
description: Tento článek poskytuje Úvod do Apache Spark ve službě Azure synapse Analytics a v různých konceptech.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6276d198e547efec3d2e3cb88816da5e2b593aae
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644687"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark v základních konceptech služby Azure synapse Analytics

Apache Spark je paralelní procesor pro zpracování, který podporuje zpracování v paměti, aby se zvýšil výkon analytických aplikací s velkým objemem dat. Apache Spark ve službě Azure synapse Analytics je jedním z implementací Apache Spark v cloudu od Microsoftu. 

Azure synapse usnadňuje vytváření a konfiguraci možností Sparku v Azure. Azure synapse poskytuje odlišnou implementaci těchto funkcí Sparku, které jsou popsány zde.

## <a name="spark-pools-preview"></a>Fondy Sparku (Preview)

Ve Azure Portal se vytvoří fond Spark (Preview). Je definice fondu Spark, který při vytvoření instance slouží k vytvoření instance Sparku, která zpracovává data. Když se vytvoří fond Sparku, existuje jenom jako metadata; žádné prostředky nespotřebováváte, nepoužíváte ani se neúčtují. Fond Spark má řadu vlastností, které řídí charakteristiky instance Spark; Tyto vlastnosti zahrnují, ale nejsou omezené na název, velikost, chování škálování, doba do živého.

Protože se k vytváření fondů Sparku nevztahují žádné dolary ani náklady na prostředky, je možné jakékoli číslo vytvořit s libovolným počtem různých konfigurací. Oprávnění lze také použít na fondy Spark, které uživatelům umožňují mít přístup pouze k některým a jiným uživatelům.

Osvědčeným postupem je vytvořit menší fondy Sparku, které se dají použít pro vývoj a ladění a pak větší pro spouštění produkčních úloh.

Můžete si přečíst, jak vytvořit fond Spark a zobrazit všechny jeho vlastnosti. Začněte [s fondy Spark v synapse Analytics](../quickstart-create-apache-spark-pool-portal.md) .

## <a name="spark-instances"></a>Instance Spark

Instance Spark se vytvoří, když se připojíte ke fondu Spark, vytvoříte relaci a spustíte úlohu. Protože k jednomu fondu Spark může mít přístup více uživatelů, vytvoří se nová instance Spark pro každého uživatele, který se připojí. 

Když odešlete druhou úlohu, pak existující instance Spark má také kapacitu, pak existující instance zpracuje úlohu. Pokud ne a na úrovni fondu existuje kapacita, vytvoří se nová instance Spark.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1

- Vytvoříte fond Spark s názvem SP1; má pevnou velikost clusteru 20 uzlů.
- Odešlete úlohu poznámkového bloku J1, která používá 10 uzlů, vytvoří se instance Spark SI1 pro zpracování úlohy.
- Nyní odešlete další úlohu J2, která používá 10 uzlů, protože stále existuje kapacita fondu a instance, J2 se zpracovává pomocí SI1.
- Pokud J2 požádal na 11 uzlů, v SP1 nebo SI1 by se nedostala kapacita. V takovém případě, pokud J2 přichází z poznámkového bloku, úloha se odmítne. Pokud J2 přichází z úlohy služby Batch, bude zařazena do fronty.

### <a name="example-2"></a>Příklad 2

- Vytvoříte fond Spark volání SP2; má povolené automatické škálování 10 – 20 uzlů.
- Odešlete úlohu poznámkového bloku J1, která používá 10 uzlů, vytvoří se instance Spark SI1 a zpracuje úlohu.
- Nyní odešlete další úlohu J2, která používá 10 uzlů, protože ve fondu stále existuje kapacita, kterou instance automaticky roste na 20 uzlů a procesy J2.

### <a name="example-3"></a>Příklad 3

- Vytvoříte fond Spark s názvem SP1; má pevnou velikost clusteru 20 uzlů.
- Odešlete úlohu poznámkového bloku J1, která používá 10 uzlů, vytvoří se instance Spark SI1 pro zpracování úlohy.
- Jiný uživatel, U2, odešle úlohu, J3, která používá 10 uzlů, vytvoří se nová instance Spark SI2, která zpracuje úlohu.
- Nyní odešlete další úlohu J2, která používá 10 uzlů, protože ve fondu stále existuje kapacita a instance J2 je zpracována SI1.

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Dokumentace k Apache Spark](https://spark.apache.org/docs/2.4.4/)
