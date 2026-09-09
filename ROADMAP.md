# Roadmap — Trabalho 2 CBD (DAS e RAID)

Enunciado: [PROPOSTA.md](PROPOSTA.md). Como compilar: [README.md](README.md).
Este arquivo é a **única fonte de verdade sobre progresso**.

## Prazos

| Data | O quê |
|---|---|
| **sexta, 11/09/2026, 23:59** | **Vencimento da postagem no AVA — PDF e slides** |
| até 13/09/2026 | Professor anuncia os grupos que vão apresentar |
| 14/09/2026 | Apresentação e debate |

**O prazo de entrega é anterior ao anúncio dos grupos.** Não dá para esperar
para saber se o grupo foi escolhido: os slides precisam estar prontos e postados
em 11/09 de qualquer forma. Restam **poucos dias** a partir de hoje — o que
significa que as Etapas iniciais (pesquisa e estruturação) precisam fechar
logo, e a Etapa final (slides) não pode ser tratada como opcional.

Enviar cópia para [milton@matematica.ufrj.br](mailto:milton@matematica.ufrj.br) dentro do prazo, como garantia
contra indisponibilidade do AVA.

---

## O que este trabalho é, e o que ele não é

O enunciado pede o estudo de interfaces de hardware para DAS (SATA, SAS, USB, etc)
e os níveis de RAID (0 a 6, 10, proprietários). Parte disso é **descritivo**: dizer
o que a sigla significa e como liga o cabo. Qualquer grupo entrega isso, e a IA
escreve isso sozinha. Não é ali que a nota se decide.

A nota e a força no debate vêm da **análise comparativa técnica**: como o
*data striping* e o *bit-level striping* afetam o paralelismo e a performance?
Como provar (via MTTF, MTTR, MTTDL) a diferença de confiabilidade entre RAID 5 e
RAID 6? Quais são os gargalos reais do HBA em relação a conexões nativas?

**Muitos grupos apresentarão os mesmos conceitos.** A diferença aparece na
capacidade de não apenas elencar "vantagens e desvantagens", mas justificar
*matematicamente* e *arquiteturalmente* o porquê de cada recomendação de uso,
ancorado em Silberschatz, Elmasri e Garcia-Molina.

---

## Princípio de execução

Uma seção por vez, fechada de ponta a ponta antes da próxima. A **ordem de
escrita não é a ordem do documento** — é a ordem das dependências:

```
Etapa 0   decisões metodológicas       <- trava tudo; define a profundidade da análise
   |
Etapa 1   pesquisa bibliográfica bruta <- levanta dados dos três livros indicados
Etapa 2   comparativos de interfaces   <- analisa DAS (SATA, eSATA, SAS, Firewire, USB, HBA)
Etapa 3   comparativos de RAID         <- analisa RAID padrão (0 a 6, 10) e não padrão
   |
Etapa 4   discussões avançadas         <- performance, striping, MTTF/MTTR/MTTDL
   |
Etapa 5   molduras (intro, conclusão, considerações)
Etapa 6   slides + preparação do debate
Etapa 7   Post-Mortem consolidado + revisão final
```

**Por que a teoria base vem primeiro:** Sem referenciar as páginas exatas de
Elmasri e Silberschatz nas tabelas e anotações, corre-se o risco de a IA
gerar conteúdo de blog. Todo dado sai da referência acadêmica.

---

## Protocolo de revisão (vale para toda etapa)

1. **Uma etapa mexe em um arquivo/seção.** Diff pequeno, revisão possível.
2. **Nenhum conceito técnico sem fonte.** O enunciado lista capítulos exatos de
   Silberschatz, Elmasri e Garcia-Molina. Eles devem ser a fonte primária.
3. **Coleta acontece em Markdown, não em LaTeX.**
   As tabelas comparativas são rascunhadas em `.md` (ou anotações); a transcrição
   para `.tex` acontece **uma vez**, depois de revisada.
4. **Todo erro de IA pego vira linha em [postmortem.md](postmortem.md) §4.**
   O enunciado exige a lista de erros e como o humano a corrigiu.
5. **Duas frases por célula, no máximo.** O post-mortem é um registro pontual,
   não um diário de desabafo.

---

## Etapa 0 — Fechar as decisões metodológicas

**Bloqueia todas as outras.** Decisões de escopo e rigor do estudo.

- [ ] **0.1 Foco em Mídia (HDD vs SSD)**: Vamos focar em discos magnéticos tradicionais
      (que deram origem aos níveis de RAID) ou trazer os impactos dos SSDs modernos
      na taxa de falha (MTTF) e na escolha de interfaces (SATA vs SAS)?
- [ ] **0.2 Profundidade da Confiabilidade**: Até onde mergulhar na matemática? Vamos
      demonstrar como calcular o MTTDL a partir do MTTF e MTTR para cada nível, ou 
      apenas usar tabelas comparativas qualitativas?
- [ ] **0.3 Estrutura Visual**: Definir se usaremos esquemas/figuras estruturais 
      para ilustrar o *striping* e a paridade, e se nós mesmos vamos gerá-las.

---

## Etapa 1 — Pesquisa bibliográfica bruta

Dado extraído diretamente da literatura indicada no roteiro.

- [ ] **1a** Silberschatz, Korth e Sudarshan (Seção 12.5): Extrair pontos-chave.
- [ ] **1b** Elmasri e Navathe (Seção 16.10): Extrair pontos-chave.
- [ ] **1c** Garcia-Molina, Ullman e Widom (Seções 13.3 e 13.4): Extrair informações adicionais.

---

## Etapa 2 — Comparativo DAS e Interfaces

Estudo detalhado das interfaces físicas, não delegando a compreensão para textos
genéricos da internet.

- [ ] **2a** SATA e eSATA: arquitetura, uso, limitações.
- [ ] **2b** SAS: diferenças de performance e confiabilidade em relação ao SATA.
- [ ] **2c** Firewire e USB (3 e 4): cenários de uso, por que não se usa USB para banco de dados relacional de alta disponibilidade, gargalos de barramento.
- [ ] **2d** HBA (Host Bus Adapter): função, alívio de CPU e arquitetura de integração.

---

## Etapa 3 — Níveis de RAID

O núcleo técnico do armazenamento de dados.

- [ ] **3a** RAID Padrão: 0, 1, 2, 3, 4, 5, 6. Para cada um: mecanismo (striping, mirroring, paridade), vantagem, desvantagem, recomendação de uso para SBD.
- [ ] **3b** RAID Híbrido: 0+1 e 10. Diferença crucial na tolerância a falhas na hora de reconstrução (MTTR).
- [ ] **3c** RAID Não Padrão: 1.5, 7, DP, S, Matrix. Identificar claramente quais são proprietários e como funcionam (cuidado extremo com alucinação de IA aqui).

---

## Etapa 4 — Discussões Avançadas (Tese e Análise)

Esta seção resolve as sub-perguntas cruciais do enunciado e ancora a defesa no debate.

- [ ] **4a** Paralelismo x Redundância: o balanço de forças.
- [ ] **4b** Níveis de *Striping*: *Data*, *Bit-level* e *Block-level*. O impacto do tamanho do stripe na performance de leitura/escrita.
- [ ] **4c** Mirroring vs Shadowing: diferenças conceituais de implementação.
- [ ] **4d** O Quarteto da Confiabilidade: MTTR, MTTDL, MTTF, MTBF. Fórmulas, interpretação e o impacto prático de tempos altos de reconstrução (MTTR) em arrays com discos muito grandes.

---

## Etapa 5 — Molduras do documento

Tudo escrito de uma vez, no fim, porque tudo aqui é resumo do que já existe.

- [ ] Introdução
- [ ] Conclusão
- [ ] Considerações e dúvidas em aberto
- [ ] Resumo (último de todos)

---

## Etapa 6 — Slides e preparação do debate

Derivados do relatório pronto, não escritos em paralelo.

- [ ] Reduzir a teoria a esquemas visuais (diagramas de RAID valem mais que listas de texto).
- [ ] Apontar nos slides exatamente os tópicos avançados (MTTDL, comparação SAS x SATA) que serão usados como ataque ou defesa.
- [ ] Preparação: Como responder por que RAID 5 entrou em desuso com discos grandes (referência ao MTTR elevado).
- [ ] `npm run export` (se aplicável ao workflow)

---

## Etapa 7 — Post-Mortem e fechamento

- [ ] Consolidar `postmortem.md` e inserir no LaTeX.
- [ ] Folha de rosto no PDF e nos slides (com DRE).
- [ ] Folha de rosto avulsa extraída para os outros integrantes do grupo.
- [ ] Conferir citações (referências cruzadas e links).
- [ ] Revisão cruzada e envio para AVA / email do professor.
