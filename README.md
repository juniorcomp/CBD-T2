# Instruções do Projeto (CBD-T2)

`PROPOSTA.md` é a fonte de verdade do enunciado e dos critérios acadêmicos. Este
arquivo descreve apenas o fluxo de produção, validação e entrega.

## Checklist de conteúdo obrigatório

Antes de gerar os arquivos finais, confirme que relatório e slides cobrem:

- DAS e as interfaces SATA, eSATA, SAS, FireWire, USB 3/4 e HBA;
- RAID 0--6, RAID 0+1 e RAID 10, com características, vantagens,
  desvantagens e aplicações em SBD;
- paralelismo, redundância, disponibilidade e confiabilidade;
- *data striping*, *bit-level striping*, *block-level striping*, *mirroring* e
  *shadowing*;
- MTTF, MTBF, MTTR e MTTDL, com hipóteses explícitas quando houver fórmulas;
- RAID não padrão: 1.5, 7, DP, S e Matrix, deixando explícito quando a
  implementação depende de fabricante;
- fontes acadêmicas indicadas no enunciado e fontes web primárias para dados
  de interfaces e produtos.

## Relatório (LaTeX)
Requer uma distribuição TeX completa (TeX Live, MiKTeX) ou Overleaf. O `main.tex` já declara os pacotes necessários.

```bash
cd relatorio
latexmk -pdf main.tex       # compila → main.pdf
latexmk -pvc -pdf main.tex  # recompila a cada vez que você salvar
latexmk -c                  # limpa auxiliares (mantém o PDF)
latexmk -C                  # limpa tudo, inclusive o PDF
```

Sem `latexmk`, a sequência manual:
```bash
pdflatex main && bibtex main && pdflatex main && pdflatex main
```

**No Overleaf:** subir a pasta `relatorio/` e definir `main.tex` como principal.

> **Nota:** Antes de entregar, procure por `% >>>` no código fonte. São as orientações de preenchimento; não aparecem no PDF, mas não devem sobrar na versão final.

### Folha de rosto avulsa
O enunciado exige que cada integrante que não posta o trabalho completo poste a folha de rosto em PDF, individualmente. Como o relatório é um arquivo só, ela é a página 1 do `main.pdf`:

```bash
qpdf main.pdf --pages . 1 -- folha-de-rosto.pdf
# ou
pdftk main.pdf cat 1 output folha-de-rosto.pdf
```
*Sem essas ferramentas:* abrir o PDF, imprimir, escolher intervalo 1, e salvar como PDF.

---

## Slides (Slidev)
Requer Node.js 20.12 ou superior. Confira com `node -v`.

```bash
cd slides
npm install         # primeira instalação; o projeto ainda não versiona package-lock.json
npm ci              # usar apenas após gerar e versionar package-lock.json
npm run dev         # abre em http://localhost:3030, recarrega ao salvar
npm run export      # gera slides-export.pdf
npm run export:pptx # se precisar de PPTX (o texto vira imagem)
npm run build       # versão web estática, em dist/
```

A primeira instalação baixa um Chromium (~150 MB), usado para exportar. Se o export reclamar de browser faltando, rode:
```bash
npx playwright install chromium
```

> **Atenção:** Não remova as flags do script de exportação no `package.json`. Sem `--per-slide`, o Slidev captura os slides antes do conteúdo assentar e o PDF sai deslocado uma página.

Durante a apresentação, tecle `p` para o modo apresentador (notas, cronômetro, próximo slide). Detalhes de sintaxe e armadilhas estão em [slides/README.md](./slides/README.md).

---

## Pesquisa
- [pesquisa/GUIA.md](./pesquisa/GUIA.md): traz as regras de coleta e onde ficam as fontes oficiais de cada número. 
- [pesquisa/tabelas.md](./pesquisa/tabelas.md): contém duas tabelas de trabalho. A coleta acontece ali, em Markdown, e só é transcrita para LaTeX depois de revisada e aceita.

---

## Versionamento
`node_modules/` e os arquivos auxiliares do LaTeX devem estar no `.gitignore`. A pasta `pesquisa/` não está ignorada — confira se ela entrou no commit com:
```bash
git status --short
git add pesquisa/
```

Os PDFs são artefatos de entrega. `relatorio/main.pdf` é exceção no Git; o PDF
dos slides normalmente permanece ignorado. Guarde os arquivos finais também
fora do repositório e poste/envie ambos conforme o enunciado.

## Arquivos finais esperados

- `relatorio/main.pdf` — relatório completo, inclusive Post-Mortem e folha de rosto;
- `relatorio/folha-de-rosto.pdf` — página 1 extraída para cada integrante que não fará a postagem integral;
- `slides/slides-export.pdf` — apresentação exportada do Slidev.

---

## Checklist de Entrega

- [x] `\title` e `\author` do `main.tex` com nomes completos e DREs
- [x] Mesmos nomes e DREs no primeiro slide de `slides/slides.md`
- [ ] Nenhum bloco `% >>>` sobrando no `main.tex`
- [ ] Nenhum número no PDF sem fonte (livros: edição/seção/página; web: URL e data de acesso)
- [ ] Seção Post-Mortem completa: prompts, autoria, erros e correções
- [ ] Folha de rosto presente no PDF e nos slides
- [ ] Folha de rosto avulsa extraída (página 1 do `main.pdf`) e distribuída aos integrantes que não vão postar o trabalho completo
- [ ] PDF e slides postados no AVA por um integrante; os demais postam só a folha de rosto
- [ ] Cópia enviada para [milton@matematica.ufrj.br](mailto:milton@matematica.ufrj.br) dentro do prazo (garantia contra indisponibilidade do AVA)
