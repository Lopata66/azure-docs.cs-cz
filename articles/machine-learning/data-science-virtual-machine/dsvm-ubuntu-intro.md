---
title: Vytvoření virtuálního počítače Ubuntu Linuxem datové vědy
titleSuffix: Azure
description: Nakonfigurujte a vytvořte v Azure a provádět analýzy a strojové učení datové vědy virtuálních počítačů pro Linux (Ubuntu).
services: machine-learning
documentationcenter: ''
author: vijetajo
ms.author: vijetaj
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/13/2019
ms.openlocfilehash: 6d79bfa8f390d145bfd963b40497030140a3d135
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013605"
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Zřízení virtuálního počítače pro datové vědy pro Linux (Ubuntu)

Virtuální počítač pro datové vědy pro Linux je image virtuálního počítače založeného na Ubuntu, který umožňuje snadno a rychle začít s machine learningem, včetně obsáhlého learningu v Azure. Nástroje pro hloubkové učení patří:

* [Caffe](https://caffe.berkeleyvision.org/): Architektura hloubkového učení vytvořená pro rychlost, expressivity a modularitu
* [Caffe2](https://github.com/caffe2/caffe2): Verze Caffe pro různé platformy
* [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): Sada nástrojů pro hloubkové učení od Microsoft Research
* [H2O](https://www.h2o.ai/): Open Source platforma pro velké objemy dat a grafické uživatelské rozhraní
* [Keras](https://keras.io/): Rozhraní API vysoké úrovně neuronové sítě v Pythonu pro TensorFlow, Microsoft Cognitive Toolkit a Theano
* [MXNet](https://mxnet.io/): Flexibilní a efektivní knihovna pro hloubkové učení s mnoha jazykovými vazbami
* [ČÍSLICE NVIDIA](https://developer.nvidia.com/digits): Grafický systém, který zjednodušuje běžné úlohy s hloubkovým učením
* [PyTorch](https://pytorch.org/): Knihovna Pythonu vysoké úrovně s podporou pro dynamické sítě
* [TensorFlow](https://www.tensorflow.org/): Open source knihovna pro Machine Intelligence z Google
* [Theano](http://deeplearning.net/software/theano/): Knihovna Pythonu pro definování, optimalizaci a efektivní vyhodnocení matematických výrazů, které zahrnují multidimenzionální pole
* [Torch](http://torch.ch/): Vědecká výpočetní architektura s rozsáhlou podporou algoritmů strojového učení
* CUDA, cuDNN a ovladač NVIDIA
* Mnoho ukázkové poznámkové bloky Jupyter

Všechny knihovny jsou verze GPU, ale také spustit na CPU.

Virtuální počítač pro datové vědy pro Linux také obsahuje oblíbené nástroje pro datové vědy a vývoj aktivit, včetně:

* Microsoft R Server Developer Edition s Microsoft R Open
* Anaconda Python distribuce (verze 2.7 a 3.5), včetně oblíbených dat knihovny analýz
* JuliaPro - kurátorované distribuci jazyk Julia s Oblíbené knihovny analýz vědecké a data
* Instanci prostředí Spark samostatné a jednoho uzlu Hadoop (HDFS, Yarn)
* JupyterHub - víceuživatelské server poznámkového bloku Jupyter s podporou jazyka R, Python, PySpark, Julia jádrech
* Azure Storage Explorer
* Rozhraní příkazového řádku Azure (CLI) pro správu prostředků Azure
* Nástroje Machine learning
  * [Pro dostupné](https://github.com/JohnLangford/vowpal_wabbit): Rychlý systém strojového učení, který podporuje techniky, jako je online, hashing, allreduce, snižování, learning2search, aktivní a interaktivní učení
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Nástroj, který poskytuje rychlou a přesnou zesílenou implementaci stromu
  * [Rattle](https://togaware.com/rattle/): Grafický nástroj, který usnadňuje Začínáme s analýzou dat a strojovým učením v R.
  * [LightGBM](https://github.com/Microsoft/LightGBM): Rychlá, distribuovaná a vysoce výkonná architektura pro zvyšování barevného přechodu
* Azure SDK v jazyce Java, Python, node.js, Ruby, PHP
* Knihovny v R a Python pro použití v Azure Machine Learning a dalšími službami Azure
* Nástroje pro vývoj a editory (RStudio, PyCharm, IntelliJ, (emacs), vim)

Datových věd zahrnuje iterace v pořadí úloh:

1. Vyhledání, načítání a předzpracování dat
1. Vytváření a testování modely
1. Nasazení modelů pro použití v inteligentních aplikacích

Odborníci přes data pomocí různých nástrojů k provedení těchto úloh. Může být velmi časově najít odpovídající verze softwaru, a pak stáhnout, kompilace a instalaci těchto verzí.

Virtuální počítač pro datové vědy pro Linux můžete podstatně usnadňují tato zatížení. Použijte ho k nastartování analytického projektu. Umožňuje vám pro práci na úkolech v různých jazycích, včetně R, Python, SQL, Java a C++. Zahrnuté ve virtuálním počítači Azure SDK můžete vytvářet své aplikace s použitím různých služeb v Linuxu pro cloudovou platformu Microsoft. Kromě toho máte přístup do jiných jazyků, jako jsou Ruby, Perl, PHP a node.js, které jsou také předem nainstalované.

Neúčtují žádné poplatky softwaru pro image virtuálního počítače Toto datové vědy. Platíte jenom Azure hardware poplatky za využití, které jsou vyhodnocené na základě velikosti virtuálního počítače, který zřídíte. Další podrobnosti o poplatky za výpočetní prostředky můžete najít na [stránce virtuálního počítače na webu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Jiné verze virtuální počítač pro datové vědy

A [CentOS](linux-dsvm-intro.md) image je taky dostupná s řadu stejných nástrojů jako Ubuntu image. A [Windows](provision-vm.md) image je k dispozici také.

## <a name="prerequisites"></a>Požadavky

Než vytvoříte virtuální počítač pro datové vědy pro Linux, musíte mít předplatné Azure. Získat, najdete v článku [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Vytvořit váš virtuální počítač pro datové vědy pro Linux

Tady jsou kroky pro vytvoření instance z virtuální počítač pro datové vědy pro Linux:

1. Přejděte na virtuální počítač [webu Azure portal](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu). Může se zobrazit výzva, abyste se přihlásili ke svému účtu Azure, pokud ještě nejste přihlášení. 
1. Kliknutím na **vytvořit** otevřete průvodce.
    ![Konfigurace – data-věda-VM](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. V následujících částech jsou uvedeny vstupy pro každý z kroků v průvodci používané k vytvoření Data Science Virtual Machine Microsoft. Tady jsou vstupy potřebné ke konfiguraci každý z těchto kroků:

    a. **Základy**:
    
    * **Předplatné**: Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač. Toto předplatné musí mít oprávnění vytvářet prostředky.
    * **Skupina prostředků**: Vytvořit nové nebo použijte existující skupinu.
    * **Název virtuálního počítače**: Název vašeho serveru pro datové vědy, který vytváříte.
    * **Oblast**: Vyberte datové centrum, které je vhodné. Obvykle je většina vašich dat, nebo je nejblíže vašemu fyzickému umístění pro nejrychlejší přístup k síti datového centra.
    * **Možnosti dostupnosti**: Tuto hodnotu nastavte, pokud chcete tento virtuální počítač použít v sadách nebo zónách dostupnosti, jinak ponechte výchozí.
    * **Obrázek**: Ponechte výchozí hodnotu.
    * **Velikost**: Vyberte jeden z typů serveru, které splňuje požadavek na funkční a náklady na omezení. Vyberte virtuální počítač NC nebo ND-Series pro instance virtuálních počítačů na bázi GPU. 
    * **Uživatelské jméno**: Uživatelské jméno správce
    * **Veřejný klíč SSH**: Veřejný klíč RSA ve formátu single line (místo klíče SSH můžete použít heslo).
    
    b. **Disky**:
    
    * **Typ disku s operačním systémem**: Pokud dáváte přednost jednotce SSD (Solid-State Drive), vyberte **SSD úrovně Premium** . Jinak klikněte na tlačítko **standardní HDD**.
    
    c. Pro zbývající nastavení můžete použít jenom výchozí hodnoty. Vzít v úvahu jiné než výchozí hodnoty, najeďte myší na informační odkaz nápovědy pro konkrétní pole.
    
    Nakonec vyberte **zkontrolovat + vytvořit** .
    
    d. **Zkontrolovat + vytvořit**:
    
    * Po úspěšném ověření ověřte, zda jsou všechny informace, které jste zadali, správné. Pomocí odkazu uvedeného na podmínky použití. Pro velikost serveru, kterou jste zvolili ve vstupu velikosti, nemá tento virtuální počítač žádné další poplatky za výpočetní kapacitu. Zřizování spusťte kliknutím na **vytvořit**.
    
    Zřizování by měla trvat asi 5 minut. Stav zřizování se zobrazí na portálu Azure portal.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Jak získat přístup k virtuální počítač pro datové vědy pro Linux

Můžete získat přístup k datové VĚDY se systémem Ubuntu pomocí tří metod:

1. Na terminálu relace SSH
1. X2Go grafické relací
1. JupyterHub a JupyterLab pro aplikace Jupyter notebook

K Azure Notebooks můžete také připojit Data Science VM ke spuštění poznámkových bloků Jupyter na virtuálním počítači a obejít omezení úrovně bezplatné služby. Další informace najdete v tématu [Správa a konfigurace projektů poznámkových bloků – výpočetní vrstva](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Po vytvoření virtuálního počítače se můžete přihlásit se pomocí protokolu SSH. Pomocí přihlašovacích údajů účtu, které jste vytvořili v **Základy** část krok 3 pro rozhraní text prostředí. Ve Windows můžete stáhnout klientský nástroj SSH, jako je [Putty](https://www.putty.org). Pokud dáváte přednost grafické desktop (X systému Windows), můžete použít X11 předávání v Putty nebo nainstalovat klienta X2Go.

> [!NOTE]
> Klient X2Go lepších výsledků než X11 předávání v testování. Doporučujeme používat X2Go klienta klasické pracovní plochy grafické rozhraní.

### <a name="x2go"></a>X2Go

Virtuální počítač s Linuxem už zřízeny X2Go serveru a připravené tak, aby přijímal připojení klienta. Pro připojení k desktopu grafické virtuálního počítače s Linuxem, proveďte následující postup u svého klienta:

1. Stažení a instalace klienta X2Go pro vaše klientská platforma z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Spusťte klienta X2Go a vyberte **novou relaci**. Otevře se okno Konfigurace s více karet. Zadejte následující parametry konfigurace:
   * **Karta relace**:
     * **Hostitel**: Název hostitele nebo IP adresa Data Science VM pro Linux
     * **Přihlašovací jméno**: Uživatelské jméno na virtuálním počítači se systémem Linux.
     * **Port SSH**: Ponechte to 22, výchozí hodnota.
     * **Typ relace**: Změňte hodnotu na desktop Xfce. Virtuální počítač s Linuxem v současné době podporuje pouze Desktop xfce.
   * **Karta média**: Pokud je nepotřebujete používat, můžete vypnout zvukovou podporu a tisk klienta.
   * **Sdílené složky**: Pokud chcete adresáře z klientských počítačů připojených k VIRTUÁLNÍmu počítači se systémem Linux, přidejte do této karty adresáře klientských počítačů, které chcete sdílet s VIRTUÁLNÍm počítačem.

Po přihlášení k virtuálnímu počítači pomocí klienta SSH nebo grafické Desktop xfce prostřednictvím klienta X2Go, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Na XFCE uvidíte aplikacích zástupci v nabídce a ikony na ploše pro celou řadu nástrojů.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub a JupyterLab

Datové VĚDY se systémem Ubuntu běží [JupyterHub](https://github.com/jupyterhub/jupyterhub), server Jupyter více uživatelů. Pokud se chcete připojit, přejděte na\/adresu https:/your-VM-IP: 8000 na svém přenosném počítači nebo na ploše, zadejte uživatelské jméno a heslo, které jste použili k vytvoření virtuálního počítače, a přihlaste se. Mnoho ukázkové poznámkové bloky jsou k dispozici pro procházení a vyzkoušejte si.

JupyterLab, generace poznámkové bloky Jupyter a JupyterHub, je také k dispozici. Pokud k němu chcete získat přístup, přihlaste se k JupyterHub a potom přejděte\/k adrese URL https:/your-VM-IP: 8000/User/Your-username/Lab. JupyterLab můžete nastavit jako výchozí server Poznámkový blok přidáním tohoto řádku do */etc/jupyterhub/jupyterhub_config.py*:

```python
c.Spawner.default_url = '/lab'
```

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Nástroje nainstalované na virtuální počítač pro datové vědy pro Linux

### <a name="deep-learning-libraries"></a>Knihovny obsáhlého learningu

#### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit je open source, obsáhlý learning toolkit. Python vazeb jsou k dispozici v kořenové certifikáty a py35 prostředí Conda. Zahrnuje také nástroj příkazového řádku (cntk), který je již v CESTĚ.

Ukázkové poznámkové bloky Python jsou k dispozici v JupyterHub. Pokud chcete spustit základní ukázku z příkazového řádku, spusťte v prostředí následující příkazy:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Další informace najdete v tématu část CNTK [Githubu](https://github.com/Microsoft/CNTK)a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe

Caffe je rozhraní hloubkového učení z Berkeley vize a výukové centrum. Je k dispozici v /opt/caffe. Příklady lze nalézt v /opt/caffe/examples.

#### <a name="caffe2"></a>Caffe2

Caffe2 je rozhraní hloubkového učení ze sítě Facebook, která je založená na Caffe. Je dostupná v případě Pythonu 2.7 v kořenové prostředí Conda. Pokud ho chcete aktivovat, spusťte následující příkaz z prostředí:

```bash
source /anaconda/bin/activate root
```

Jsou k dispozici v JupyterHub některé příklad poznámkových bloků.

#### <a name="h2o"></a>H2O

H2O je rychlé, v paměti a distribuované machine learning a prediktivní analytická platforma. Balíček Python je nainstalována v kořenových a py35 Anaconda prostředí. Balíček R je také nainstalována. Chcete-li spustit H2O z příkazového řádku, spusťte `java -jar /dsvm/tools/h2o/current/h2o.jar`; existují různé [možnosti příkazového řádku](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , který chcete konfigurovat. Je přístupný webového uživatelského rozhraní tok tak, že přejdete do http://localhost:54321 začít. Ukázkové poznámkové bloky jsou dostupné v JupyterHub.

#### <a name="keras"></a>Keras

Keras je špičkové síťové rozhraní API neuronové v Pythonu, které je schopné spouštět na TensorFlow, Microsoft Cognitive Toolkit nebo Theano. Je k dispozici v kořenové certifikáty a py35 prostředí Pythonu.

#### <a name="mxnet"></a>MXNet

MXNet je navržené pro efektivity a pružnosti rozhraní hloubkového učení. Má vazby R a Python na datové VĚDY. Ukázkové poznámkové bloky jsou součástí JupyterHub a ukázkový kód je k dispozici v /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>NVIDIA ČÍSLIC

NVIDIA hloubkového učení GPU školení systému, známé jako ČÍSLICE, je systém pro zjednodušení běžných úloh hloubkového učení stejně jako správu dat, návrh a školení neuronové sítě v systémech GPU a sledování výkonu v reálném čase s použitím pokročilé vizualizace.

ČÍSLIC je dostupný jako služba, volá číslic. Spusťte službu a přejděte do http://localhost:5000 začít.

ČÍSLICE se nainstaluje také jako modul Pythonu v prostředí Conda kořenové.

#### <a name="tensorflow"></a>TensorFlow

TensorFlow je knihovna obsáhlého learningu od Googlu. Je to open source softwarová knihovna pro numerické výpočty pomocí grafů toku dat. Je k dispozici v prostředí Python py35 TensorFlow, a některé ukázkové poznámkové bloky jsou součástí JupyterHub.

#### <a name="theano"></a>Theano

Theano je knihovna Python pro efektivní numerické výpočty. Je k dispozici v kořenové certifikáty a py35 prostředí Pythonu. 

#### <a name="torch"></a>Torch

Svítilnou je vědecké výpočetní architektura díky široké podpoře algoritmů strojového učení. Je k dispozici v /dsvm/tools/torch a interaktivní relaci tou a Správce balíčků luarocks jsou k dispozici na příkazovém řádku. Příklady jsou k dispozici v /dsvm/samples/torch.

PyTorch dočíst i v prostředí Anaconda root. Příklady v /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server

R je jedním z nejoblíbenějších jazyků pro analýzy dat a strojové učení. Pokud chcete používat R pro analýzy, virtuální počítač má Microsoft R Server (Paní) s Microsoft R Open (MRO) a matematické jádra knihovny (MKL). MKL optimalizuje matematických operací, které jsou běžné v analytických algoritmů. MRO je 100 % kompatibilní s CRAN r. a některé z knihoven jazyka R, které jsou publikovány v síti CRAN se dá nainstalovat na MRO. PANÍ umožňuje škálování a operacionalizace R modely do webové služby. Můžete upravit sady R v jednom z výchozí editorů, jako je RStudio, editoru vi nebo (emacs). Pokud dáváte přednost použití editoru Emacs, byl předem nainstalované. Balíček (emacs) UPU (Emacs mluví statistiky) zjednodušuje práci se soubory R v editoru (emacs).

Spuštění R konzoly, stačí zadat **R** v prostředí. Tento příkaz vás provede interaktivním prostředím. K vývoji aplikace R, obvykle použít editor, například (emacs) nebo vi a pak spusťte skripty v rámci jazyka R. S prostředím RStudio máte úplné grafické prostředí IDE k vývoji aplikace R.

K dispozici je také skriptu jazyka R můžete nainstalovat [balíčky Top 20 R](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) potřebujete. Tento skript můžete spustit, jakmile se v rozhraní interaktivní R, které může uživatel zadat (jak je uvedeno) tak, že zadáte **R** v prostředí.  

### <a name="python"></a>Python

Anaconda Python se instaluje s 3,5 prostředí a použije se Python 2.7. 2\.7 prostředí se nazývá _kořenové_, a 3.5 prostředí se nazývá _py35_. Toto rozdělení obsahuje základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data.

Výchozím nastavením je py35 prostředí. Chcete-li aktivovat prostředí root (2.7):

```bash
source activate root
```

Znovu aktivovat py35 prostředí:

```bash
source activate py35
```

Abyste mohli vyvolat interaktivní relace Pythonu, stačí zadat **python** v prostředí. 

Nainstalovat dodatečné knihovny jazyka Python pomocí ```conda``` nebo ```pip``` . Pro pip aktivujte první správné prostředí, pokud nechcete, aby výchozí hodnota:

```bash
source activate root
pip install <package>
```

Nebo zadat úplnou cestu k nástroje pip:

```bash
/anaconda/bin/pip install <package>
```

Pro systém conda, musíte vždycky zadat název prostředí (_py35_ nebo _kořenové_):

```bash
conda install <package> -n py35
```

Pokud jste na grafického rozhraní nebo máte X11 předávání set up, můžete zadat **pycharm** ke spuštění Pythonu PyCharm integrovaného vývojového prostředí. Můžete použít výchozí textových editorů. Kromě toho můžete použít Spyder, integrované vývojové prostředí Pythonu, který je součástí distribuce Anaconda Python. Spyder potřebuje grafické stolní počítač nebo X11 předávání. Zástupce Spyder je součástí grafické desktop.

### <a name="jupyter-notebook"></a>Jupyter Notebook

Distribuce Anacondy také součástí poznámkového bloku Jupyter, prostředí pro sdílení kódu a analýzy. Poznámkový blok Jupyter je přístupný prostřednictvím JupyterHub. Přihlášení pomocí místního systému Linux uživatelské jméno a heslo.

Server poznámkového bloku Jupyter předem nakonfigurovaný s Python 2, Python 3 a R jádra. Existuje ikona na ploše s názvem "Poznámkový blok Jupyter" spustit prohlížeč pro přístup k serveru poznámkového bloku. Pokud jste na virtuálním počítači přes SSH nebo X2Go klienta, můžete také navštívit [ https://localhost:8000/ ](https://localhost:8000/) pro přístup k serveru Poznámkový blok Jupyter.

> [!NOTE]
> Pokračujte, pokud chcete získat všechna upozornění certifikátu.

Server poznámkového bloku Jupyter můžete přistupovat z libovolného hostitele. Stačí zadat *https://\<název DNS virtuálního počítače nebo IP adresu\>: 8000 /*

> [!NOTE]
> Port 8000 je otevřen v bráně firewall ve výchozím nastavení při zřízení virtuálního počítače. 

Sbalené ukázkové poznámkové bloky – jednu v Pythonu a jeden v jazyce R. Zobrazí se odkaz na ukázky na domovské stránce Poznámkový blok po ověření do poznámkového bloku Jupyter s použitím místního systému Linux uživatelské jméno a heslo. Můžete vytvořit nový poznámkový blok tak, že vyberete **nový**a potom příslušný jazyk jádra. Pokud se nezobrazí **nový** tlačítko, klikněte na tlačítko **Jupyter** ikony v levém horním rohu přejít na domovskou stránku server poznámkového bloku.

### <a name="apache-spark-standalone"></a>Apache Spark samostatné

Samostatná Apache Spark je předinstalován v systému Linux datové VĚDY můžete vyvíjet aplikace Spark místně nejprve před testování a nasazení na velkých clusterech. Jádra aplikace Jupyter můžete si projít programy PySpark. Když otevřete Jupyter, klikněte na tlačítko **nový** tlačítko by se měla zobrazit seznam dostupných jader. "– Python Spark" je jádra PySpark, který vám umožňuje vytvářet aplikace pomocí jazyka Python Spark. K sestavování programu Spark můžete použít také Python IDE, jako je PyCharm nebo Spyder. V této samostatné instanci se zásobník Spark spouští v rámci volajícího klientského programu, který urychluje a usnadňuje řešení problémů v porovnání s vývojem v clusteru Spark.

Poznámkový blok PySpark ukázka je k dispozici na Jupyter, které můžete najít v adresáři "Ve SparkML" v domovském adresáři Jupyter ($HOME/poznámkových bloků/ve SparkML/pySpark). 

Pokud programujete v jazyce R pro Spark, můžete použít Microsoft R Server, Spark nebo sparklyr. 

Před spuštěním v kontextu Sparku Microsoft R serveru, je potřeba jeden čas instalace krok umožňující místní Hadoop HDFS a Yarn instance jednoho uzlu. Ve výchozím nastavení služby Hadoop jsou nainstalované ale deaktivovány datové VĚDY. Chcete-li ji povolit, je třeba spustit následující příkazy jako uživatel root při prvním:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Služby související se systémem Hadoop můžete zastavit, pokud je nepotřebujete spuštěním```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```

Ukázka demonstrující, jak vyvíjet a testovat paní ve vzdáleném kontextu Spark (což je samostatná instance Spark na DSVM), je k dispozici v adresáři */dsvm/Samples/MRS* .

### <a name="ides-and-editors"></a>Editory a integrovanými vývojovými prostředími

Máte možnost zvolit si několik editorů kódu, včetně VI/VIM, (Emacs), PyCharm, RStudio a IntelliJ. IntelliJ, RStudio PyCharm jsou grafické editory a musíte být přihlášeni grafické desktopu k jejich použití. Tyto editory obsahují desktopových a aplikačních zástupci v nabídce je spustit.

**VIM** a **Emacs** jsou textové editory. Do (emacs) jsme nainstalovali balíček doplňku volat (emacs) mluví statistiky (UPU), která usnadňuje práci s jazykem R v editoru (emacs). Další informace najdete v [UPU](https://ess.r-project.org/).

**LaTex** se instaluje prostřednictvím balíčku texlive spolu s doplňkem (emacs) [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) balíček, který zjednodušuje vytváření dokumentů LaTex v rámci (emacs).  

### <a name="databases"></a>Databáze

#### <a name="graphical-sql-client"></a>Grafický klient SQL

**SQuirrel SQL**, grafický klient SQL byl poskytnut se připojit do různých databází (jako je například Microsoft SQL Server a MySQL) a ke spouštění dotazů SQL. SQuirrel SQL můžete spustit z grafické relace plochy (například pomocí klienta X2Go) pomocí ikony na ploše nebo pomocí následujícího příkazu v prostředí:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Před prvním použitím nastavení ovladačů a aliasy databáze. Ovladače JDBC jsou umístěné na:

*/usr/share/java/jdbcdrivers*

Další informace najdete v tématu [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Nástroje příkazového řádku pro přístup k systému Microsoft SQL Server

Balíček ovladače ODBC pro SQL Server také obsahuje dva nástroje příkazového řádku:

**bcp**: Nástroj BCP hromadně kopíruje data mezi instancí Microsoft SQL Server a datovým souborem v uživatelsky definovaném formátu. Nástroje bcp lze použít k importu velkého počtu nových řádků do tabulky serveru SQL Server nebo exportovat data z tabulek do datových souborů. Import dat do tabulky, musíte použít soubor formátu vytvořený pro tabulku nebo pochopit strukturu tabulky a typy dat, které jsou platné pro její sloupce.

Další informace najdete v tématu [připojení pomocí bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Příkazy jazyka Transact-SQL můžete zadat pomocí nástroje Sqlcmd a také systémových postupů a souborů skriptu na příkazovém řádku. Tento nástroj používá rozhraní ODBC pro spuštění dávky Transact-SQL.

Další informace najdete v tématu [připojení pomocí sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Existují určité rozdíly v tento nástroj mezi platformami operačních systémů Linux a Windows. Viz podrobnosti naleznete v dokumentaci.

#### <a name="database-access-libraries"></a>Přístup ke knihovnám databáze

Nejsou k dispozici v R a Python pro přístup k databázím knihovny.

* V jazyce R **RODBC** balíčku nebo **dplyr** balíček umožňuje zadat dotaz nebo spouštění příkazů jazyka SQL na serveru databáze.
* V jazyce Python **pyodbc** knihovna poskytuje přístup k databázi s rozhraním ODBC jako základní vrstvě.  

### <a name="azure-tools"></a>Nástroje Azure

Na virtuálním počítači jsou nainstalované nástroje Azure následující:

* **Rozhraní příkazového řádku Azure**: Rozhraní příkazového řádku Azure umožňuje vytvářet a spravovat prostředky Azure prostřednictvím příkazů prostředí. Abyste mohli vyvolat nástrojů Azure, stačí zadat **azure pomáhají**. Další informace najdete v tématu [stránku dokumentace k Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Průzkumník služby Microsoft Azure Storage**: Průzkumník služby Microsoft Azure Storage je grafický nástroj, který se používá k procházení objektů, které jste uložili v účtu úložiště Azure, a k nahrávání a stahování dat do a z objektů blob Azure. Průzkumník služby Storage se dá dostat z ikonu zástupce na ploše. Můžete ho vyvolat na příkazovém řádku prostředí tak, že zadáte **StorageExplorer**. Musíte být přihlášeni z klienta X2Go nebo mít nastavené přesměrování X11.
* **Knihovny Azure**: Níže jsou uvedené některé z předem nainstalovaných knihoven.
  
  * **Python**: Knihovny související s Azure v Pythonu, které jsou nainstalované, jsou **Azure**, **AzureML**, **pydocumentdb**a **pyodbc**. První tři knihovny můžete přístup služby Azure storage, Azure Machine Learning a Azure Cosmos DB (databáze NoSQL v Azure). Čtvrtý knihovny pyodbc (spolu s ovladač Microsoft ODBC pro SQL Server), umožňuje přístup k systému SQL Server, Azure SQL Database a Azure SQL Data Warehouse z Pythonu pomocí rozhraní ODBC. Zadejte **pip seznamu** zobrazíte všechny uvedené knihovny. Ujistěte se, že tento příkaz spustit v případě Pythonu 2.7 i 3.5 prostředí.
  * **R**: Knihovny související s Azure v jazyce R, které jsou nainstalovány, jsou **AzureML** a **RODBC**.
  * **Java**: Seznam knihoven Azure Java najdete v adresáři **/dsvm/SDK/AzureSDKJava** na virtuálním počítači. Jsou klíčové knihovny Azure ovladače úložiště a správu rozhraní API služby Azure Cosmos DB a JDBC pro SQL Server.  

Můžete přistupovat [webu Azure portal](https://portal.azure.com) předinstalované prohlížeče Firefox. Na portálu Azure portal můžete vytvářet, spravovat a monitorovat prostředky Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná Cloudová služba, která umožňuje vytvořit, nasadit a sdílení řešení prediktivní analýzy. Vytváření experimentů a modelů z Azure Machine Learning Studio. Byla přístupná z webového prohlížeče na virtuální počítač pro datové vědy návštěvou [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po přihlášení k Azure Machine Learning Studio máte přístup k plátno pro experimentování ve kterém můžete vytvořit logický tok pro algoritmech strojového učení. Také mají přístup do poznámkového bloku Jupyter, které jsou hostované v Azure Machine Learning a můžete bez problémů pracovat s experimenty v nástroji Machine Learning Studio. Zprovoznění modelů strojového učení, které jste vytvořili podle jejich zabalení v rozhraní webových služeb. Modely strojového učení zprovozňování umožňují klientům napsaným v jakémkoli jazyce vyvolat předpovědi z těchto modelů. Další informace najdete v tématu [dokumentace ke službě Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Můžete také sestavovat modely v R nebo Pythonu ve virtuálním počítači a potom ji nasadíte v produkčním prostředí v Azure Machine Learning. Jsme nainstalovali knihovny v jazyce R (**AzureML**) a Python (**azureml**) Chcete-li povolit tuto funkci.

Informace o tom, jak nasadit modely R a Pythonu do Azure Machine Learning najdete v tématu [deset věcí, které můžete provést na virtuální počítač pro datovou vědu](vm-do-ten-things.md) (zejména v části "sestavovat modely s využitím R nebo Python a Zprovozňovat jejich používání Azure Machine Learning").

> [!NOTE]
> Tyto pokyny byly vytvořeny pro verze Windows virtuální počítač pro datové vědy. Ale uvedené informace o nasazení modelů Azure Machine Learning je pro virtuální počítač s Linuxem.

### <a name="machine-learning-tools"></a>Nástroje Machine learning

Virtuální počítač obsahuje několik strojového učení, nástroje a algoritmy, které byly předem zkompilovat a předem nainstalovaný místně. Mezi ně patří:

* **Pro dostupné**: Rychlý online algoritmus pro učení.
* **xgboost**: Nástroj, který poskytuje optimalizované a posílené doménové algoritmy.
* **Rattle**: Grafický nástroj založený na jazyce R pro snadné zkoumání a modelování dat.
* **Python**: Anaconda Python se dodává s algoritmy strojového učení s knihovnami, jako je Scikit – učení. Můžete nainstalovat další knihovny pomocí `pip install` příkazu.
* **LightGBM**: Rychlé a distribuované barevné přechodové rozhraní s vysokým výkonem na základě algoritmů rozhodovacího stromu.
* **R**: K dispozici je bohatá knihovna funkcí strojového učení pro R. Některé z knihoven, které jsou předem nainstalovány, jsou LM, GLM, randomForest, rpart. Další knihovny se dá nainstalovat spuštěním:
  
        install.packages(<lib name>)

Zde jsou některé další informace o nástrojích pro první tři strojového učení v seznamu.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit je strojové učení systém, který používá techniky, jako je online, hash, allreduce, snížení, learning2search, aktivní a interaktivního vzdělávacího.

Chcete-li spustit nástroj na základním příkladu, použijte následující příkazy:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Existují jiné, větší ukázky v tomto adresáři. Další informace o zobrazit najdete v tématu [v této části GitHub](https://github.com/JohnLangford/vowpal_wabbit)a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost

Toto je knihovnu, která je navržený a optimalizovaný pro algoritmy Posílený (strom). Cílem této knihovny je tak, aby nabízel výpočet omezení počítačů extrémy potřebných k poskytování rozsáhlé stromové struktury zvýšení skóre, která je škálovatelná, přenosných a přesné.

Je zadaný jako příkazového řádku, jakož i knihovny R.

Použití této knihovny v jazyce R, můžete spustit interaktivní relace jazyka R (pouze zadáním **R** v prostředí) a načíst knihovnu.

Tady je jednoduchý příklad, který můžete spustit v R řádku:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Spusťte příkazový řádek xgboost, tady jsou příkazy ke spuštění v prostředí:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.Model soubor zapsán do zadaný adresář. Informace o tomto příkladu ukázku najdete [na Githubu](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Další informace o xgboost, najdete v článku [stránky dokumentace xgboost](https://xgboost.readthedocs.org/en/latest/)a jeho [úložiště GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle

Rattle ( **R** **A**nalytická **T**rukopis **T**o **L**získat **E** asily) používá zkoumání a modelování dat na základě grafického uživatelského rozhraní. Zobrazí statistické a vizuální souhrny dat, transformace dat, která lze snadno modelovat, sestavení bez dohledu a pod dohledem modely z dat, uvede výkon modelů graficky, a nastaví skóre, které se nová data. Také vygeneruje kód R, replikaci operace v uživatelském rozhraní, které můžete spustit přímo v R nebo použít jako výchozí bod pro další analýzu.

Pokud chcete spustit Rattle, musíte být v grafickém přihlašovací relaci plochy. V terminálu zadejte ```R``` zadat prostředí R. Na řádku R zadejte následující příkazy:

```R
library(rattle)
rattle()
```

Nyní grafické rozhraní otevře sadu karet. Tady jsou kroky pro rychlý Start v Rattle potřebné k použití ukázkové sady dat počasí a vytvoření modelu. V některé z následujících kroků zobrazí se výzva k automaticky nainstaluje a načte některé požadované balíčky jazyka R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte přístup k instalaci balíčku v adresáři systému (výchozí), může se zobrazit výzva v okně konzoly R k instalaci balíčků na své osobní knihovny. Odpověď *y* -li zobrazit tyto výzvy.

1. Klikněte na tlačítko **Spustit**.
1. Otevře se dialogové okno, s výzvou, pokud chcete použít příklad sady dat o počasí. Klikněte na tlačítko **Ano** načíst v příkladu.
1. Klikněte na tlačítko **modelu** kartu.
1. Klikněte na tlačítko **Execute** sestavit rozhodovací strom.
1. Klikněte na tlačítko **nakreslit** zobrazíte rozhodovací strom.
1. Klikněte na tlačítko **doménové struktury** přepínač a klikněte na tlačítko **Execute** vytvářet náhodné doménové struktury.
1. Klikněte na tlačítko **vyhodnotit** kartu.
1. Klikněte na tlačítko **riziko** přepínač a klikněte na tlačítko **Execute** zobrazíte dva grafy výkonu rizika (součet).
1. Klikněte na tlačítko **protokolu** zobrazte vytvořit R kód pro předchozí operace.
   (Kvůli chybě v aktuální verzi Rattle, je třeba jej vložit *#* znak před *exportovat tento protokol...*  v textu v protokolu.)
1. Klikněte na tlačítko **exportovat** tlačítko pro uložení souboru skriptu jazyka R s názvem *weather_script. R* do domovské složky.

Můžete ukončit Rattle a R. Nyní můžete upravit vygenerovaný skript jazyka R nebo ho použít, protože ho můžete kdykoli spustit vše, co se provádí v Uživatelském rozhraní Rattle opakovat. Zejména pro začátečníky v jazyce R Toto je snadný způsob, jak rychle provést analýzu a strojové učení v jednoduché grafického rozhraní, při automatické generování kódu v jazyce R můžete upravit nebo Další informace.

## <a name="next-steps"></a>Další postup

Zde je, jak můžete pokračovat v učení a zkoumání:

* [Vědecké zpracování dat na virtuální počítač pro datové vědy pro Linux](linux-dsvm-walkthrough.md) návodu se dozvíte, jak provést několik běžných úloh datové vědy Data Science virtuálního počítače s Linuxem zřízené tady. 
* Prozkoumejte různé nástrojů pro datové vědy pro datovou vědu virtuální počítač a vyzkoušejte si nástroje popsané v tomto článku. Můžete také spustit *dsvm. více informací* na prostředí v rámci virtuálního počítače pro základní informace a odkazy na další informace o těchto nástrojích, nainstalovaný na virtuálním počítači.  
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](https://aka.ms/tdsp).
* Přejděte [galerii Azure AI](https://gallery.azure.ai/) pro machine learning a datové analýzy ukázek, které využívají služby Azure AI.
