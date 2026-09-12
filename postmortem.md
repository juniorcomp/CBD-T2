# Log de Post-Mortem — Trabalho 2 CBD

> **Preencha isto DURANTE o trabalho, não no final.**
>
> O enunciado exige, na seção Post-Mortem do relatório: (i) log dos
> prompts-chave, (ii) autoria individual — quem fez o quê, quem decidiu o quê e
> **por quê** — e (iii) os erros gerados pela IA com as respectivas correções e
> quem as fez.
>
> Reconstruir isso no último dia não fica crível: o log perde as datas, os erros
> somem e a autoria vira ficção. A seção Post-Mortem do `relatorio/main.tex` é
> montada a partir daqui.

## Convenções deste arquivo

1. **Todo erro da IA que for pego vira imediatamente uma linha na tabela §4.** A lista de erros é a evidência mais forte de que houve revisão humana.
2. **No máximo duas frases por célula.** Uma para o erro, com o número ou o nome errado; uma para a correção. Sem narrativa. A tabela é um índice.
3. **Erro que merece prosa não é linha da tabela** — é um parágrafo em §5, e só entra lá se revelar um **padrão**.
4. **Toda decisão do grupo vira uma linha em §2**, com a justificativa.
5. **Prompt que mudou o rumo do trabalho vira entrada em §3.**
6. **Data em toda linha.** Sem data, a cronologia se perde e o log deixa de ser um log.

---

## 1. Divisão de responsabilidades

| Integrante | Responsável por | Decisões que tomou e por quê |
|---|---|---|
| Daniel Rebouças de Sousa Barros — DRE 123273542 | Interfaces DAS e revisão de fontes técnicas para SATA, eSATA, SAS, FireWire, USB e HBA. | Priorizou documentação técnica para dados atuais de interface, pois os livros-base não cobrem todos os padrões recentes. |
| Hugo Leandro Antunes — DRE 123143543 | RAID 0--4, striping e blocos de paridade. | Organizou a explicação por granularidade de distribuição, para relacionar arquitetura, paralelismo e gargalos de escrita. |
| Jeson Wen Chen — DRE 123051893 | RAID 5/6 e métricas MTTF, MTTR, MTBF e MTTDL. | Explicitou as hipóteses das fórmulas e evitou limiar universal para recomendar RAID 6. |
| João Batista Brasil Junior — DRE 121091172 | RAID 0+1/10, RAID não padrão e comparação de aplicações em SBD. | Diferenciou níveis compostos e qualificou implementações proprietárias conforme a documentação disponível. |
| Pedro Cintra Silveira — DRE 123419342 | Integração do LaTeX, slides, referências e revisão final de coerência. | Manteve consistência entre o relatório, os slides e o checklist de entrega. |

---

## 2. Decisões do grupo (não delegadas à IA)

Registre aqui toda escolha que exigiu julgamento humano. Exemplo: quais fontes usar para as características de DAS e RAID, qual a profundidade técnica das comparações, ou como estruturar a apresentação.

| Data | Quem decidiu | Decisão | Justificativa |
|---|---|---|---|
| 09/09/2026 | Pedro Cintra Silveira | Usar PROPOSTA.md como fonte de verdade e manter checklist de entrega no README. | Evita esquecer PDF, slides, folha de rosto e Post-Mortem. |
| 10/09/2026 | Daniel R. S. Barros e João Batista Brasil Junior | Tratar interfaces atuais e RAID proprietário apenas com documentação oficial ou limitação explícita. | Livros-base não cobrem todos os padrões atuais; nomes proprietários não podem ser generalizados. |
| 10/09/2026 | Jeson Wen Chen | Não usar limiar universal de número de discos para recomendar RAID 6. | A decisão depende de MTTR, capacidade, carga, criticidade e risco operacional. |

---

### Decisões da revisão assistida (distintas das decisões humanas acima)

| Data | Executor | Decisão | Justificativa |
|---|---|---|---|
| 10/09/2026 | Codex, sob pedido do usuário | Separar HDD/SSD, adotar modelos explícitos e fontes primárias; preservar autoria não documentada. | Evitar especificações e contribuições humanas inventadas. |

## 3. Log de prompts-chave

### Prompt 1
- **Data:** 09/09/2026
- **Quem:** Pedro Cintra Silveira
- **Ferramenta:** Antigravity (Gemini 3.1 Pro)
- **Objetivo:** Inicializar a estrutura do relatório e revisar os requisitos da proposta.
- **Prompt:**

  ```
  fazendo uma analise e buscando quais são os pontos que faltam para a realização da tarefa, se puder coloque um checklist de tarefas a serem compridas no readme...
  ```

- **O que voltou:** Checklist adicionado ao README e descrição da tarefa no proposta.md.
- **O que o grupo fez com isso:** Estabeleceu a base para monitorar as entregas exigidas pelo professor.

### Ferramentas declaradas pelo grupo

- GPT-5.6 Terra;
- Gemini 3.1;
- Claude Sonnet;
- Claude Opus 4.6.

### Prompt 2 — fluxo de redação e revisão
- **Data:** 10/09/2026.
- **Quem:** Pedro Cintra Silveira (incorporação e revisão editorial).
- **Ferramenta:** IA generativa; o modelo específico não foi retido no histórico.
- **Objetivo:** gerar uma subseção em LaTeX por vez a partir de bibliografia fornecida e revisar erros técnicos, de formatação ou prolixidade apontados pelo grupo.
- **Prompt:**

  ```
  Atue como um especialista em Sistemas de Banco de Dados e coautor acadêmico.
  Nosso objetivo é redigir um relatório técnico em LaTeX sobre Arquiteturas de
  Armazenamento Físico (DAS e RAID). [...] Geração Fragmentada: Eu solicitarei
  a redação de apenas uma subseção por vez, colando a referência bibliográfica
  base. [...] Revisão e Correção: Eu lerei o texto. Se houver qualquer erro
  técnico, alucinação, formatação inadequada ou prolixidade, eu apontarei o
  problema. Você fará a correção.
  ```
- **Resultado e uso:** orientou a produção fragmentada e a tabela de correções da seção 4.

### Prompt 3 — estrutura do estudo DAS e RAID
- **Data:** 10/09/2026.
- **Quem:** Hugo Leandro Antunes, Jeson Wen Chen e João Batista Brasil Junior (incorporação técnica).
- **Ferramenta:** IA generativa; o modelo específico não foi retido no histórico.
- **Objetivo:** produzir rascunho em Markdown para interfaces DAS, desempenho, confiabilidade, striping, RAID padrão e RAID não padrão.
- **Prompt:**

  ```
  Atue como um especialista em Arquitetura de Computadores e Sistemas de Banco
  de Dados. O objetivo é escrever um estudo acadêmico profundo sobre
  arquiteturas de armazenamento físico utilizando DAS e RAID. Baseie o rigor
  técnico nas obras de Silberschatz, Korth e Sudarshan; Elmasri e Navathe; e
  Garcia-Molina, Ullman e Widom. Estruture o trabalho utilizando Markdown,
  com títulos, subtítulos e tabelas comparativas.
  ```
- **Resultado e uso:** gerou material-base depois convertido e revisado para relatório e slides.

### Prompt 4 — auditoria de coerência
- **Data:** 10/09/2026.
- **Quem:** Pedro Cintra Silveira (incorporação e revisão editorial).
- **Ferramenta:** GPT-5.6 Terra.
- **Objetivo:** confrontar proposta.md e readme.md quanto a escopo, precisão técnica, clareza e lacunas.
- **Prompt:**

  ```
  Com base em todo o contexto carregado, examine criteriosamente os arquivos
  proposta.md e readme.md para avaliar a coerência e a precisão do material.
  Execute a análise cobrindo coerência e alinhamento; precisão técnica e
  consistência; clareza e completude; e sugestões de correção, indicando o
  trecho problemático e a versão corrigida sugerida.
  ```
- **Resultado e uso:** corrigiram-se instalação dos slides, checklists, nomenclatura SATA e trechos técnicos sem fonte suficiente.

---

## 4. Erros da IA e correções

**Regra do grupo: Nenhum conceito técnico entra sem validação contra os livros (Silberschatz, Elmasri, Garcia-Molina) ou documentação oficial.** Toda vez que essa regra pegar um erro, ele vira uma linha aqui.

| # | Data | Onde (seção/tabela) | Erro gerado pela IA | Correção aplicada | Fonte que sustentou a correção | Quem corrigiu |
|---|---|---|---|---|---|---|
| 1 | 10/09/2026 | RAID 2 e tabela comparativa | A IA afirmou “mínimo: 3 discos” como regra universal. | A quantidade de discos de redundância depende da implementação do código de Hamming e da quantidade de discos de dados; a regra foi removida. | Elmasri e Navathe, Seção 16.10, p. 587. | Hugo Leandro Antunes |
| 2 | 10/09/2026 | RAID-DP | Foram atribuídos algoritmo interno e limite universal de 28 discos sem fonte primária suficiente. | Mantida somente a propriedade verificável: duas paridades e tolerância a duas falhas; tamanho do grupo é orientação específica do ONTAP. | NetApp, ONTAP RAID Groups and Local Tiers. | João Batista Brasil Junior |
| 3 | 10/09/2026 | RAID-S | Foi descrito como algoritmo que adapta dinamicamente a distribuição conforme o acesso. | Corrigido para tecnologia proprietária Symmetrix baseada em grupos de volumes de dados e paridade; não há evidência para adaptação dinâmica. | NASA/EMC, RAID-S Technical Overview. | João Batista Brasil Junior |
| 4 | 10/09/2026 | Interfaces DAS | A IA usou “SATA II/III” e tratou MTBF como atributo da interface. | Adotada a nomenclatura SATA-IO e a confiabilidade passou a depender de disco, firmware, controlador e ambiente. | SATA-IO, SATA Naming Guidelines. | Daniel Rebouças de Sousa Barros |


| 5 | 10/09/2026 | Interfaces | SAS-4: agregado de 4.500 MB/s; filas SAS fixadas em 256. | Corrigido cálculo para 4.800 MB/s e fila dependente do produto [chaves BibTeX: microchip_sas4,seagate_sas]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Daniel Rebouças de Sousa Barros |
| 6 | 10/09/2026 | SATA/USB | AHCI confundido com protocolo físico; energia USB vinculada à geração. | Separadas interface de programação, transporte e Power Delivery [chaves BibTeX: intel_ahci,usb_pd]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Daniel Rebouças de Sousa Barros |
| 7 | 10/09/2026 | eSATA | Origem atribuída à revisão SATA 2.6. | Especificações externas datadas de 2004 [chaves BibTeX: sata_esata]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Daniel Rebouças de Sousa Barros |
| 8 | 10/09/2026 | RAID 1.5/7 | Atribuição a LSI/Broadcom e descrições sem funcionamento. | Identificada HighPoint; aprofundado RAID 7 com documentação primária [chaves BibTeX: highpoint_15,storagecomputer_7]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | João Batista Brasil Junior |
| 9 | 10/09/2026 | Confiabilidade | Igualdades de MTTDL sem modelo de reparo suficiente; MTBF sem convenção. | Derivadas aproximações com reparo serial exponencial e delimitada contagem de tempo [chaves BibTeX: nist_mtbf]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Jeson Wen Chen |
| 10 | 10/09/2026 | RAID 5/WAL | Quatro I/Os em toda escrita; commit sempre aguardando ambos os discos. | Separados RMW/full stripe, cache e modos de durabilidade [chaves BibTeX: raid5_io,postgresql_wal_config]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Jeson Wen Chen |
| 11 | 10/09/2026 | Integridade | Redundância apresentada como garantia de serviço ininterrupto. | Acrescentados limites, write hole, checksums e recuperação [chaves BibTeX: spdk_raid5f,krioukov2008]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Pedro Cintra Silveira |
| 12 | 10/09/2026 | Bibliografia | Link IBM genérico e PostgreSQL 16 com URL current. | Substituídas fontes e fixada versão; corrigida autoria do artigo EMC preservado pela NASA. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Pedro Cintra Silveira |
| 13 | 10/09/2026 | Shadowing | Confusão entre espelhamento físico e ponto de commit na paginação sombra. | Separadas camadas e descrita troca atômica da referência persistente [chaves BibTeX: dbbook_storage,elmasri2016]. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Hugo Leandro Antunes |
| 14 | 10/09/2026 | Comparação | Eficiência fixa de RAID 1 com múltiplas cópias e avaliações vagas de escrita. | Usada eficiência 1/m; comparadas cargas e acrescentados exemplos calculados. | Fontes em relatorio/ref.bib e pesquisa/revisao_fontes.md | Pedro Cintra Silveira |

### Erros a vigiar nesta tarefa específica (DAS e RAID)

Marque quando encontrar:
- [ ] Confusão entre níveis de RAID não padrão (ex: RAID 1.5, 7, Matrix). A IA costuma alucinar detalhes desses níveis proprietários.
- [ ] Fórmulas erradas para cálculo de redundância e capacidade (ex: capacidade útil no RAID 5 e RAID 6).
- [ ] Confusão entre termos de confiabilidade: MTTR, MTTDL, MTTF e MTBF.
- [ ] Atribuição incorreta de interfaces (SATA, SAS, USB) para usos puramente DAS versus SAN/NAS.

---

## 5. Lições aprendidas

Uma observação sobre o *padrão* dos erros vale mais do que a lista deles. Em que tipo de tarefa a IA foi confiável? Em que tipo ela falhou de forma sistemática? Onde a verificação humana precisou ser mais densa?

### 10/09/2026 — Precisão diminui em nomenclaturas proprietárias

IA foi útil para estruturar comparações, mas produziu detalhes não verificáveis sobre RAID 1.5, RAID-DP e RAID-S. Para esses tópicos, o grupo passou a aceitar documentação do fabricante ou a registrar explicitamente a limitação da fonte.

### 10/09/2026 — Fórmulas exigem hipóteses

Fórmulas de MTTDL ajudam a comparar cenários, mas não são previsão literal. O relatório passou a declarar hipóteses como independência das falhas, taxa constante e MTTR conhecido, além de lembrar que RAID não substitui backup testado.

## Revisão técnica desta sessão — 10/09/2026

- Ferramenta: Codex; solicitante não identificado nominalmente nesta sessão.
- Prompt literal: “Faça correções todas as necessaárias. Posteriromente, melhore necessário use fonte externas também e as coloque referenciada no refbib e no final do maintext”
- Resultado: relatório e bibliografia revisados; exemplos e modelos delimitados. Nenhum benchmark foi executado.
- Limitação: não há compilador LaTeX disponível e o download solicitado não foi autorizado; não foi realizada inspeção visual de PDF.
- A validação humana das atribuições anteriores e os prompts históricos completos não foram fornecidos.
