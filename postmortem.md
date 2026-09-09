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
| Nome do Aluno 1 — DRE XXXXXXXXX | | |
| Nome do Aluno 2 — DRE XXXXXXXXX | | |
| Nome do Aluno 3 — DRE XXXXXXXXX | | |

---

## 2. Decisões do grupo (não delegadas à IA)

Registre aqui toda escolha que exigiu julgamento humano. Exemplo: quais fontes usar para as características de DAS e RAID, qual a profundidade técnica das comparações, ou como estruturar a apresentação.

| Data | Quem decidiu | Decisão | Justificativa |
|---|---|---|---|
| 09/09/2026 | | **0.1** — | |

---

## 3. Log de prompts-chave

### Prompt 1
- **Data:** 09/09/2026
- **Quem:** 
- **Ferramenta:** Antigravity (Gemini 3.1 Pro)
- **Objetivo:** Inicializar a estrutura do relatório e revisar os requisitos da proposta.
- **Prompt:**

  ```
  fazendo uma analise e buscando quais são os pontos que faltam para a realização da tarefa, se puder coloque um checklist de tarefas a serem compridas no readme...
  ```

- **O que voltou:** Checklist adicionado ao README e descrição da tarefa no proposta.md.
- **O que o grupo fez com isso:** Estabeleceu a base para monitorar as entregas exigidas pelo professor.

---

## 4. Erros da IA e correções

**Regra do grupo: Nenhum conceito técnico entra sem validação contra os livros (Silberschatz, Elmasri, Garcia-Molina) ou documentação oficial.** Toda vez que essa regra pegar um erro, ele vira uma linha aqui.

| # | Data | Onde (seção/tabela) | Erro gerado pela IA | Correção aplicada | Fonte que sustentou a correção | Quem corrigiu |
|---|---|---|---|---|---|---|
| 1 | | | | | | |

### Erros a vigiar nesta tarefa específica (DAS e RAID)

Marque quando encontrar:
- [ ] Confusão entre níveis de RAID não padrão (ex: RAID 1.5, 7, Matrix). A IA costuma alucinar detalhes desses níveis proprietários.
- [ ] Fórmulas erradas para cálculo de redundância e capacidade (ex: capacidade útil no RAID 5 e RAID 6).
- [ ] Confusão entre termos de confiabilidade: MTTR, MTTDL, MTTF e MTBF.
- [ ] Atribuição incorreta de interfaces (SATA, SAS, USB) para usos puramente DAS versus SAN/NAS.

---

## 5. Lições aprendidas

Uma observação sobre o *padrão* dos erros vale mais do que a lista deles. Em que tipo de tarefa a IA foi confiável? Em que tipo ela falhou de forma sistemática? Onde a verificação humana precisou ser mais densa?

### `[data]` — `[título da lição]`
(Adicione lições ao longo do trabalho)
