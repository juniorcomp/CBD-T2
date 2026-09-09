# Tabelas — cópia de trabalho

A coleta acontece **aqui**, em Markdown. A transcrição para
`../relatorio/main.tex` acontece **uma vez**, depois de a tabela ser aceita.
Transcrever duas vezes é onde os números divergem.

Regras de coleta: [GUIA.md](GUIA.md). Convenções: [../DECISOES.md](../DECISOES.md).

**Toda célula com número ou conceito técnico denso leva uma nota `[n]` apontando 
para a fonte na lista do fim de cada tabela**, com livro, edição, seção/página 
e data de acesso.

---

## Tabela 1 — Comparativo de Interfaces para DAS

Roteiro, parte 1. Serve para o texto da Etapa 2 e para os primeiros slides de comparativo.

| Interface | Taxa de Transf. (Típica) | Custo | Confiabilidade (MTBF relativo) | Recomendação SBD |
|---|---|---|---|---|
| **SATA** [1] | 600 MB/s (SATA III) | Baixo | Menor (projetado para desktop) | Armazenamento frio, arquivamento ou volumes não críticos [2] |
| **eSATA** [1] | 600 MB/s | Baixo | Menor | Obsoleto/raro em SBD, superado pelo USB 3.0 para conexões externas |
| **SAS** [3] | 1200 MB/s a 22.5 GB/s (SAS-4) | Alto | Muito Alta (comandos SCSI, full-duplex) | Carga analítica (OLAP) e transacional (OLTP) corporativa pesada. Tier 1. |
| **USB 3/4** [1] | 5 Gbps a 40 Gbps | Baixo | Baixa (overhead de protocolo, não otimizado para I/O constante) | Backups pontuais offline, transferência temporária. Nunca para dados live [2] |
| **Firewire** [1] | 400 a 800 Mbps | Alto (histórico) | Média | Obsoleto. Antiga alternativa para daisy-chaining antes do avanço do USB/Thunderbolt |
| **HBA (Host Bus Adapter)** [3] | Depende do barramento PCIe | Alto | Muito Alta | Fundamental para SBDs robustos: desonera a CPU do host da carga de gerenciar discos e RAIDs de hardware [4] |

> **Nota:** SAS e SATA usam conectores fisicamente compatíveis, mas eletricamente 
> e logicamente diferentes. Um controlador SAS aceita discos SATA (barateando custos 
> de armazenamento secundário), mas um controlador SATA *não* aceita discos SAS [3].

**Fontes:**

1. [PREENCHER COM FONTE - ex: Silberschatz Cap. 12]
2. [PREENCHER COM FONTE - recomendação de uso em SBD]
3. [PREENCHER COM FONTE - Especificação SAS ou Livro]
4. [PREENCHER COM FONTE - Elmasri/Garcia-Molina sobre desoneração de CPU]

---

## Tabela 2 — Comparativo de Níveis de RAID Padrão

Roteiro, parte 2. Serve para estruturar a principal tabela do relatório (Etapa 3).

| Nível de RAID | Mecanismo Principal | Capacidade Útil | MTTR (Reconstrução) | Tolerância a Falha (MTTDL base) | Vantagem para SBD | Desvantagem para SBD |
|---|---|---|---|---|---|---|
| **RAID 0** [1] | Block-level striping | $N \times C$ | N/A (Se falhar, perdeu) | Zero (Falha de 1 disco = falha total) | Máximo paralelismo de I/O | Nenhuma confiabilidade [1] |
| **RAID 1** [1] | Mirroring | $\frac{N}{2} \times C$ | Rápido (Cópia direta) | 1 disco (ou mais, dependendo dos pares) | Leitura paralela, escrita segura | Custo alto de armazenamento (50% perdido) |
| **RAID 5** [2] | Block-level striping c/ Paridade distribuída | $(N-1) \times C$ | Muito Lento (Requer ler todos os discos para recalcular a paridade) | 1 disco | Bom balanço entre capacidade útil e redundância | *Write penalty* alto. Em discos grandes modernos (10TB+), a reconstrução pode demorar dias e causar uma 2ª falha [2] |
| **RAID 6** [2] | Block-level striping c/ Dupla paridade | $(N-2) \times C$ | Extremamente Lento | 2 discos | Sobrevive a Unrecoverable Read Errors (URE) durante reconstrução do disco 1 | Pior *write penalty* que o RAID 5. Exige hardware forte para cálculo de Reed-Solomon |
| **RAID 10** [3] | Striping sobre espelhos (1+0) | $\frac{N}{2} \times C$ | Rápido | 1 disco por subgrupo espelhado | Excelente performance de escrita (sem cálculo de paridade) | Custo altíssimo [3] |

> **Atenção:** Em capacidade útil, $N$ é o número de discos e $C$ é a capacidade do 
> menor disco no array. Fórmulas de confiabilidade aprofundadas (como cálculo exato 
> de MTBF do array) devem ir para o texto em si, mas os *insights* (como a lentidão 
> do MTTR do RAID 5) ancoram as vantagens/desvantagens aqui.

**Fontes:**

1. [PREENCHER - ex: Silberschatz Seção 12.5.X]
2. [PREENCHER - ex: Elmasri Seção 16.10.X sobre penalidade de paridade]
3. [PREENCHER - ex: Garcia-Molina sobre RAID Híbrido]

---
