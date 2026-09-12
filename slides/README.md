# Slides (Slidev + tema academic)

https://sli.dev

## Setup — uma vez só

Precisa de **Node.js 20.12 ou superior**. Dentro desta pasta:

```powershell
npm.cmd ci
```

O projeto versiona `package-lock.json`; por isso, `npm.cmd ci` reproduz as
versões testadas. No PowerShell, prefira `npm.cmd` para evitar que a política
de execução do Windows bloqueie o arquivo `npm.ps1`.

Isso baixa o Slidev e o Chromium usado para exportar (é o passo demorado,
alguns minutos). Se o export reclamar de browser faltando, rode:

```bash
npx playwright install chromium
```

## Rodar

```powershell
npm.cmd run dev
```

Abre em `http://localhost:3030`. O navegador atualiza sozinho conforme você
edita o `slides.md`.

Atalhos úteis durante a apresentação:

| Tecla | O quê |
|---|---|
| `→` / `espaço` | próximo passo (respeita os `v-click`) |
| `←` | voltar |
| `p` | **modo apresentador** — notas, cronômetro, próximo slide |
| `d` | modo escuro |
| `o` | visão geral de todos os slides |
| `g` | ir para um slide pelo número |
| desenhar | botão de caneta na barra inferior, para anotar ao vivo |

O modo apresentador importa neste trabalho: depois da apresentação vem uma
rodada de perguntas abertas e um debate. As notas são onde ficam os números de
reserva que não couberam no slide.

## Exportar

```powershell
npm.cmd run export        # gera slides-export.pdf
npm.cmd run export:pptx   # se precisar de PPTX (texto vira imagem)
```

> **Não remova a flag `--per-slide` do script.**
> Sem elas, o Slidev captura os slides antes do conteúdo assentar e o PDF sai
> com o conteúdo deslocado uma página — diagramas e listas aparecem no slide
> seguinte.

## Sintaxe rápida

| O que | Como |
|---|---|
| Novo slide | linha com `---` |
| Configuração do slide | bloco YAML logo após o `---` (ex.: `layout: section`) |
| Notas do apresentador | comentário HTML `<!-- ... -->` **no fim** do slide |
| Revelar itens um a um | envolver a lista em `<v-clicks>` … `</v-clicks>` |
| Revelar um bloco | `<v-click>` … `</v-click>` ou `v-click` como atributo |
| Duas colunas | `layout: two-cols` com `::right::` no meio |
| Fórmula | `$...$` inline, `$$...$$` em bloco (KaTeX) |
| Diagrama | bloco de código ` ```mermaid ` — ajuste com `{scale: 0.6}` |
| Imagem | `![alt](/caminho.png)` — arquivos em `public/` |

### Layouts

Do tema `academic`: `cover`, `intro`, `table-of-contents`, `index`, `figure`,
`figure-side`.
Do próprio Slidev (funcionam em qualquer tema): `default`, `section`, `center`,
`two-cols`, `two-cols-header`, `quote`, `fact`, `statement`, `end`, `none`.

O layout `fact` serve bem para um número único grande — neste trabalho, por
exemplo, para destacar MTTDL ou a capacidade útil de um nível RAID.

### Componentes do tema academic

`Footnotes` / `Footnote` — notas de rodapé numeradas no pé do slide. Use-as para
citar livros, especificações de interfaces e documentação de fabricantes.
**Todo slide com número ou afirmação técnica densa deve indicar sua fonte.**

`Pagination` — a numeração no canto, renderizada automaticamente.

### Capa

O layout `cover` do tema junta os autores num parágrafo só e separa o último
com um " and " em inglês — com cinco nomes e DREs fica ilegível. Por isso o
`coverDate: ''` no cabeçalho desliga o bloco do tema, e a lista de integrantes é
montada no corpo do primeiro slide, onde dá para controlar o layout.

### Fontes

Três linhas no cabeçalho, sem CSS. Qualquer fonte do
[fonts.google.com](https://fonts.google.com) serve:

```yaml
fonts:
  sans: 'IBM Plex Sans'
  serif: 'IBM Plex Serif'
  mono: 'IBM Plex Mono'
  weights: '300,400,600'
```

Outras chaves: `italic`, `fallbacks`, `local` e `provider`
(`google`, `coollabs` ou `none`). Com o `npm run dev` aberto, a troca aparece
na hora — é o melhor jeito de comparar.

## Armadilhas que custam tempo

1. **`<br>` quebra o build.** O Slidev compila o Markdown como template Vue, que
   exige tags fechadas — use `<br />`.
2. **Só o último comentário HTML do slide vira nota.** Um comentário no meio do
   slide é renderizado como texto visível.
3. **Nada de comentários `#` no cabeçalho YAML.** O parser do Slidev empurra
   essas linhas para o corpo do slide e elas viram texto visível, em letra
   garrafal. Comentário sobre um slide vai no bloco `<!-- ... -->` do fim dele.
4. **Exportar sem `--per-slide` desloca o conteúdo em uma página.** Já está
   resolvido no script, mas se você edita o comando, lembre.

## Regra prática para este trabalho

As tabelas do relatório **não cabem** num slide de forma legível. Mostre
recortes de 4 a 5 colunas e deixe a versão completa para o PDF. Vale mais um
slide com três números que a plateia consegue ler do que um com trinta que
ninguém lê.

## Trocar de tema

O tema atual é o tema oficial padrão do Slidev, [`@slidev/theme-default`](https://www.npmjs.com/package/@slidev/theme-default).
Para experimentar outro:

```bash
npm i slidev-theme-neversink
```

e mude `theme:` no cabeçalho do `slides.md`. A galeria oficial está em
https://sli.dev/resources/theme-gallery, e dá para listar tudo com
`npm search slidev-theme`.

**Cada tema traz os próprios layouts e parâmetros.** Trocar não é só mudar a
linha do `theme:` — o bloco da capa e os `layout:` dos slides provavelmente vão
precisar de ajuste.
