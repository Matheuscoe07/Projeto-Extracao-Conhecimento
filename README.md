# Auditoria Cívica Algorítmica nos Gastos da CEAP
### Projeto Final — TNA 5773: Extração de Conhecimento

> **É possível isolar assinaturas comportamentais anômalas e detectar indícios de microdesvios no uso da cota parlamentar utilizando engenharia de variáveis e algoritmos não supervisionados?**

---

## Sobre o Projeto

Este projeto aplica técnicas de **Knowledge Discovery in Databases (KDD)** para auditar os gastos da **Cota para o Exercício da Atividade Parlamentar (CEAP)** da Câmara dos Deputados brasileira, cobrindo o período de **2019 a 2025** (56ª e 57ª legislaturas).

Inspirado pela [Operação Serenata de Amor](https://serenata.ai/), o estudo utiliza algoritmos de detecção de anomalias para identificar padrões atípicos de gasto que escapariam à fiscalização humana convencional, gerando um **ranking de indícios de atipicidade comportamental** por parlamentar.

---

## Autores

| Nome | 
|---|
| Gabriel C. Guimarães |
| Karem L. Silva |
| Keila Miranda-Moraes |
| Luíza Manso |
| Matheus Coelho |

**Disciplina:** TNA 5773 — Extração de Conhecimento: Transformando Dados em Conhecimento  
**Instituição:** [Sua instituição aqui]  
**Ano:** 2026

---

## Fonte de Dados

| Base | Descrição | Acesso |
|---|---|---|
| Portal de Dados Abertos da Câmara | Gastos CEAP por parlamentar (2019–2025) | [dadosabertos.camara.leg.br](https://dadosabertos.camara.leg.br) |
| Ato da Mesa nº 43/2009 (atualizado pelo nº 270/2023) | Tetos mensais da CEAP por UF | [camara.leg.br](https://www2.camara.leg.br/legin/int/atomes/2009/atodamesa-43-21-maio-2009-588364-normaatualizada-cd-mesa.html) |

---

## Metodologia

```
Coleta → Sanitização → Limpeza → Tetos CEAP → Feature Engineering
    → EDA → Isolation Forest → DBSCAN → Score Composto → Ranking
```

### Etapas

1. **Download automatizado** dos CSVs anuais via Portal de Dados Abertos
2. **Sanitização** de encoding (UTF-8 + BOM) e padronização de colunas
3. **Limpeza** — remoção de datas impossíveis, valores negativos, UFs inválidas
4. **Engenharia de variáveis** — criação de features de risco:
   - Notas fiscais seriadas (mesmo CNPJ, documentos consecutivos)
   - Concentração de gastos em fornecedor único
   - Meses com gasto "raspando" o teto mensal (±2%)
   - Proporção de notas de pessoa física
   - Gastos médios em alimentação
5. **Isolation Forest** — score de anomalia individual (300 estimadores, 5% contaminação)
6. **DBSCAN** — clusterização por densidade com redução PCA para visualização 2D
7. **Score composto ponderado** — combinação de sinais algorítmicos e heurísticos
8. **Ranking** de indícios de atipicidade comportamental

### Pesos do Score Composto

| Componente | Peso |
|---|---|
| Isolation Forest (`risco_iso`) | 40% |
| Acima do teto anual | 15% |
| Concentração em fornecedor único (> 70%) | 15% |
| Notas fiscais seriadas (> 5) | 10% |
| Gasto exato no limite (≥ 3 meses) | 10% |
| Excesso de NF pessoa física (> 30%) | 5% |
| Outlier DBSCAN | 5% |

---

## Bibliotecas Utilizadas

```
pandas · numpy · scikit-learn · plotly · matplotlib · seaborn
```

---

## Como Reproduzir

O projeto foi desenvolvido no **Google Colab**. Para reproduzir:

**1. Clone o repositório**
```bash
git clone https://github.com/seu-usuario/seu-repositorio.git
```

**2. Abra o notebook no Colab**

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

**3. Execute as células em ordem**

As células baixam os dados automaticamente do portal da Câmara. O download completo (2019–2025) leva alguns minutos na primeira execução e é armazenado em cache para execuções subsequentes.

**4. Para gerar o relatório em Quarto**
```python
!wget -q https://github.com/quarto-dev/quarto-cli/releases/download/v1.5.57/quarto-1.5.57-linux-amd64.deb
!dpkg -i quarto-1.5.57-linux-amd64.deb
!quarto render relatorio_final.qmd --to html
```
---

## Resultados Principais

- **1,5 milhão** de registros analisados (2019–2025)
- **247 outliers** detectados pelo Isolation Forest (~5% do total)
- **330 outliers** identificados pelo DBSCAN
- Variância explicada pelo PCA: **50,6%** (2 componentes)
- Parlamentar com maior score de indício de atipicidade: **Gabriel Mota (RR, 2025) — score 100/100**

> ⚠️ **Nota sobre responsabilidade analítica:** os scores gerados por este modelo indicam *indícios de atipicidade estatística*, não constituem afirmações de irregularidade ou ilegalidade. Os resultados devem ser interpretados como uma ferramenta de priorização para auditorias humanas, não como prova de conduta indevida.

---

## Referências

- AGGARWAL, C. C. **Outlier Analysis**. 2. ed. Springer, 2017.
- BRASIL. **Ato da Mesa nº 43, de 21 de maio de 2009**. Câmara dos Deputados, 2009.
- CHANDOLA, V.; BANERJEE, A.; KUMAR, V. Anomaly detection: A survey. **ACM Computing Surveys**, v. 41, n. 3, 2009.
- FAYYAD, U.; PIATETSKY-SHAPIRO, G.; SMYTH, P. From Data Mining to Knowledge Discovery in Databases. **AI Magazine**, v. 17, n. 3, 1996.
- LIU, F. T.; TING, K. M.; ZHOU, Z. Isolation Forest. **IEEE ICDM**, 2008.
- OKFN BRASIL. **Operação Serenata de Amor**. 2017. Disponível em: https://serenata.ai/
