---
title: Strategie přístupu k datům
description: Azure Data Factory teď podporuje rozsahy statických IP adres.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 0b966b10c5bbc7bb90a4226d94dda8b75e25c3af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84247474"
---
# <a name="data-access-strategies"></a>Strategie přístupu k datům

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zásadním cílem zabezpečení organizace je chránit svá úložiště dat před náhodným přístupem přes Internet, může se jednat o místní nebo cloudové nebo SaaS úložiště dat. 

Cloudové úložiště dat obvykle řídí přístup pomocí následujících mechanismů:
* Pravidla brány firewall, která omezují připojení podle IP adresy
* Mechanismus ověřování, který vyžaduje, aby uživatelé prokáže svoji identitu
* Autorizační mechanismy, které omezují uživatelům konkrétní akce a data

> [!TIP]
> Díky [zavedení rozsahu statických IP adres](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)teď můžete u konkrétní oblasti Azure Integration runtime povolit seznam ROZSAHů IP adres, abyste se ujistili, že není nutné povolit všechny IP adresy Azure v cloudových úložištích dat. Tímto způsobem můžete omezit IP adresy, které mají povolený přístup k úložištím dat.

> [!NOTE] 
> Rozsahy IP adres jsou blokované pro Azure Integration runtime a v tuto chvíli se používají jenom pro přesun dat, kanály a externí aktivity. Tok dat nyní nepoužívá tyto rozsahy IP adres. 

To by mělo fungovat v mnoha scénářích a máme na to, že je žádoucí jedinečnou statickou IP adresu na prostředí Integration runtime, ale to by nebylo možné pomocí Azure Integration Runtime aktuálně, což je bez serveru. V případě potřeby můžete kdykoli nastavit Integration Runtime v místním prostředí a použít k němu statickou IP adresu. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Strategie přístupu k datům prostřednictvím Azure Data Factory

* **[Trusted Service](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** -Azure Storage (Blob, adls Gen2) podporuje konfiguraci brány firewall, která umožňuje pro zabezpečený přístup k účtu úložiště vybrat důvěryhodné služby platformy Azure. Trusted Services vynutily ověřování spravované identity, které zajišťuje, aby se žádná jiná továrna dat nemohla připojit k tomuto úložišti, pokud to neumožňuje použití spravované identity. Další podrobnosti najdete v **[tomto blogu](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)**. To je proto extrémně zabezpečené a doporučuje se. 
* **Jedinečná statická IP adresa** – budete muset nastavit prostředí Integration runtime v místním prostředí, abyste získali statickou IP adresu pro Data Factory konektory. Tento mechanismus zajišťuje, že můžete blokovat přístup ze všech ostatních IP adres. 
* **[Rozsah statických IP](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** adres – můžete použít IP adresy Azure Integration runtime, abyste je mohli zobrazit v úložišti (například S3, Salesforce atd.). V takovém případě omezuje IP adresy, které se můžou připojit k úložištím dat, ale také spoléhá na pravidla ověřování a autorizace.
* **[Tag služby](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** – značka služby představuje skupinu předpon IP adres z dané služby Azure (například Azure Data Factory). Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. To je užitečné v případě, že je povolený přístup k datům v úložištích IaaS hostovaných daty v Virtual Network.
* **Povolení služeb Azure** – některé služby umožňují, aby se k němu připojovaly všechny služby Azure v případě, že zvolíte tuto možnost. 

Další informace o podporovaných mechanismech zabezpečení sítě v úložištích dat v Azure Integration Runtime a v místním prostředí Integration Runtime najdete níže v části dvě tabulky.  
* **Azure Integration Runtime**

    | Úložiště dat                  | Podporované mechanismy zabezpečení sítě v úložištích dat         | Důvěryhodná služba     | Rozsah statických IP adres | Značky služeb | Povolení služeb Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|
    | Úložiště dat Azure PaaS       | Azure Cosmos DB                                             | -                   | Ano             | -            | Yes                  |
    |                              | Průzkumník dat Azure                                         | -                   | Ano*            | Ano*         | -                    |
    |                              | Azure Data Lake Gen1                                        | -                   | Ano             | -            | Yes                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL               | -                   | Ano             | -            | Yes                  |
    |                              | Azure File Storage                                          | -                   | Yes             | -            | .                    |
    |                              | Azure Storage (blog, ADLS Gen2)                             | Ano (pouze ověřování MSI) | Yes             | -            | .                    |
    |                              | Azure SQL DB, SQL DW (synapse Analytics), SQL ml          | -                   | Ano             | -            | Yes                  |
    |                              | Azure Key Vault (pro načtení tajných klíčů/připojovacího řetězce) | Ano                 | Yes             | -            | -                    |
    | Jiná úložiště dat PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage atd.            | -                   | Yes             | -            | -                    |
    | LaaS Azure                   | SQL Server, Oracle atd.                                  | -                   | Ano             | Yes          | -                    |
    | Místní laaS              | SQL Server, Oracle atd.                                  | -                   | Yes             | -            | -                    |
    
    **Dá se použít jenom v případě, že je virtuální síť ve službě Azure Průzkumník dat vložená a rozsah IP adres se dá použít na NSG/bráně firewall.* 

* **Integration Runtime v místním prostředí (ve virtuální síti/místním prostředí)**
    
    | Úložiště dat                  | Podporované mechanismy zabezpečení sítě v úložištích dat         | Statická IP adresa | Důvěryhodné služby  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Úložiště dat Azure PaaS       | Azure Cosmos DB                                               | Ano       | -                   |
    |                                | Průzkumník dat Azure                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Yes       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Yes       | -                   |
    |                                | Azure File Storage                                            | Yes       | -                   |
    |                                | Azure Storage (blog, ADLS Gen2)                             | Yes       | Ano (pouze ověřování MSI) |
    |                                | Azure SQL DB, SQL DW (synapse Analytics), SQL ml          | Yes       | -                   |
    |                                | Azure Key Vault (pro načtení tajných klíčů/připojovacího řetězce) | Ano       | Yes                 |
    | Jiná úložiště dat PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage atd.              | Yes       | -                   |
    | LaaS Azure                     | SQL Server, Oracle atd.                                  | Yes       | -                   |
    | Místní laaS              | SQL Server, Oracle atd.                                  | Ano       | -                   |    

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících souvisejících článcích:
* [Podporované zdroje dat](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Azure Key Vault důvěryhodné služby](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Azure Storage důvěryhodné služby Microsoftu](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Spravovaná identita pro Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
