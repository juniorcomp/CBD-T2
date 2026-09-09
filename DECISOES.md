# Decisões metodológicas — Etapa 0

Convenções que precisam estar fechadas **antes** da coleta. Mudar qualquer uma
depois invalida dado já coletado.

Este arquivo é o **registro do raciocínio**. As decisões são replicadas, em uma
linha cada, em [postmortem.md](postmortem.md) §2 (autoria), e consolidadas na
seção *Metodologia e delimitações* do `relatorio/main.tex` — que é o lugar único
delas no relatório. Nunca se repetem em outras seções do texto.

## Como usar

Cada bloco abaixo já traz **por que a decisão importa** e **quais são as opções**.
Falta o que só o grupo pode preencher: a escolha, quem a tomou, quando, e a
justificativa.

Formato de cada bloco, depois de fechado:

```
**Decisão:** <a escolha, em uma frase>
**Quem / quando:** <nome> — <data>
**Justificativa:** <por quê; o que a alternativa custaria>
**Consequência:** <o que fica travado a partir daqui>
```

Uma decisão revisada **não é apagada**: o texto antigo é riscado e a revisão
entra embaixo, com a data. O histórico da revisão é evidência de revisão humana
— e o enunciado grada exatamente isso.

Estado: `[ ]` aberta · `[x]` fechada · `[~]` fechada e depois revisada.

---

## 0.1 — Foco em Mídia de Armazenamento (HDD vs SSD)

**Por que importa.** O desempenho de qualquer interface (SATA, SAS) e os 
parâmetros de falha (MTTF) mudam drasticamente dependendo do meio magnético vs 
estado sólido. Misturar conceitos de HDD com taxas de transferência de SSD NVMe 
pode invalidar os cálculos ou causar confusão no debate.

**Opções.** Focar apenas em HDD, já que foram neles que os conceitos de RAID 
foram moldados · Fazer a análise híbrida, mostrando as diferenças práticas do 
mesmo nível de RAID em HDD vs SSD.

- [ ] **Decisão:** 
- **Quem / quando:** 
- **Justificativa:** 
- **Consequência:** 

---

## 0.2 — Profundidade Matemática da Confiabilidade

**Por que importa.** O enunciado pede MTTF, MTTR, MTTDL e MTBF. Isso pode ser 
entregue como uma mera definição conceitual (texto) ou demonstrado através das 
fórmulas reais de confiabilidade usadas em sistemas de armazenamento.

**Opções.** Apenas definições de livro texto · Incluir e aplicar as equações para 
calcular (ou pelo menos ilustrar) a tolerância a falhas entre diferentes tipos de RAID.

- [ ] **Decisão:** 
- **Quem / quando:** 
- **Justificativa:** 
- **Consequência:** 

---

## 0.3 — Inclusão ou Foco Exclusivo nos RAID Não Padrão

**Por que importa.** RAIDs 1.5, 7, DP, S e Matrix são arranjos proprietários. 
Muita documentação deles sumiu ou foi absorvida por patentes de mercado. 
Aprofundar demais neles pode gerar informações falsas ou não auditáveis nos livros.

**Opções.** Dedicar uma seção inteira para eles com a mesma profundidade do RAID 0-6 
· Tratá-los apenas de forma ilustrativa para demonstrar que o mercado cria 
variações comerciais.

- [ ] **Decisão:** 
- **Quem / quando:** 
- **Justificativa:** 
- **Consequência:** 

---

## 1.10 — Posição da legenda nas tabelas

**Por que importa.** Várias tabelas `longtable` ou comparativas no relatório. A 
posição deve ser uniforme.

- [x] **Decisão:** legenda **abaixo** da tabela, em todas as `longtable`.
- **Quem / quando:** Grupo — 09/09/2026
- **Justificativa:** preferência do grupo e convenção da maioria dos periódicos de computação.
- **Consequência:** padrão travado para o LaTeX.

---

## 1.13 — Citação de fonte em inglês: original ou tradução

**Por que importa.** Os livros recomendados podem ter trechos fundamentais que o 
grupo consultou em versões em inglês (Garcia-Molina, por exemplo, ou manuais 
originais de especificações de hardware).

- [x] **Decisão:** trecho citado de fonte em inglês vai **traduzido no corpo**,
      entre aspas e em itálico, com a marca **`tradução nossa`** dentro da
      própria citação.
- **Quem / quando:** Grupo — 09/09/2026
- **Justificativa:** o idioma do material é o português, e facilita a leitura da banca.
- **Consequência:** Toda citação em língua estrangeira precisará ser traduzida no texto do relatório final, sem notas de rodapé longas com originais.

---

## Decisões que surgirem depois

Decisão tomada fora da Etapa 0 entra aqui, numerada a partir de 1.1, com o mesmo
formato. Toda decisão registrada aqui precisa de uma linha correspondente em
`postmortem.md` §2 — é ali que ela conta ponto.
