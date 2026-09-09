# Instruções do Projeto (CBD-T2)

## Relatório (LaTeX)
Requer uma distribuição TeX completa (TeX Live, MiKTeX) ou Overleaf. Além do preâmbulo padrão, usa um pacote a mais: `longtable`.

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
npm ci              # instala as versões exatas do package-lock (primeira vez)
npm install         # alternativa, se você mexeu no package.json
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
- [pesquisa/tabelas.md](./pesquisa/tabelas.md): são as seis tabelas a preencher — a coleta acontece ali, em Markdown, e só é transcrita para LaTeX depois de revisada e aceita.

---

## Versionamento
`node_modules/` e os arquivos auxiliares do LaTeX devem estar no `.gitignore`. A pasta `pesquisa/` não está ignorada — confira se ela entrou no commit com:
```bash
git status --short
git add pesquisa/
```

---

## Checklist de Entrega

- [ ] `\title` e `\author` do `main.tex` com nomes completos e DREs
- [ ] Mesmos nomes e DREs no primeiro slide de `slides/slides.md`
- [ ] Nenhum bloco `% >>>` sobrando no `main.tex`
- [ ] Nenhum número no PDF sem fonte e data de acesso
- [ ] Seção Post-Mortem completa: prompts, autoria, erros e correções
- [ ] Folha de rosto presente no PDF e nos slides
- [ ] Folha de rosto avulsa extraída (página 1 do `main.pdf`) e distribuída aos integrantes que não vão postar o trabalho completo
- [ ] PDF e slides postados no AVA por um integrante; os demais postam só a folha de rosto
- [ ] Cópia enviada para [milton@matematica.ufrj.br](mailto:milton@matematica.ufrj.br) dentro do prazo (garantia contra indisponibilidade do AVA)
