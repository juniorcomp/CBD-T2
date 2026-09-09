# Arquiteturas de Armazenamento Físico: DAS e RAID

## Um Estudo sobre Interfaces de Conexão Direta, Técnicas de Organização de Dados e Níveis de Redundância para Sistemas de Banco de Dados

---

**Referências Bibliográficas Fundamentais:**

- SILBERSCHATZ, A.; KORTH, H. F.; SUDARSHAN, S. *Database System Concepts*. 7ª ed. McGraw-Hill, 2019.
- ELMASRI, R.; NAVATHE, S. B. *Fundamentals of Database Systems*. 7ª ed. Pearson, 2016.
- GARCIA-MOLINA, H.; ULLMAN, J. D.; WIDOM, J. *Database Systems: The Complete Book*. 2ª ed. Pearson, 2009.

---

## 1. Introdução ao DAS e Interfaces de Conexão

### 1.1 Conceito de Direct Attached Storage (DAS)

O **Direct Attached Storage (DAS)** constitui a forma mais elementar e historicamente consolidada de arquitetura de armazenamento, na qual os dispositivos de storage — sejam discos rígidos magnéticos (HDDs), unidades de estado sólido (SSDs) ou unidades ópticas — são conectados **diretamente** ao barramento de entrada/saída de um servidor ou estação de trabalho, sem a intermediação de uma rede de armazenamento dedicada (SILBERSCHATZ; KORTH; SUDARSHAN, 2019, Cap. 12).

Conforme descrito por Elmasri e Navathe (2016), o subsistema de armazenamento físico de um SGBD (Sistema de Gerenciamento de Banco de Dados) depende criticamente das características do hardware subjacente. O DAS oferece **latência mínima** de acesso, pois elimina os overheads de protocolos de rede (como TCP/IP em NAS ou Fibre Channel em SAN), tornando-se particularmente adequado para cargas de trabalho transacionais que exigem tempos de resposta determinísticos.

Do ponto de vista arquitetural, Garcia-Molina, Ullman e Widom (2009) classificam o DAS como um modelo de acesso **ponto-a-ponto**, onde existe uma relação exclusiva entre o host computacional e o dispositivo de armazenamento. Essa exclusividade implica que os dados armazenados em um dispositivo DAS são, em princípio, acessíveis apenas pelo host ao qual estão fisicamente conectados — uma característica que contrasta com arquiteturas compartilhadas como SAN (Storage Area Network) e NAS (Network Attached Storage).

A eficácia de uma solução DAS está intrinsecamente vinculada à **interface de conexão** utilizada entre o host e o dispositivo. A escolha da interface determina a largura de banda máxima, a latência de acesso, a distância física suportada e o grau de sofisticação do protocolo de comando (incluindo enfileiramento de comandos e gerenciamento de erros).

### 1.2 Interfaces de Conexão

#### 1.2.1 SATA (Serial Advanced Technology Attachment)

A interface **SATA** (Serial ATA) emergiu como evolução da interface Parallel ATA (PATA/IDE), substituindo o barramento paralelo de 40/80 condutores por uma conexão serial ponto-a-ponto. O padrão SATA foi projetado para oferecer maior largura de banda, cabos mais finos (facilitando o fluxo de ar em gabinetes) e hot-swapping nativo.

| Geração | Taxa de Transferência | Codificação | Ano de Introdução |
|---------|----------------------|-------------|-------------------|
| SATA I (1.5 Gb/s) | ~150 MB/s | 8b/10b | 2003 |
| SATA II (3 Gb/s) | ~300 MB/s | 8b/10b | 2004 |
| SATA III (6 Gb/s) | ~600 MB/s | 8b/10b | 2009 |

O protocolo SATA utiliza o conjunto de comandos **ATA Command Set (ACS)**, que inclui o **NCQ (Native Command Queuing)** com profundidade de fila de até 32 comandos. O NCQ permite que o controlador do disco reordene as requisições de I/O para minimizar o tempo de busca (*seek time*), o que é particularmente relevante para HDDs onde a latência rotacional e o posicionamento do atuador são fatores determinantes de desempenho (SILBERSCHATZ; KORTH; SUDARSHAN, 2019).

**Cenários de uso em BD:** O SATA é amplamente empregado em servidores de banco de dados de pequeno e médio porte, data warehouses onde a relação custo/capacidade é prioritária, e sistemas de backup em disco. Sua limitação principal reside na profundidade de fila reduzida e na menor robustez do protocolo de recuperação de erros em comparação com SAS.

#### 1.2.2 eSATA (External Serial ATA)

O **eSATA** é a extensão externa do padrão SATA, definido a partir da revisão SATA 2.6, que especifica conectores e cabos blindados para uso externo ao gabinete do computador. O eSATA mantém a mesma largura de banda da interface SATA interna correspondente (até 6 Gb/s no SATA III), diferenciando-se do USB e FireWire por não introduzir overhead de tradução de protocolo.

O conector eSATA é eletricamente idêntico ao SATA interno, porém com blindagem aprimorada e um formato de conector que suporta até 5.000 ciclos de inserção/remoção (contra 50 ciclos do conector SATA interno). Uma limitação importante é a **ausência de alimentação elétrica** pelo cabo eSATA padrão — embora a variante **eSATAp** (powered eSATA) combine dados SATA com alimentação USB para dispositivos de menor consumo.

**Cenários de uso em BD:** O eSATA é utilizado em cenários de backup externo direto e em estações de trabalho que necessitam de expansão de armazenamento sem a penalidade de desempenho associada a interfaces como USB 2.0. No contexto de bancos de dados, é empregado para unidades de backup dedicadas onde a velocidade de transferência é crítica.

#### 1.2.3 SAS (Serial Attached SCSI)

A interface **SAS (Serial Attached SCSI)** representa a evolução serial do barramento paralelo SCSI (*Small Computer System Interface*), combinando a topologia ponto-a-ponto do SATA com o conjunto de comandos SCSI, amplamente reconhecido por sua robustez, versatilidade e capacidade de gerenciamento avançado de dispositivos.

| Geração | Taxa de Transferência (por lane) | Largura de Banda Full-Duplex | Ano |
|---------|----------------------------------|------------------------------|-----|
| SAS-1 | 3 Gb/s | 600 MB/s | 2004 |
| SAS-2 | 6 Gb/s | 1.200 MB/s | 2009 |
| SAS-3 | 12 Gb/s | 2.400 MB/s | 2013 |
| SAS-4 | 22,5 Gb/s | 4.500 MB/s | 2017 |

O SAS oferece características técnicas superiores ao SATA para ambientes empresariais:

- **Comunicação full-duplex:** enquanto o SATA opera em half-duplex, o SAS permite transmissão e recepção simultâneas, duplicando efetivamente a largura de banda.
- **Profundidade de fila de 256 comandos:** contra 32 do NCQ SATA, permitindo maior concorrência de I/O.
- **Topologia expansível:** um domínio SAS pode endereçar até 16.384 dispositivos através de expanders, contra a conexão ponto-a-ponto exclusiva do SATA.
- **Protocolo de erros avançado:** o SCSI Error Recovery Protocol oferece mecanismos sofisticados de detecção e recuperação de erros, incluindo reservas de setor e realocação automática.
- **Compatibilidade reversa com SATA:** controladores SAS podem conectar discos SATA (mas não o inverso), permitindo configurações híbridas.

**Cenários de uso em BD:** O SAS é o padrão *de facto* para servidores de banco de dados de missão crítica, sistemas OLTP de alta concorrência e configurações RAID empresariais. A profundidade de fila estendida e o full-duplex são essenciais para cargas de trabalho com alto grau de paralelismo de I/O (GARCIA-MOLINA; ULLMAN; WIDOM, 2009).

#### 1.2.4 FireWire (IEEE 1394)

O **FireWire** (IEEE 1394), também conhecido como **i.LINK** (Sony) ou **Lynx** (Texas Instruments), é uma interface serial de alta velocidade originalmente desenvolvida pela Apple e padronizada pelo IEEE. Utiliza uma topologia *daisy-chain* ou árvore, com protocolo isocrônico que garante largura de banda determinística.

| Variante | Taxa de Transferência | Conector |
|----------|----------------------|----------|
| FireWire 400 (IEEE 1394a) | 400 Mb/s (~50 MB/s) | 4-pin / 6-pin |
| FireWire 800 (IEEE 1394b) | 800 Mb/s (~100 MB/s) | 9-pin |
| FireWire S1600 (IEEE 1394-2008) | 1,6 Gb/s (~200 MB/s) | 9-pin (beta) |
| FireWire S3200 (IEEE 1394-2008) | 3,2 Gb/s (~400 MB/s) | 9-pin (beta) |

O FireWire distingue-se por sua arquitetura **peer-to-peer**, que permite comunicação direta entre dispositivos sem mediação do host CPU, reduzindo a utilização do processador durante transferências de dados. O protocolo suporta até 63 dispositivos por barramento e fornece alimentação elétrica pelo cabo (até 45W no IEEE 1394a).

**Cenários de uso em BD:** Historicamente utilizado em estações de trabalho para armazenamento externo de alto desempenho, o FireWire encontra-se em declínio frente ao USB 3.x e Thunderbolt. Em contextos de banco de dados, seu uso era restrito a soluções DAS externas em ambientes de edição multimídia com bancos de dados de ativos digitais.

#### 1.2.5 USB (Universal Serial Bus) — Versões 3 e 4

A interface **USB** evoluiu de uma solução de baixa velocidade para periféricos genéricos para uma interface de armazenamento de alta performance, especialmente a partir das gerações USB 3.x e USB4.

| Versão | Nome Comercial | Taxa de Transferência | Codificação | Conector |
|--------|---------------|----------------------|-------------|----------|
| USB 3.0 | USB 3.2 Gen 1 (SuperSpeed) | 5 Gb/s (~500 MB/s) | 8b/10b | Type-A / Type-C |
| USB 3.1 | USB 3.2 Gen 2 (SuperSpeed+) | 10 Gb/s (~1.212 MB/s) | 128b/132b | Type-A / Type-C |
| USB 3.2 | USB 3.2 Gen 2x2 | 20 Gb/s (~2.424 MB/s) | 128b/132b | Type-C |
| USB4 v1.0 | USB4 Gen 2x2 / Gen 3x2 | 20 / 40 Gb/s | 128b/132b | Type-C |
| USB4 v2.0 | USB4 80 Gbps | 80 Gb/s (120 Gb/s assimétrico) | PAM-3 / 128b/132b | Type-C |

O USB 3.x introduziu o conceito de **SuperSpeed**, com um barramento de dados adicional operando em paralelo ao barramento USB 2.0 legado. O protocolo de transferência em massa (*Bulk Transfer*) utilizado para armazenamento suporta enfileiramento de comandos e transferência assíncrona, embora com overhead de protocolo superior ao eSATA.

O **USB4**, baseado na especificação Thunderbolt 3 licenciada pela Intel ao USB-IF, unifica dados, vídeo e energia em um único cabo Type-C, com suporte a túnel de protocolos PCIe e DisplayPort. A versão 2.0 do USB4 alcança 80 Gb/s simétricos (ou 120 Gb/s em modo assimétrico com PAM-3), posicionando-se como uma interface viável para armazenamento externo de altíssimo desempenho.

**Cenários de uso em BD:** O USB 3.x/4 é utilizado em cenários de backup portátil, unidades de armazenamento externo para desenvolvimento e testes de banco de dados, e em soluções de armazenamento para edge computing. O USB4 v2.0, com largura de banda equivalente a interfaces internas, abre possibilidades para DAS externo de alta performance anteriormente restritas a Thunderbolt ou eSATA.

#### 1.2.6 HBA (Host Bus Adapter)

O **HBA (Host Bus Adapter)** não constitui uma interface de barramento em si, mas sim um componente de hardware — tipicamente uma placa de expansão PCIe — que atua como **controlador de interface** entre o barramento interno do host (PCIe) e o protocolo de armazenamento externo (SAS, SATA, Fibre Channel, iSCSI). Conforme Silberschatz, Korth e Sudarshan (2019), o HBA desempenha papel fundamental na hierarquia de armazenamento ao abstrair os detalhes do protocolo de transporte e apresentar ao sistema operacional uma interface uniforme de acesso a blocos.

Funcionalidades avançadas de um HBA incluem:

- **Offloading de protocolo:** processamento do protocolo SCSI/SAS/FC em hardware dedicado, liberando ciclos de CPU do host.
- **Cache de escrita com bateria (BBU — Battery Backup Unit):** permite write-back caching com proteção contra perda de dados em caso de falha de energia.
- **Processamento de RAID em hardware:** muitos HBAs integram controladores RAID dedicados, realizando cálculos de paridade e operações de striping sem consumir recursos do processador principal.
- **Multipathing:** suporte a múltiplos caminhos físicos para o mesmo dispositivo de armazenamento, aumentando disponibilidade e throughput.

Em arquiteturas DAS corporativas, o HBA é o elemento central que viabiliza a construção de arrays de discos com níveis avançados de RAID, conectando múltiplos discos SAS ao host através de expanders e backplanes.

### 1.3 Tabela Comparativa das Interfaces de Conexão

| Interface | Taxa Máxima | Distância Máx. | Hot-Swap | Profundidade de Fila | Alimentação | Uso Primário |
|-----------|------------|-----------------|----------|---------------------|-------------|--------------|
| SATA III | 6 Gb/s | 1 m (interno) | Sim | 32 (NCQ) | N/A (interno) | Desktop, NAS, storage econômico |
| eSATA | 6 Gb/s | 2 m | Sim | 32 (NCQ) | Não (padrão) | Backup externo direto |
| SAS-4 | 22,5 Gb/s | 10 m | Sim | 256 | N/A (interno) | Servidores, RAID empresarial |
| FireWire 800 | 800 Mb/s | 4,5 m | Sim | N/A | Sim (até 45W) | Legado, mídia digital |
| USB 3.2 Gen 2x2 | 20 Gb/s | 1 m | Sim | N/A | Sim (até 100W) | Storage externo, portátil |
| USB4 v2.0 | 80 Gb/s | 1 m (passivo) | Sim | N/A | Sim (até 240W EPR) | DAS externo de alta performance |
| HBA (PCIe 4.0 x8) | ~128 Gb/s | N/A (interno) | N/A | Depende do protocolo | N/A | Controlador RAID, arrays SAS/FC |

---

## 2. Fundamentos de Desempenho e Confiabilidade em Storage

A capacidade de um subsistema de armazenamento atender às demandas de um SGBD é determinada por dois eixos fundamentais e frequentemente complementares: **desempenho** (throughput e latência de I/O) e **confiabilidade** (disponibilidade dos dados e tolerância a falhas). Conforme Silberschatz, Korth e Sudarshan (2019, Cap. 12), a organização física do armazenamento deve equilibrar esses dois objetivos, reconhecendo que otimizações em um eixo frequentemente impõem custos no outro.

### 2.1 Paralelismo em I/O

O **paralelismo** no contexto de armazenamento refere-se à capacidade de distribuir operações de entrada/saída entre múltiplos dispositivos físicos que operam simultaneamente, de modo a ampliar a largura de banda agregada e reduzir o tempo de resposta percebido pela aplicação.

Garcia-Molina, Ullman e Widom (2009) formalizam que, dado um conjunto de *n* discos operando em paralelo, o throughput teórico máximo do subsistema é:

> **Throughput_agregado = n × Throughput_individual**

Na prática, o ganho efetivo é limitado por:

1. **Overhead do controlador:** o processamento de distribuição e reagregação dos dados consome ciclos de CPU ou do processador do HBA.
2. **Contenção no barramento:** se múltiplos discos compartilham o mesmo barramento (e.g., um único canal SAS), a largura de banda do barramento torna-se o gargalo.
3. **Desbalanceamento de carga:** se os dados não estão uniformemente distribuídos entre os discos, alguns dispositivos tornam-se pontos quentes (*hotspots*) enquanto outros permanecem ociosos.
4. **Latência de sincronização:** em operações que exigem dados de múltiplos discos (como leitura de um bloco distribuído), o tempo de resposta é determinado pelo disco mais lento (*straggler effect*).

No contexto de SGBDs, o paralelismo de I/O é explorado pelo *buffer manager* ao realizar **prefetching** de páginas e por operações de varredura (*scan*) que leem blocos de múltiplos discos simultaneamente. Silberschatz, Korth e Sudarshan (2019) enfatizam que a eficácia do paralelismo depende criticamente da técnica de distribuição de dados empregada — notadamente, *data striping* — que será detalhada na Seção 3.

### 2.2 Redundância

A **redundância** constitui o mecanismo fundamental para garantir a **disponibilidade** e a **confiabilidade** dos dados armazenados. Elmasri e Navathe (2016) definem redundância, no contexto de armazenamento físico, como a manutenção de cópias adicionais (parciais ou completas) dos dados, de modo que a falha de um ou mais dispositivos não resulte em perda irrecuperável de informação.

As formas de redundância empregadas em sistemas de armazenamento incluem:

- **Replicação completa (Mirroring):** cada bloco de dados é mantido em dois ou mais discos independentes. Oferece a recuperação mais rápida, porém com custo máximo de capacidade.
- **Redundância por paridade:** informações de verificação (paridade) são calculadas sobre conjuntos de blocos de dados e armazenadas em disco(s) dedicado(s) ou distribuído(s). Permite a reconstrução de dados perdidos com menor overhead de armazenamento, ao custo de maior complexidade computacional.
- **Redundância por códigos de correção de erros (ECC):** técnicas como códigos de Hamming ou Reed-Solomon que permitem não apenas detectar mas corrigir erros em múltiplos bits ou blocos, utilizadas em níveis RAID específicos.

A inter-relação entre paralelismo e redundância é central na teoria de RAID: o **paralelismo** visa maximizar o desempenho, enquanto a **redundância** mitiga o risco inerentemente ampliado de falha quando múltiplos dispositivos operam simultaneamente. Conforme Silberschatz, Korth e Sudarshan (2019) demonstram formalmente, a probabilidade de falha de *pelo menos um* disco em um array de *n* discos cresce monotonicamente com *n*, tornando a redundância não apenas desejável mas **necessária** em arrays paralelos de produção.

### 2.3 Métricas de Falha e Reparo

A caracterização quantitativa da confiabilidade de um sistema de armazenamento exige a definição precisa de métricas probabilísticas que modelam o comportamento de falha e recuperação dos componentes. As quatro métricas fundamentais são apresentadas a seguir, conforme a terminologia consolidada na literatura (SILBERSCHATZ; KORTH; SUDARSHAN, 2019; ELMASRI; NAVATHE, 2016):

#### 2.3.1 MTTF — Mean Time to Failure

O **MTTF (Tempo Médio até a Falha)** representa o tempo esperado de operação contínua de um componente desde sua ativação (ou última reparação) até a ocorrência da próxima falha. O MTTF é aplicável a **componentes não reparáveis** ou ao primeiro ciclo de vida de um componente reparável.

Para um disco rígido individual com taxa de falha constante λ (modelo exponencial):

> **MTTF = 1 / λ**

Fabricantes de HDDs tipicamente especificam MTTF na faixa de 1.000.000 a 2.000.000 de horas para discos empresariais (equivalente a 114–228 anos). É fundamental observar que esse valor representa uma média estatística sobre uma grande população de discos, não uma garantia de vida útil individual.

Para um array de *n* discos independentes sem redundância, o MTTF do sistema (tempo até a primeira falha de qualquer disco) é:

> **MTTF_array = MTTF_disco / n**

Essa relação inversa demonstra quantitativamente por que arrays grandes sem redundância são intrinsecamente frágeis: um array de 100 discos com MTTF individual de 1.000.000 horas tem MTTF de sistema de apenas 10.000 horas (~14 meses).

#### 2.3.2 MTTR — Mean Time to Repair

O **MTTR (Tempo Médio de Reparo)** quantifica o tempo esperado para restaurar um componente falhado à operação normal. No contexto de arrays de discos, o MTTR abrange:

1. **Detecção da falha:** tempo até o sistema identificar o disco defeituoso (minutos em sistemas com monitoramento ativo, horas ou dias em sistemas sem monitoramento).
2. **Substituição física:** tempo para o operador substituir o disco falhado (minutos com hot-swap, horas se requer desligamento).
3. **Reconstrução dos dados:** tempo para o controlador RAID reconstruir os dados do disco substituído a partir da redundância existente. Este componente é frequentemente o dominante, podendo levar **horas a dias** dependendo da capacidade do disco e da carga concorrente do sistema.

O MTTR é uma métrica operacional (não intrínseca ao hardware) e depende de fatores como política de manutenção, disponibilidade de peças sobressalentes e carga do sistema durante a reconstrução.

#### 2.3.3 MTBF — Mean Time Between Failures

O **MTBF (Tempo Médio Entre Falhas)** é aplicável a **sistemas reparáveis** e representa o tempo médio entre duas falhas consecutivas, incluindo o tempo de reparo:

> **MTBF = MTTF + MTTR**

Em sistemas de alta disponibilidade onde MTTR << MTTF, os valores de MTBF e MTTF convergem. A distinção, porém, é importante em cenários onde o MTTR é significativo — como na reconstrução de arrays RAID de grande capacidade, onde o tempo de rebuild pode representar uma fração não desprezível do MTBF.

Silberschatz, Korth e Sudarshan (2019) enfatizam que o MTBF é a métrica mais relevante para planejamento de capacidade e dimensionamento de SLAs (Service Level Agreements), pois captura o ciclo completo de operação-falha-reparo.

#### 2.3.4 MTTDL — Mean Time to Data Loss

O **MTTDL (Tempo Médio até a Perda de Dados)** é a métrica mais crítica para sistemas de armazenamento redundantes, pois quantifica o tempo esperado até a ocorrência de uma **perda irrecuperável de dados** — ou seja, uma falha que excede a capacidade de redundância do sistema.

Para um array espelhado (RAID 1) com dois discos:

> **MTTDL = MTTF_disco² / (2 × MTTR)**

Essa fórmula reflete que a perda de dados ocorre apenas quando um segundo disco falha *durante o intervalo de reparo* do primeiro disco já falhado. Para um RAID 1 com MTTF individual de 1.000.000 horas e MTTR de 24 horas:

> **MTTDL = (10⁶)² / (2 × 24) = 10¹² / 48 ≈ 20,8 × 10⁹ horas ≈ 2,4 × 10⁶ anos**

Este resultado demonstra o poder da redundância: enquanto o array sem redundância de dois discos teria MTTF de 500.000 horas (~57 anos), o array espelhado eleva o MTTDL para a ordem de milhões de anos.

Garcia-Molina, Ullman e Widom (2009) observam que o MTTDL assume independência estatística entre falhas de discos — uma premissa que pode ser violada na prática por fatores como lotes de fabricação defeituosos, condições ambientais compartilhadas (temperatura, vibração) ou eventos correlacionados (falhas de alimentação elétrica). Modelos de confiabilidade mais sofisticados incorporam fatores de correlação de falha para produzir estimativas de MTTDL mais realistas.

#### 2.3.5 Relação entre as Métricas

| Métrica | Escopo | Aplica-se a | Fórmula Base | Significado Prático |
|---------|--------|-------------|-------------|-------------------|
| MTTF | Componente individual | Não reparáveis | 1/λ | Vida útil esperada até a primeira falha |
| MTTR | Processo de reparo | Reparáveis | Empírica | Janela de vulnerabilidade durante reparo |
| MTBF | Ciclo completo | Reparáveis | MTTF + MTTR | Frequência de interrupções por falha |
| MTTDL | Sistema redundante | Arrays RAID | Depende do nível RAID | Robustez contra perda permanente de dados |

A relação hierárquica entre essas métricas pode ser sintetizada: o **MTTF** e o **MTTR** são métricas de entrada que alimentam o cálculo do **MTBF** (visão de disponibilidade do componente) e do **MTTDL** (visão de durabilidade dos dados do sistema). Um projetista de sistemas de banco de dados deve minimizar o MTTR e maximizar o MTTDL, reconhecendo que ambos os objetivos são alcançados através de redundância adequada, monitoramento proativo e procedimentos de manutenção eficientes.

---

## 3. Técnicas Essenciais de Organização de Dados

As técnicas de organização de dados em arrays de discos constituem os mecanismos fundamentais pelos quais o paralelismo e a redundância, discutidos na seção anterior, são implementados na prática. Silberschatz, Korth e Sudarshan (2019) identificam três categorias principais de técnicas: *data striping* (para paralelismo), *mirroring/shadowing* (para redundância por replicação) e *parity* (para redundância por codificação).

### 3.1 Data Striping

O **data striping** (ou distribuição de dados, ou ainda entrelaceamento) consiste na partição dos dados em unidades de tamanho fixo — denominadas **strips** ou **chunks** — que são distribuídas ciclicamente entre os discos do array. O objetivo é permitir que múltiplos discos processem diferentes porções de uma mesma requisição de I/O simultaneamente, ampliando a largura de banda efetiva.

O tamanho da unidade de distribuição (***strip size*** ou ***chunk size***) é um parâmetro de configuração crítico que determina a granularidade do paralelismo:

- **Strip size pequeno** (bit ou byte): maximiza o paralelismo para requisições individuais grandes, mas introduz overhead de sincronização e torna cada requisição dependente de todos os discos.
- **Strip size grande** (bloco ou múltiplos blocos): permite que requisições pequenas sejam atendidas por um único disco, melhorando o throughput para cargas de trabalho com alta concorrência de requisições independentes.

#### 3.1.1 Bit-Level Striping

No **bit-level striping**, cada byte de dados é decomposto em seus bits individuais, que são distribuídos entre os discos do array. Em um array de 8 discos, por exemplo, o bit *i* de cada byte é armazenado no disco *i* (para *i* = 0, 1, ..., 7).

Conforme Garcia-Molina, Ullman e Widom (2009), o bit-level striping oferece a granularidade máxima de distribuição:

- **Vantagem:** cada operação de leitura de um único byte mobiliza todos os 8 discos simultaneamente, alcançando a taxa de transferência máxima do array para qualquer tamanho de requisição.
- **Desvantagem:** como toda requisição envolve todos os discos, o array não pode atender múltiplas requisições independentes em paralelo (todos os discos estão sempre ocupados com a mesma requisição). Além disso, o overhead de sincronização entre os discos é máximo.

Na prática, o bit-level striping é raramente utilizado em sistemas de armazenamento contemporâneos, sendo encontrado historicamente em implementações de RAID 2 com códigos de Hamming.

#### 3.1.2 Block-Level Striping

O **block-level striping** distribui os dados em unidades de blocos (tipicamente 64 KB, 128 KB ou 256 KB), de modo que blocos consecutivos de um arquivo ou tablespace são armazenados em discos diferentes. Para um array de *n* discos, o bloco lógico *b* é armazenado no disco *b mod n*.

Silberschatz, Korth e Sudarshan (2019) demonstram que o block-level striping oferece o melhor equilíbrio entre paralelismo e concorrência:

- **Para requisições grandes** (e.g., varreduras sequenciais de tabelas): múltiplos blocos consecutivos são lidos em paralelo de discos diferentes, alcançando throughput proporcional ao número de discos.
- **Para requisições pequenas** (e.g., leituras aleatórias de índices B-tree): cada requisição é atendida por um único disco, permitindo que o array processe *n* requisições independentes simultaneamente.

O block-level striping é a técnica de distribuição empregada nos níveis de RAID mais utilizados (RAID 0, 4, 5, 6, 10), sendo a escolha padrão para cargas de trabalho de banco de dados.

### 3.2 Mirroring (Espelhamento) e Shadowing

O **mirroring** (espelhamento) consiste na manutenção de uma cópia idêntica de cada bloco de dados em dois ou mais discos fisicamente distintos. Quando o disco primário recebe uma operação de escrita, o mesmo dado é escrito simultaneamente (ou quase simultaneamente) no disco espelho, garantindo que ambas as cópias permaneçam sincronizadas.

Elmasri e Navathe (2016) detalham as implicações operacionais do mirroring:

- **Leitura:** o controlador pode direcionar requisições de leitura ao disco cujo atuador está mais próximo do setor solicitado, reduzindo a latência média de leitura. Em arrays espelhados com *m* cópias, o throughput de leitura é teoricamente multiplicado por *m*.
- **Escrita:** toda operação de escrita deve ser replicada em todas as *m* cópias, tornando o throughput de escrita limitado pelo disco mais lento. No entanto, com write-back caching, as escritas podem ser confirmadas à aplicação antes da replicação completa, mitigando a penalidade de latência.
- **Custo:** o overhead de armazenamento é de 100% para espelhamento duplo (2 cópias = 50% de eficiência), tornando-o a forma mais cara de redundância.

O termo **shadowing** é frequentemente utilizado como sinônimo de mirroring na literatura, embora alguns autores (notadamente em contextos de mainframe IBM) o distingam como uma forma de replicação assíncrona onde o disco "sombra" (*shadow*) pode estar geograficamente separado do disco primário. No contexto de RAID e armazenamento local, os termos são intercambiáveis.

Em bancos de dados, Silberschatz, Korth e Sudarshan (2019) recomendam o mirroring para **logs de transação** (*redo logs* e *undo logs*), onde a perda de dados pode comprometer a propriedade de durabilidade (D) do modelo ACID. A escrita sequencial dos logs é bem adequada ao padrão de I/O do mirroring, e a criticidade dos logs justifica o custo de 100% de overhead.

### 3.3 Blocos de Paridade

A **paridade** constitui uma técnica de redundância baseada em codificação que permite a reconstrução de dados perdidos utilizando significativamente menos espaço de armazenamento do que o mirroring. O conceito fundamental baseia-se na operação lógica **XOR (OU-Exclusivo)**, que possui a propriedade de reversibilidade:

> Se **P = D₁ ⊕ D₂ ⊕ D₃ ⊕ ... ⊕ Dₙ** (onde ⊕ denota XOR)

Então, para qualquer *Dᵢ* perdido:

> **Dᵢ = D₁ ⊕ D₂ ⊕ ... ⊕ Dᵢ₋₁ ⊕ Dᵢ₊₁ ⊕ ... ⊕ Dₙ ⊕ P**

Ou seja, o dado de qualquer disco individual pode ser reconstruído aplicando-se XOR sobre todos os demais discos de dados e o disco de paridade.

Garcia-Molina, Ullman e Widom (2009) analisam as propriedades de eficiência da paridade:

- **Overhead de armazenamento:** em um grupo de paridade com *n* discos de dados e 1 disco de paridade, o overhead é de apenas 1/n (e.g., 25% para 4 discos de dados, contra 100% do mirroring).
- **Custo computacional:** o cálculo do XOR é uma operação trivial em hardware, podendo ser realizado pelo processador do HBA/controlador RAID sem impacto significativo na CPU do host.
- **Penalidade de escrita (*write penalty*):** a atualização de um único bloco de dados requer a leitura do bloco antigo, a leitura da paridade antiga, o cálculo da nova paridade e a escrita do novo bloco e da nova paridade — totalizando **4 operações de I/O** para cada escrita lógica. Essa penalidade é conhecida como **read-modify-write** e é o principal fator limitante de desempenho em RAID com paridade (RAID 4/5/6).
- **Tolerância a falhas:** a paridade simples (XOR) tolera a falha de **exatamente um disco**. A perda simultânea de dois ou mais discos em um grupo de paridade resulta em perda irrecuperável de dados. Para tolerância a duas falhas, são necessárias técnicas de paridade dupla (e.g., RAID 6 com códigos Reed-Solomon ou paridade P+Q).

Para cargas de trabalho de banco de dados, a escolha entre mirroring e paridade representa um *trade-off* clássico entre custo de armazenamento e desempenho de escrita. Sistemas OLTP com alta taxa de escritas aleatórias tendem a favorecer mirroring (RAID 1/10), enquanto sistemas de leitura intensiva ou data warehouses podem beneficiar-se da eficiência de armazenamento da paridade (RAID 5/6).

---

## 4. Estudo Comparativo dos Níveis de RAID Padrão

O acrônimo **RAID** — originalmente **Redundant Array of Inexpensive Disks**, posteriormente reinterpretado como **Redundant Array of Independent Disks** — foi formalizado por Patterson, Gibson e Katz na Universidade de Berkeley em 1988. A taxonomia original definia cinco níveis (RAID 1 a 5), posteriormente expandida pela indústria e pela academia para incluir os níveis 0, 6 e combinações aninhadas.

Conforme Silberschatz, Korth e Sudarshan (2019, Cap. 12), cada nível RAID representa uma combinação específica de técnicas de striping e redundância, otimizada para diferentes perfis de carga de trabalho. A análise detalhada de cada nível é apresentada a seguir.

### 4.1 RAID 0 — Striping sem Redundância

#### Arquitetura

O RAID 0 implementa **block-level striping puro** sem qualquer forma de redundância. Os dados são distribuídos em strips de tamanho configurável (tipicamente 64 KB a 256 KB) entre todos os *n* discos do array, de forma cíclica (*round-robin*). Cada bloco lógico *b* é armazenado no disco *b mod n*, na posição *⌊b/n⌋* dentro do disco.

#### Vantagens

- **Desempenho máximo:** tanto leituras quanto escritas beneficiam-se do paralelismo total de *n* discos, alcançando throughput teórico de *n* × taxa individual.
- **Eficiência de armazenamento de 100%:** toda a capacidade dos discos é utilizada para dados úteis, sem overhead de redundância.
- **Sem penalidade de escrita:** escritas não requerem cálculos de paridade ou replicação.
- **Simplicidade de implementação:** não requer lógica de reconstrução ou cálculos de redundância.

#### Desvantagens

- **Zero tolerância a falhas:** a falha de qualquer disco individual resulta em perda total dos dados do array.
- **MTTF degradado:** o MTTF do sistema é MTTF_disco/n, diminuindo linearmente com o número de discos.
- **Inadequado para dados de produção:** a ausência de redundância torna o RAID 0 inaceitável para armazenamento de dados de valor permanente.

#### Recomendações de Uso em BD

O RAID 0 é apropriado exclusivamente para dados **descartáveis** ou **facilmente reproduzíveis**: espaço temporário de ordenação (*temp tablespace*), caches de resultado intermediário em processamento analítico, e ambientes de desenvolvimento/teste onde a perda de dados não tem impacto operacional. Nunca deve ser utilizado para tablespaces de dados persistentes, logs de transação ou backups.

### 4.2 RAID 1 — Mirroring

#### Arquitetura

O RAID 1 implementa **espelhamento completo** sem striping: cada disco de dados possui uma cópia idêntica (disco espelho). Em uma configuração de *n* discos, os dados são armazenados em *n/2* pares espelhados, onde cada par contém os mesmos dados.

As operações de escrita são propagadas a ambos os discos do par simultaneamente. As operações de leitura podem ser distribuídas entre os dois discos do par, permitindo ao controlador escolher o disco com menor latência estimada.

#### Vantagens

- **Tolerância a falhas robusta:** o sistema sobrevive à falha de um disco em cada par espelhado. Em princípio, até *n/2* discos podem falhar simultaneamente sem perda de dados, desde que nenhum par perca ambos os discos.
- **Desempenho de leitura elevado:** as leituras podem ser distribuídas entre os dois espelhos, efetivamente dobrando o throughput de leitura.
- **Reconstrução rápida:** a reconstrução após uma falha consiste em uma cópia direta do disco sobrevivente para o disco substituto, sem cálculos complexos.
- **Sem penalidade de escrita por paridade:** embora as escritas sejam duplicadas, não há cálculo de XOR ou read-modify-write.

#### Desvantagens

- **Eficiência de armazenamento de 50%:** metade da capacidade total do array é consumida por cópias espelho.
- **Custo elevado:** para uma dada capacidade útil, requer o dobro de discos em relação ao RAID 0.
- **Throughput de escrita limitado:** cada operação de escrita é executada em dois discos, limitando a taxa de escrita à velocidade de um único disco (embora a latência individual de cada escrita não seja impactada com write-back caching).

#### Recomendações de Uso em BD

Silberschatz, Korth e Sudarshan (2019) recomendam o RAID 1 para **logs de transação** (redo/undo logs), onde a combinação de alta taxa de escritas sequenciais, criticidade extrema dos dados e necessidade de recuperação rápida justifica o custo de armazenamento. Também é adequado para bancos de dados de pequeno porte com requisitos rigorosos de disponibilidade.

### 4.3 RAID 2 — Bit-Level Striping com ECC (Hamming)

#### Arquitetura

O RAID 2 emprega **bit-level striping** combinado com **códigos de correção de erros de Hamming**. Os bits de cada byte são distribuídos entre os discos de dados, e discos adicionais armazenam os bits de paridade calculados segundo o código de Hamming, permitindo a detecção e correção de erros de um bit e a detecção de erros de dois bits.

Para um grupo de *d* discos de dados, são necessários *⌈log₂(d)⌉ + 1* discos de paridade. Por exemplo, 4 discos de dados requerem 3 discos de paridade (total de 7 discos).

#### Vantagens

- **Detecção e correção de erros em tempo real:** capaz de identificar e corrigir erros de bit sem interrupção do serviço.
- **Alta taxa de transferência para leituras sequenciais:** o bit-level striping distribui cada transferência entre todos os discos.

#### Desvantagens

- **Redundância elevada para poucos discos:** o overhead de armazenamento é significativo (e.g., 43% para 4+3 discos).
- **Obsolescência prática:** os discos modernos já incorporam mecanismos de ECC internos (códigos de Reed-Solomon no nível do setor), tornando o ECC externo do RAID 2 redundante.
- **Complexidade de implementação:** requer sincronização rotacional precisa entre todos os discos (spinning synchronization).
- **Sem concorrência de I/O:** como toda requisição envolve todos os discos, requisições independentes não podem ser processadas em paralelo.

#### Recomendações de Uso em BD

O RAID 2 é **historicamente obsoleto** e não é empregado em sistemas de banco de dados contemporâneos. Seu interesse é puramente acadêmico, como ilustração dos princípios de codificação de erros aplicados a arrays de discos.

### 4.4 RAID 3 — Byte-Level Striping com Paridade Dedicada

#### Arquitetura

O RAID 3 utiliza **byte-level striping** entre os discos de dados, com um disco dedicado exclusivamente ao armazenamento de informações de paridade (XOR). Todos os discos devem operar de forma sincronizada (spindle-locked), pois cada operação de I/O acessa todos os discos simultaneamente.

Em um array RAID 3 com *n* discos de dados e 1 disco de paridade, o byte *b* é armazenado no disco *b mod n*, e o disco de paridade armazena o XOR dos bytes correspondentes de todos os discos de dados.

#### Vantagens

- **Alta taxa de transferência sequencial:** ideal para grandes transferências contínuas, alcançando throughput agregado de *n* × taxa individual.
- **Eficiência de armazenamento:** overhead de apenas 1 disco de paridade, independente do número de discos de dados.
- **Tolerância à falha de um disco:** qualquer disco individual (dados ou paridade) pode ser reconstruído a partir dos demais.

#### Desvantagens

- **Inadequado para I/O aleatório:** como toda operação envolve todos os discos, o array não pode processar múltiplas requisições pequenas em paralelo.
- **Gargalo no disco de paridade:** todas as escritas atualizam o disco de paridade, tornando-o potencialmente um ponto de contenção.
- **Requer sincronização rotacional:** a necessidade de spindle-lock entre todos os discos aumenta a complexidade e o custo.

#### Recomendações de Uso em BD

O RAID 3 é apropriado para cargas de trabalho com **predominância de leitura/escrita sequencial de grandes volumes** — como data warehouses com operações de carga (*bulk load*), streaming de dados multimídia ou processamento de imagens médicas em bancos de dados PACS. É inadequado para OLTP ou qualquer carga com I/O aleatório significativo.

### 4.5 RAID 4 — Block-Level Striping com Paridade Dedicada

#### Arquitetura

O RAID 4 emprega **block-level striping** entre os discos de dados, com um disco dedicado ao armazenamento de blocos de paridade. Cada strip (bloco) é armazenado integralmente em um único disco, e o disco de paridade armazena o XOR dos blocos correspondentes de todos os discos de dados.

A diferença fundamental em relação ao RAID 3 é a granularidade: enquanto o RAID 3 distribui bytes entre discos (exigindo acesso simultâneo a todos os discos para qualquer operação), o RAID 4 distribui blocos inteiros, permitindo que requisições de leitura de blocos individuais sejam atendidas por um único disco.

#### Vantagens

- **Leituras aleatórias independentes:** requisições de leitura que envolvem um único bloco podem ser atendidas por um disco individual, permitindo paralelismo de requisições.
- **Boa taxa de transferência sequencial:** leituras de múltiplos blocos consecutivos mobilizam todos os discos de dados em paralelo.
- **Eficiência de armazenamento:** overhead de apenas 1/(n+1) para *n* discos de dados + 1 de paridade.

#### Desvantagens

- **Gargalo de escrita no disco de paridade:** toda operação de escrita requer atualização do disco de paridade, criando um ponto de contenção severo (*write bottleneck*). Para *n* discos de dados gerando escritas simultâneas, todas convergem para o único disco de paridade.
- **Write penalty de 4 I/Os:** cada escrita de um bloco requer: leitura do bloco antigo, leitura da paridade antiga, escrita do novo bloco e escrita da nova paridade.
- **Escritas aleatórias severamente penalizadas:** a combinação do gargalo do disco de paridade com o write penalty torna o RAID 4 extremamente lento para cargas de escrita aleatória.

#### Recomendações de Uso em BD

O RAID 4 é adequado para cargas de trabalho com **alta proporção de leituras** e escritas predominantemente sequenciais, como data warehouses read-mostly e sistemas de arquivamento. Na prática, o RAID 5 (que distribui a paridade entre todos os discos) é quase universalmente preferido ao RAID 4, pois elimina o gargalo do disco de paridade dedicado.

### 4.6 RAID 5 — Block-Level Striping com Paridade Distribuída

#### Arquitetura

O RAID 5 aprimora o RAID 4 ao **distribuir os blocos de paridade entre todos os discos** do array, em vez de concentrá-los em um disco dedicado. A distribuição segue um padrão rotacional (e.g., *left-symmetric*, *left-asymmetric*, *right-symmetric*, *right-asymmetric*) que garante que cada disco armazena aproximadamente a mesma quantidade de dados e paridade.

Em um array de *n* discos, para cada grupo de *n-1* strips de dados, o strip de paridade é armazenado em um disco diferente do grupo anterior. Isso distribui uniformemente a carga de escritas de paridade entre todos os discos, eliminando o gargalo do RAID 4.

#### Vantagens

- **Eliminação do gargalo de paridade:** a distribuição da paridade permite que escritas concorrentes em strips de grupos diferentes sejam processadas em paralelo, pois atualizam discos de paridade distintos.
- **Boa eficiência de armazenamento:** capacidade útil de *(n-1)/n*, equivalente ao RAID 4. Para 5 discos, a eficiência é de 80%.
- **Desempenho de leitura aleatória:** similar ao RAID 4, com leituras de blocos individuais atendidas por um único disco.
- **Equilíbrio custo-benefício:** amplamente considerado o melhor compromisso entre desempenho, redundância e custo para muitas cargas de trabalho.

#### Desvantagens

- **Write penalty persiste:** cada escrita aleatória de um bloco ainda requer 4 operações de I/O (read-modify-write), embora sem o gargalo de um único disco.
- **Tolerância a apenas uma falha:** a perda simultânea de dois discos resulta em perda de dados, o que se torna uma preocupação crescente com discos de grande capacidade e tempos de reconstrução longos.
- **Degradação durante reconstrução:** após a falha de um disco, o array opera em modo degradado com desempenho significativamente reduzido, e qualquer falha adicional durante esse período resulta em perda de dados (*RAID 5 write hole*).

#### Recomendações de Uso em BD

O RAID 5 é amplamente utilizado em bancos de dados de leitura intensiva, data warehouses, sistemas de apoio à decisão (DSS) e aplicações com proporção moderada de escritas. Silberschatz, Korth e Sudarshan (2019) recomendam cautela com RAID 5 para discos de grande capacidade (>2 TB), onde o tempo de reconstrução elevado aumenta significativamente o risco de uma segunda falha durante o rebuild. Para esses cenários, RAID 6 é preferível.

### 4.7 RAID 6 — Block-Level Striping com Paridade Dupla Distribuída

#### Arquitetura

O RAID 6 estende o conceito do RAID 5 adicionando um **segundo esquema de paridade independente**, permitindo a sobrevivência à falha simultânea de **dois discos**. Os dois blocos de paridade são tipicamente denominados **P** (paridade XOR convencional) e **Q** (paridade diagonal, baseada em códigos Reed-Solomon ou em aritmética sobre campos de Galois — GF(2⁸)).

Em um array de *n* discos, cada grupo de strips contém *n-2* strips de dados, 1 strip de paridade P e 1 strip de paridade Q. Assim como no RAID 5, os blocos P e Q são distribuídos rotativamente entre todos os discos.

#### Vantagens

- **Tolerância a duas falhas simultâneas:** o array sobrevive à perda de quaisquer dois discos sem perda de dados, oferecendo um MTTDL ordens de grandeza superior ao RAID 5.
- **Resiliência durante reconstrução:** a existência de redundância dupla permite que o array continue protegido contra uma segunda falha durante o processo de reconstrução de um disco falhado.
- **Escalabilidade segura:** viabiliza arrays de grande porte com muitos discos de alta capacidade, onde a probabilidade de falha dupla durante reconstrução não é desprezível.

#### Desvantagens

- **Write penalty ampliada:** cada escrita aleatória requer **6 operações de I/O** (leitura do bloco antigo, leitura de P antigo, leitura de Q antigo, escrita do novo bloco, escrita de P novo, escrita de Q novo).
- **Menor eficiência de armazenamento:** capacidade útil de *(n-2)/n*. Para 6 discos, a eficiência é de 67% (contra 83% do RAID 5 com o mesmo número de discos).
- **Complexidade computacional:** o cálculo da paridade Q (Reed-Solomon) é significativamente mais complexo que o XOR simples, exigindo suporte de hardware dedicado no controlador RAID.

#### Recomendações de Uso em BD

O RAID 6 é recomendado para bancos de dados de **missão crítica** com discos de grande capacidade (≥4 TB), onde o tempo de reconstrução prolongado torna a janela de vulnerabilidade do RAID 5 inaceitável. É particularmente adequado para data warehouses de grande escala, sistemas de arquivamento de longo prazo e qualquer cenário onde a perda de dados é catastrófica. Elmasri e Navathe (2016) identificam o RAID 6 como o nível mínimo aceitável para arrays com mais de 8 discos em ambientes de produção.

### 4.8 RAID 0+1 (RAID 01) — Striping Espelhado

#### Arquitetura

O RAID 0+1 é um nível **aninhado** que combina RAID 0 (striping) e RAID 1 (mirroring) em uma hierarquia de dois níveis: primeiro aplica-se o striping, e o resultado é então espelhado. Concretamente, dois arrays RAID 0 idênticos são mantidos em sincronia, de modo que um é o espelho do outro.

Em uma configuração típica de 4 discos (A, B, C, D):
- **Array RAID 0 primário:** discos A e B (striping).
- **Array RAID 0 espelho:** discos C e D (cópia idêntica do striping de A+B).

#### Vantagens

- **Alto desempenho de leitura e escrita:** herda o throughput do RAID 0 para ambas as operações.
- **Tolerância a falhas:** sobrevive à falha de um disco, desde que o disco espelho correspondente esteja operacional.
- **Reconstrução simples:** cópia direta do array RAID 0 sobrevivente.

#### Desvantagens

- **Eficiência de armazenamento de 50%:** idêntica ao RAID 1 puro.
- **Vulnerabilidade em caso de falha:** se um disco falha no array primário, todo o array primário é marcado como "falhado", e o sistema depende exclusivamente do array espelho. Uma segunda falha em *qualquer* disco do array espelho resulta em perda de dados. Em contraste, o RAID 10 tolera melhor múltiplas falhas (vide seção seguinte).
- **Reconstrução do array inteiro:** a falha de um disco requer a reconstrução completa do array RAID 0 que continha o disco falhado, copiando todos os dados do espelho — um processo mais lento e intensivo em I/O do que a reconstrução de um único disco no RAID 10.

#### Recomendações de Uso em BD

O RAID 0+1 é menos utilizado que o RAID 10 em ambientes de banco de dados, devido à menor resiliência a falhas múltiplas. Pode ser empregado em cenários onde a simplicidade de gerenciamento é prioritária e o número de discos é pequeno (4 discos).

### 4.9 RAID 10 (RAID 1+0) — Espelhamento Distribuído

#### Arquitetura

O RAID 10 inverte a hierarquia do RAID 0+1: primeiro aplica-se o mirroring, e os pares espelhados são então combinados via striping. Cada par de discos forma um *subarray* RAID 1, e os dados são distribuídos (striped) entre os pares.

Em uma configuração de 4 discos (A, B, C, D):
- **Par espelhado 1:** discos A e B (idênticos).
- **Par espelhado 2:** discos C e D (idênticos).
- **Striping** entre os pares: strips ímpares no par 1, strips pares no par 2.

#### Vantagens

- **Alto desempenho de leitura e escrita:** equivalente ao RAID 0+1, com o benefício adicional de que leituras podem ser distribuídas entre os dois discos de cada par espelhado.
- **Superior tolerância a falhas múltiplas:** o array sobrevive à falha de até *n/2* discos, desde que nenhum par perca ambos os discos. Para 4 discos, existem cenários de duas falhas simultâneas onde o RAID 10 sobrevive e o RAID 0+1 não (e.g., falha de A e D, que estão em pares diferentes).
- **Reconstrução rápida e localizada:** a reconstrução afeta apenas o par espelhado do disco falhado, copiando os dados de um único disco (o espelho sobrevivente) para o disco substituto. O volume de dados copiados é 1/n do array total, contra 1/2 no RAID 0+1.
- **Desempenho mantido durante reconstrução:** apenas o par afetado sofre degradação; os demais pares continuam operando normalmente.

#### Desvantagens

- **Eficiência de armazenamento de 50%:** idêntica ao RAID 1 e ao RAID 0+1.
- **Custo elevado:** requer o dobro de discos para a capacidade desejada.
- **Mínimo de 4 discos:** a configuração mínima funcional requer pelo menos 4 discos.

#### Recomendações de Uso em BD

O RAID 10 é amplamente considerado o **nível ótimo para bancos de dados OLTP de alta performance** (SILBERSCHATZ; KORTH; SUDARSHAN, 2019). A combinação de alto throughput de leitura e escrita, ausência de write penalty por paridade, e tolerância robusta a falhas torna-o ideal para tablespaces de dados transacionais, logs de redo/undo e índices. O custo adicional de armazenamento é justificado pela performance e confiabilidade. Grandes SGBDs como Oracle, SQL Server e PostgreSQL frequentemente recomendam RAID 10 para seus tablespaces primários.

### 4.10 Tabela Comparativa dos Níveis de RAID Padrão

| Nível RAID | Técnica | Discos Mínimos | Eficiência de Armazenamento | Tolerância a Falhas | Leitura Aleatória | Escrita Aleatória | Leitura Sequencial | Escrita Sequencial | Aplicação Ideal em BD |
|------------|---------|----------------|---------------------------|--------------------|--------------------|-------------------|--------------------|--------------------|----------------------|
| **RAID 0** | Block striping | 2 | 100% | Nenhuma | ★★★★★ | ★★★★★ | ★★★★★ | ★★★★★ | Temp tablespace, scratch data |
| **RAID 1** | Mirroring | 2 | 50% | 1 disco por par | ★★★★ | ★★★ | ★★★ | ★★★ | Logs de transação, BD pequenos |
| **RAID 2** | Bit striping + Hamming | 7 (4+3) | ~57% | 1 disco (correção), 2 discos (detecção) | ★★ | ★★ | ★★★★ | ★★★ | Obsoleto — interesse acadêmico |
| **RAID 3** | Byte striping + paridade dedicada | 3 | (n-1)/n | 1 disco | ★★ | ★★ | ★★★★★ | ★★★★ | DW com bulk load, streaming |
| **RAID 4** | Block striping + paridade dedicada | 3 | (n-1)/n | 1 disco | ★★★★ | ★★ | ★★★★★ | ★★★ | DW read-mostly (substituído por RAID 5) |
| **RAID 5** | Block striping + paridade distribuída | 3 | (n-1)/n | 1 disco | ★★★★ | ★★★ | ★★★★★ | ★★★★ | DW, DSS, BD leitura-intensiva |
| **RAID 6** | Block striping + paridade dupla distribuída | 4 | (n-2)/n | 2 discos | ★★★★ | ★★ | ★★★★★ | ★★★ | BD missão-crítica, arrays grandes |
| **RAID 0+1** | Striping + mirroring | 4 | 50% | 1 disco (vulnerável a 2ª falha no espelho) | ★★★★★ | ★★★★ | ★★★★★ | ★★★★ | Cargas mistas com poucos discos |
| **RAID 10** | Mirroring + striping | 4 | 50% | 1 disco por par (até n/2) | ★★★★★ | ★★★★★ | ★★★★★ | ★★★★★ | **OLTP, BD transacionais, logs** |

> **Legenda:** ★ = Baixo → ★★★★★ = Excelente. Classificação relativa entre os níveis RAID, considerando arrays com número equivalente de discos.

---

## 5. Níveis de RAID Não Padrão e Proprietários

Além dos níveis padronizados pela academia e pela indústria, diversas organizações desenvolveram variações proprietárias ou experimentais de RAID, visando atender requisitos específicos de desempenho, confiabilidade ou eficiência que os níveis padrão não satisfazem adequadamente. Esta seção apresenta cinco desses níveis não padronizados.

### 5.1 RAID 1.5

O **RAID 1.5** é uma variante proprietária que combina simultaneamente **mirroring** e **striping** em uma única operação atômica, diferindo do RAID 10 na forma de implementação. No RAID 1.5, os dados são simultaneamente espelhados e distribuídos (*striped*) entre os discos, de modo que o controlador realiza a escrita espelhada e a distribuição como uma operação indivisível, em vez de empilhar dois níveis RAID hierárquicos.

**Funcionamento:** O controlador RAID 1.5 recebe um bloco de dados e, em uma única operação:
1. Divide o bloco em strips.
2. Distribui os strips entre os discos pares (0, 2, 4, ...).
3. Replica cada strip nos discos ímpares correspondentes (1, 3, 5, ...).

A diferença operacional em relação ao RAID 10 reside na **transparência da hierarquia** para o controlador: enquanto no RAID 10 o controlador gerencia explicitamente subarrays RAID 1 combinados por RAID 0, no RAID 1.5 o algoritmo de distribuição e espelhamento é integrado em um único nível lógico.

**Casos de uso:** O RAID 1.5 é encontrado em controladores RAID específicos de alguns fabricantes de hardware empresarial. É empregado em cenários similares ao RAID 10 — bancos de dados transacionais de alta performance — com a vantagem potencial de menor overhead de gerenciamento interno do controlador, dependendo da implementação.

### 5.2 RAID 7

O **RAID 7** é uma arquitetura proprietária desenvolvida pela **Storage Computer Corporation** (posteriormente adquirida pela Borg Adaptive Technologies), que estende o conceito de RAID com a integração de um **sistema operacional em tempo real embarcado** no controlador RAID e um **cache de escrita de alta capacidade com proteção por UPS**.

**Funcionamento:**
- A arquitetura base é similar ao RAID 3 ou RAID 4 (striping com paridade dedicada), mas a inteligência do sistema é significativamente ampliada.
- Um **processador dedicado** executa um sistema operacional em tempo real que gerencia: otimização de I/O assíncrono, cache hierárquico de leitura e escrita, e scheduling avançado de operações de disco.
- O **cache de escrita assíncrono**, protegido por UPS (Uninterruptible Power Supply), permite que operações de escrita sejam confirmadas à aplicação imediatamente, sem esperar pela gravação física no disco, eliminando efetivamente o write penalty da paridade.
- A paridade é calculada e escrita de forma assíncrona pelo processador do controlador, a partir dos dados no cache.

**Vantagens:**
- Desempenho de escrita dramaticamente superior ao RAID 3/4/5, pois as escritas são absorvidas pelo cache com latência de memória.
- Processamento de I/O autônomo que reduz a carga na CPU do host.
- Alto throughput tanto para operações sequenciais quanto aleatórias.

**Desvantagens:**
- Solução proprietária e de alto custo.
- Dependência da proteção por UPS para integridade do cache de escrita.
- Produto descontinuado, sem suporte ativo.

**Casos de uso:** O RAID 7 foi projetado para servidores de banco de dados e aplicações de missão crítica que demandavam altíssimo throughput de I/O nas décadas de 1990-2000. Embora o produto original tenha sido descontinuado, os conceitos de cache assíncrono protegido e processamento de I/O inteligente são hoje incorporados em controladores RAID empresariais modernos.

### 5.3 RAID DP (Double Parity)

O **RAID DP (Double Parity)** é a implementação proprietária da **NetApp** do conceito de paridade dupla, funcionalmente similar ao RAID 6 mas com otimizações específicas para o sistema de arquivos proprietário **WAFL (Write Anywhere File Layout)** utilizado nos equipamentos NetApp (agora parte da linha ONTAP).

**Funcionamento:**
- O RAID DP utiliza dois discos de paridade por grupo RAID: um disco armazena a paridade horizontal (P, equivalente ao XOR do RAID 5) e o outro armazena a paridade diagonal (DP), calculada sobre combinações diagonais dos blocos de dados.
- A inovação reside na integração com o WAFL, que é um sistema de arquivos *log-structured*: escritas são sempre realizadas em novos blocos (write-anywhere), eliminando a necessidade do ciclo read-modify-write típico do RAID 5/6. Novas paridades P e DP são calculadas a partir dos novos dados escritos, sem necessidade de ler os dados ou paridades antigos.
- Essa otimização reduz o write penalty de 6 I/Os (RAID 6 convencional) para efetivamente **1 I/O de escrita + cálculos de paridade em background**.

**Vantagens:**
- Write penalty significativamente inferior ao RAID 6 convencional, graças à integração com o WAFL.
- Tolerância a duas falhas simultâneas de disco.
- Suporte a arrays de grande porte (até 28 discos por grupo RAID DP) com alta eficiência de armazenamento.

**Casos de uso:** O RAID DP é amplamente utilizado em sistemas de armazenamento **NetApp FAS/AFF** para bancos de dados Oracle, SQL Server e SAP HANA. É a configuração padrão de proteção nos sistemas NetApp e representa uma escolha madura para bancos de dados corporativos hospedados em infraestrutura NetApp.

### 5.4 RAID S

O **RAID S** é uma variante proprietária desenvolvida pela **EMC Corporation** (agora Dell EMC) para seus sistemas de armazenamento **Symmetrix** (posteriormente rebatizados como **VMAX** e **PowerMax**). O RAID S implementa uma forma de espelhamento otimizada para a arquitetura cache-centric dos arrays Symmetrix.

**Funcionamento:**
- O RAID S é essencialmente um RAID 1 (mirroring) aprimorado pela arquitetura de **cache global** dos arrays Symmetrix.
- Todas as operações de escrita são realizadas inicialmente no **cache DRAM** do array (protegido por baterias e espelhado entre controladores), sendo confirmadas à aplicação imediatamente.
- O *destaging* (escrita do cache para os discos) é realizado de forma assíncrona pelo firmware do array, que otimiza a ordem e o agrupamento das escritas para maximizar o throughput dos discos.
- O espelhamento é gerenciado no nível do controlador do array, que pode utilizar técnicas como **write folding** (coalescência de múltiplas escritas ao mesmo bloco em uma única operação de destage) para reduzir a carga nos discos.

**Vantagens:**
- Desempenho de escrita excepcionalmente alto, limitado pela velocidade do cache (DRAM) e não dos discos.
- Simplicidade conceitual do mirroring com a performance de uma arquitetura cache-first.
- Integração otimizada com o firmware e o hardware proprietário EMC.

**Casos de uso:** O RAID S é empregado em ambientes de **banco de dados de altíssima performance** hospedados em arrays de armazenamento Dell EMC de alto nível (Symmetrix VMAX, PowerMax). É comumente utilizado para bancos de dados Oracle RAC, SAP HANA e cargas OLTP de grande escala em ambientes corporativos.

### 5.5 Matrix RAID

O **Matrix RAID** é uma tecnologia desenvolvida pela **Intel** como parte de sua plataforma **Intel Rapid Storage Technology (RST)**, projetada para permitir que um **único conjunto de discos físicos** hospede **múltiplos volumes RAID com níveis diferentes**.

**Funcionamento:**
- Em uma configuração Matrix RAID típica com dois discos físicos, o espaço de armazenamento de cada disco é dividido em duas partições:
  - A **primeira partição** de cada disco é combinada em um volume **RAID 0** (striping), otimizado para desempenho.
  - A **segunda partição** de cada disco é combinada em um volume **RAID 1** (mirroring), otimizado para confiabilidade.
- O controlador Intel ICH/PCH (chipset) gerencia ambos os volumes simultaneamente, apresentando-os ao sistema operacional como unidades lógicas distintas.
- O sistema operacional pode, por exemplo, instalar o sistema e aplicações no volume RAID 0 (onde a velocidade é prioritária e os dados são facilmente reinstalados) e armazenar dados de banco de dados no volume RAID 1 (onde a proteção contra falhas é essencial).

**Vantagens:**
- Permite combinar desempenho e redundância com apenas 2 discos, sem a necessidade de 4 discos (como o RAID 10).
- Flexibilidade de alocação: o administrador define o tamanho de cada volume e o nível RAID correspondente.
- Integrado ao chipset Intel, sem necessidade de controlador RAID discreto.

**Desvantagens:**
- Limitado a 2-3 discos na maioria das implementações.
- Performance inferior a controladores RAID discretos (hardware RAID).
- Implementação via firmware/driver (pseudo-hardware RAID), com potenciais limitações de funcionalidade e desempenho.

**Casos de uso:** O Matrix RAID é uma solução para **estações de trabalho de desenvolvimento** e **pequenos servidores de banco de dados** que dispõem de poucos discos e necessitam combinar uma partição de alta performance (para temp tablespace, scratch data) com uma partição protegida (para dados persistentes de banco de dados). Não é adequado para ambientes corporativos ou de alta disponibilidade.

---

## Considerações Finais

A escolha da arquitetura de armazenamento físico — desde a interface de conexão (SATA, SAS, USB) até o nível de RAID empregado — constitui uma decisão arquitetural de impacto direto sobre o desempenho, a disponibilidade e a escalabilidade de um sistema de banco de dados. Conforme demonstrado ao longo deste estudo, não existe uma solução universalmente ótima; cada combinação de técnicas de striping, mirroring e paridade representa um ponto específico no espaço de *trade-offs* entre custo, performance e confiabilidade.

As referências de Silberschatz, Korth e Sudarshan (2019), Elmasri e Navathe (2016) e Garcia-Molina, Ullman e Widom (2009) convergem na recomendação de que o projetista de banco de dados deve:

1. **Caracterizar a carga de trabalho** (proporção leitura/escrita, padrão sequencial/aleatório, criticidade dos dados) antes de selecionar o nível RAID.
2. **Dimensionar a redundância** em função do MTTDL desejado, considerando o tamanho do array, a capacidade dos discos e o MTTR esperado.
3. **Avaliar o custo total de propriedade** (TCO), incluindo não apenas o custo de aquisição dos discos, mas também o consumo energético, o espaço físico e o custo operacional de manutenção.
4. **Considerar soluções híbridas** — como RAID 10 para tablespaces transacionais e RAID 6 para tablespaces de leitura ou arquivamento — para otimizar cada componente do banco de dados de acordo com seu perfil de acesso.

O avanço das tecnologias de armazenamento — incluindo SSDs NVMe, Storage Class Memory (SCM) e interfaces de alta velocidade como USB4 e CXL (*Compute Express Link*) — continuará a transformar o panorama do armazenamento físico, exigindo do profissional de banco de dados uma atualização constante dos fundamentos aqui apresentados.

---

## Referências Bibliográficas

1. ELMASRI, R.; NAVATHE, S. B. **Fundamentals of Database Systems**. 7. ed. Hoboken: Pearson, 2016.

2. GARCIA-MOLINA, H.; ULLMAN, J. D.; WIDOM, J. **Database Systems: The Complete Book**. 2. ed. Upper Saddle River: Pearson Prentice Hall, 2009.

3. PATTERSON, D. A.; GIBSON, G. A.; KATZ, R. H. A Case for Redundant Arrays of Inexpensive Disks (RAID). In: **ACM SIGMOD International Conference on Management of Data**, 1988, Chicago. Proceedings. New York: ACM, 1988. p. 109–116.

4. SILBERSCHATZ, A.; KORTH, H. F.; SUDARSHAN, S. **Database System Concepts**. 7. ed. New York: McGraw-Hill Education, 2019.
