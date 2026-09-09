---
theme: academic
title: Arquiteturas de Armazenamento Físico para SBD
info: |
  Trabalho 2 — Construção de Bancos de Dados (UFRJ)
mdc: true
layout: cover
coverDate: ''
fonts:
  sans: 'Lato'
  serif: 'EB Garamond'
  mono: 'Fira Code'
  weights: '300,400,700'
---

# Arquiteturas de Armazenamento Físico para SBD

DAS, Interfaces e Níveis de RAID

<div class="pt-3 text-sm leading-relaxed">

Daniel Rebouças de Sousa Barros — DRE 123273542<br />
Hugo Leandro Antunes — DRE 123143543<br />
Jeson Wen Chen — DRE 123051883<br />
João Batista Brasil Junior — DRE 121091172<br />
Pedro Cintra Silveira — DRE 123419342

</div>

<div class="pt-2 text-xs opacity-60 leading-relaxed">

Universidade Federal do Rio de Janeiro — Construção de Bancos de Dados<br />
Prof. Milton Ramirez — Setembro de 2026

</div>

<!--
Folha de rosto, obrigatória pelo enunciado: nomes completos e DREs.
Comentário no FIM do slide = nota do apresentador. Tecle "p" para vê-las.
-->

---
layout: default
---

# Roteiro

<v-clicks>

- O Gargalo de Armazenamento
- Interfaces DAS (Direct Attached Storage)
- Fundamentos de RAID (Paralelismo e Redundância)
- Níveis de RAID Padronizados e Proprietários
- Métricas e Confiabilidade
- Conclusões

</v-clicks>

<!--
<v-clicks> revela os itens um a um. Para um bloco só, use <v-click>.
-->

---
layout: section
---

# 1. Conceitos Fundamentais

---

# O Gargalo de Armazenamento

Por que precisamos de paralelismo?

<v-clicks>

- Evolução díspar: CPU e RAM evoluem mais rápido que HDDs mecânicos.
- O I/O limita o desempenho de transações (OLTP) e análises (OLAP).
- **A Solução Física:** Agrupamento de discos operando em paralelo.
- **O Desafio Estatístico:** Probabilidade de falha escala com número de discos.
- **A Resposta Arquitetural:** RAID (Redundant Arrays of Independent Disks).

</v-clicks>

<!--
Introduzir a motivação do artigo seminal de Patterson, Gibson e Katz (1988).
-->

---

# DAS (Direct Attached Storage)

<div class="grid grid-cols-2 gap-8 pt-2">

<div v-click>

### O que é?

- Conexão física ponto-a-ponto entre o *host* e os discos.
- Sem intermediação de redes dedicadas (como SAN/NAS).
- Camada de armazenamento mais comum em nós individuais de banco de dados.

</div>

<div v-click>

### Qual a importância no SBD?

- Define a vazão bruta e a latência de acesso aos blocos de dados.
- O SBD se apoia diretamente na **Interface** (e.g. SAS) e no adaptador (HBA).

</div>

</div>

<!--
Diferenciação clara entre armazenamento de rede e armazenamento direto (DAS).
-->

---
layout: section
---

# 2. Interfaces DAS

---

# Comparativo de Interfaces para SBD

<div class="text-sm">

| Interface | Taxa Máx. | Duplex | Integridade | Uso típico em SBD |
|---|---|---|---|---|
| **SATA III** | 6 Gbps | Half | CRC Básico | Servidores de entrada |
| **SAS-3** | 12 Gbps | Full | T10-PI fim-a-fim | OLTP, Missão crítica |
| **USB 3.2** | 20 Gbps | Full | CRC | Bancos embarcados |
| **USB4 v2** | 80 Gbps | Full | CRC | Storage externo (desenv.) |

</div>

<Footnotes separator>
  <Footnote :number=1>Os detalhes de FileWire e eSATA estão no relatório, bem como limites exatos de comandos pendentes.</Footnote>
</Footnotes>

<!--
Recorte de colunas: mostre apenas o que define por que SAS é superior ao SATA em SBD (Full Duplex + Proteção fim-a-fim).
-->

---

# O HBA (Host Bus Adapter) e Controladoras

<v-clicks>

- **HBA Passthrough:** Expõe os discos de forma bruta ao SO, delegando o RAID ao software (mdadm, ZFS).
- **Controladora RAID por Hardware:** Oculta complexidade e expõe LUNs virtuais.
- **Battery Backup Unit (BBU):** Bateria atrelada ao cache de escrita.
- **Impacto no WAL:** A BBU permite confirmar escritas em disco (*commit*) sem esperar a agulha física gravar, mitigando latência em OLTP.

</v-clicks>

<!--
Conexão HBA vs. SGBD: o WAL (write-ahead log) vive em harmonia com o BBU do HBA hardware.
-->

---
layout: section
---

# 3. Fundamentos de RAID

---

# Paralelismo: Striping (Distribuição)

<div class="grid grid-cols-2 gap-8 pt-2">

<div v-click>

### Nível de Bit (Bit-level)
- Divide os bits de 1 byte pelos discos.
- Leitura altamente paralela.
- Bloqueia todos os discos a cada I/O. Péssimo para múltiplas transações.

</div>

<div v-click>

### Nível de Bloco (Block-level)
- Distribui blocos lógicos inteiros (*round-robin*).
- Permite requisições independentes concorrentes nos discos.
- **É a base de desempenho do RAID 0, 4, 5 e 6.**

</div>

</div>

<!--
Importante diferenciar granularidade de striping. O tamanho do bloco (strip size) define as vantagens.
-->

---

# Redundância: Mirroring vs. Paridade

<div class="grid grid-cols-2 gap-8 pt-2">

<div v-click>

### Mirroring (Espelhamento)
- Cópia 1:1 integral (RAID 1).
- Rápida leitura distribuída.
- Tolerância isolada sem overhead de processamento.
- **Custo:** 100% de desperdício de espaço.

</div>

<div v-click>

### Paridade (XOR)
- Proteção computada (*exclusive OR*).
- $B_i = B_1 \oplus B_2 \oplus P$.
- Um bloco protege $(n-1)$ blocos de dados.
- **Custo:** Penalidade de recomputação a cada escrita (*read-modify-write*).

</div>

</div>

---
layout: fact
---

# MTTDL
Mean Time to Data Loss

Tempo médio esperado até a **perda irreversível** de dados (a métrica que tira o sono do DBA).

<!--
Não confunda com MTTF (falha de 1 disco). O MTTDL quantifica as falhas críticas simultâneas que superam a tolerância.
-->

---
layout: section
---

# 4. Níveis de RAID

---

# Comparação de Níveis

<div class="text-[0.8rem] leading-tight">

| Nível | Tolerância | Eficiência | Performance Escrita | Aplicação em SBD |
|---|---|---|---|---|
| **RAID 0** | 0 falhas | 100% | Excelente | Tabelas temporárias (*TempDB*) |
| **RAID 1** | 1 (por par)| 50% | Boa (limitada) | Logs de transação (WAL) e metadados |
| **RAID 5** | 1 falha | (n-1)/n | Moderada | Data Warehouses, OLAP, leitura intensiva |
| **RAID 6** | 2 falhas | (n-2)/n | Baixa | Arrays enormes, discos densos (> 8TB) |
| **RAID 10**| 1 (por par)| 50% | Excelente | **Cargas OLTP pesadas de missão crítica** |

</div>

<!--
Esta é a consolidação das recomendações do livro do Silberschatz para projetos de banco de dados.
-->

---

# Soluções Proprietárias e Aninhadas

<div class="grid grid-cols-2 gap-8 pt-2">

<div v-click>

### RAID-DP e RAID-S
- **DP (NetApp):** Dupla paridade sem a sobrecarga aleatória graças à tecnologia WAFL de alocação de logs.
- **RAID-S (EMC):** Paridade corporativa delegada por cache NVRAM.

</div>

<div v-click>

### RAID 10 (Striping de Espelhos)
- Junta a velocidade de RAID 0 com segurança de RAID 1.
- Contorna a penalidade de cálculo de paridade (XOR) que freia os HDDs tradicionais em escrita pesada.

</div>

</div>

<!--
Abraçar a diversidade corporativa sem ficar refém dos algoritmos "vanilla".
-->

---
layout: section
---

# 5. Conclusões

---

# Considerações Finais

<v-clicks>

- O modelo ideal de \textit{storage} mescla HDDs, SSDs e NVMe alinhados à criticidade.
- A configuração **depende da carga**: OLAP favorece RAID 5 (espaço eficiente), enquanto OLTP depende de RAID 10 (IOPS puro).
- O Gargalo de Rebuild: Discos modernos gigantes geram um \textit{MTTR} perigoso (dias de reconstrução) — evite RAID 5 com HDDs enormes.

</v-clicks>

---

# Post-mortem: uso de IA

<v-clicks>

- **Agilidade:** Conversão maciça de anotações soltas para prosa coesa usando Claude/Gemini.
- **O perigo conceitual:** A IA confundiu "Shadowing" de hardware com "Shadow Paging" (mecanismo lógico do próprio SGBD para Atomicidade).
- **Intervenção Humana:** A revisão e validação com literatura acadêmica rigorosa (Elmasri e Silberschatz) continua insubstituível.

</v-clicks>

<!--
O enunciado destaca esta parte. Mostrando claramente um erro da IA (Shadowing de hardware vs. Shadow Paging de SBD) defendemos a autoria.
-->

---
layout: center
class: text-center
---

# Obrigado

## Perguntas?
