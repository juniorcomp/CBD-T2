# Guia de Coleta

## Regra principal
Nenhum dado técnico (conceito, fórmula, característica) entra numa tabela ou no texto sem a fonte (nome do autor, livro e capítulo/página) ao lado.
As fontes primárias devem ser os livros acadêmicos recomendados na [PROPOSTA.md](../PROPOSTA.md). Blog, artigo de opinião de site obscuro e resposta de assistente de IA não são fontes definitivas — servem como apoio, mas a fundamentação real sai dos livros.
Não achou a informação sobre algum protocolo proprietário obscuro? Escreve "não encontrado nas literaturas base". Célula vazia é revisada; célula preenchida por intuição da IA passa despercebida e quebra no debate.

## Decidir antes de coletar
O que estamos assumindo como base tecnológica (ex: HDDs convencionais ou arrays mistos de SSD NVMe e HDD?), qual o rigor matemático para o cálculo de MTTR e MTTDL.
Essas decisões moram no [../DECISOES.md](../DECISOES.md). Mudar a premissa de hardware no meio invalida a discussão avançada sobre RAID 5.

## Onde estão as fontes

| O que | Onde |
|---|---|
| Armazenamento e RAID (conceitos e arranjos) | **Silberschatz**, Korth e Sudarshan: *Sistema de Bancos de Dados*, seção **12.5**. |
| Paralelismo e redundância em banco de dados | **Elmasri** e Navathe: *Sistemas de Banco de Dados*, seção **16.10**. |
| Mecanismos de armazenamento estendido, falhas | **Garcia-Molina**, Ullman e Widom: *Database Systems*, seções **13.3** e **13.4**. |
| Interfaces modernas (USB 3/4, HBA, SAS) | Especificações oficiais dos consórcios (USB-IF, SATA-IO, T10/SCSI) e manuais de fabricante, pois os livros não cobrem padrões atuais. |
| RAIDs proprietários (Matrix, 1.5, 7, DP, S) | Documentação do fabricante que cunhou o termo. Sem fonte primária acessível, registrar a limitação em vez de atribuir algoritmo ou desempenho. |

## Armadilhas de unidade, nome e conceito
- **MTTF x MTBF x MTTR x MTTDL**: Não tratar esses termos de confiabilidade como sinônimos soltos. Mean Time To Failure foca em componentes sem reparo (ou até que falhem), MTBF exige declarar a convenção: em ensaios costuma contar tempo de operação; o ciclo de calendário inclui reparo, e MTTDL é a perda catastrófica de dados em arrays. A IA costuma jogar essas siglas juntas numa salada.
- **RAID 0+1 vs RAID 10 (1+0)**: É um erro clássico inverter a ordem da montagem arquitetural, o que gera uma diferença monstruosa no cálculo de risco (se um disco cair, a probabilidade de um segundo disco derrubar todo o volume é muito maior no 0+1 do que no 1+0).
- **Paridade vs Mirroring**: Fórmulas de espaço útil. Para RAID 5, capacidade é (N-1) * C; para RAID 6, (N-2) * C; RAID 10 cai pela metade (N/2 * C). Em petabytes, uma premissa errada aqui corta a capacidade em centenas de TB.
- **Níveis proprietários (RAID 1.5, 7, DP, S, Matrix)**: não atribuir algoritmo, capacidade ou tolerância sem documentação do fabricante. A IA costuma tratar esses nomes como se fossem níveis abertos e universais.

## Registro de fontes
As fontes usadas ao longo da pesquisa bruta podem ser catalogadas aqui para não se perderem até a transcrição do LaTeX.

| # | O que buscava | Fonte / Seção | Data | Quem | Serviu? |
|---|---|---|---|---|---|
| 1 | Fundamentos do RAID 0 a 6 | Silberschatz, cap 12.5 | 09/09/2026 | Grupo | sim, referencial padrão. |
| 2 | Diferença RAID 10 e 0+1 | Garcia-Molina | 09/09/2026 | Grupo | a verificar |

---

## Modelos de entrada para o `ref.bib`

Estes modelos podem ser usados no seu `relatorio/ref.bib` na transcrição final. Não usar ferramentas automáticas que leiam tudo, copie e cole manualmente na raiz do `.bib`.

```bibtex
@book{silberschatz2020,
  title     = {Database System Concepts},
  author    = {Silberschatz, Abraham and Korth, Henry F. and Sudarshan, S.},
  year      = {2020},
  publisher = {McGraw-Hill Education},
  edition   = {7}
}

@book{elmasri2016,
  title     = {Fundamentals of Database Systems},
  author    = {Elmasri, Ramez and Navathe, Shamkant B.},
  year      = {2016},
  publisher = {Pearson},
  edition   = {7}
}

@book{garciamolina2009,
  title     = {Database Systems: The Complete Book},
  author    = {Garcia-Molina, Hector and Ullman, Jeffrey D. and Widom, Jennifer},
  year      = {2009},
  publisher = {Pearson},
  edition   = {2}
}

@misc{intel_matrix,
  author = {{Intel Corporation}},
  title = {Intel Matrix Storage Technology -- Whitepaper},
  year = {2026},
  howpublished = {\url{https://www.intel.com/...}},
  note = {Acesso em: [PREENCHER]}
}
```
Ao colar, confira se a chave já está em uso para evitar duplicatas na compilação do LaTeX.
