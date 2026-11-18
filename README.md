# Predição de patologias cardíacas em crianças e jovens (UCMF)

Estudo de caso da disciplina de **Mineração de Dados** do Bacharelado em Inteligência Artificial da Universidade Federal de Goiás (UFG). O trabalho aplica o processo de KDD a dados de cardiologia pediátrica para prever se um paciente apresenta patologia cardíaca (`NORMAL X ANORMAL`).

**Integrantes:** Ivanildo Hyvo, Igor Nucada, Gustavo Alves

## Dados

Os dados foram coletados no Real Hospital Português (Recife-PE) e anonimizados, com aprovação dos comitês de ética envolvidos. São atendimentos de pacientes de 0 a 19 anos, com dados antropométricos, sinais vitais, achados do exame físico, anamnese e motivo da consulta. Os dados são disponibilizados aqui apenas para fins acadêmicos e de reprodutibilidade.

| Arquivo | Descrição |
|---|---|
| `UCMF_raw.csv` | Base original: 12.873 atendimentos e 21 variáveis |
| `UCMF_limpo.csv` | Base após o pré-processamento da Parte 1: 11.402 atendimentos e 22 colunas |

## Notebooks

| Notebook | Conteúdo | Abrir |
|---|---|---|
| `colab_mineracao_A1.ipynb` | **Parte 1 — Pré-processamento:** análise univariada, identificação e tratamento dos problemas de qualidade (grafias, valores impossíveis, ausências disfarçadas, duplicatas), comparação Peso × Altura e análise bivariada com o alvo. Gera o `UCMF_limpo.csv`. | [![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/hyvo442/mineracao-ucmf/blob/main/colab_mineracao_A1.ipynb) |
| `colab_mineracao_A2.ipynb` | **Parte 2 — Modelagem:** seleção de variáveis, pipeline de pré-processamento, ajuste e comparação de três classificadores, teste de McNemar, matrizes de confusão e importância dos atributos. | [![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/hyvo442/mineracao-ucmf/blob/main/colab_mineracao_A2.ipynb) |

## Metodologia (Parte 2)

- **Variáveis removidas:** `SOPRO` (vazamento de informação: V de Cramér = 0,84 com o alvo), `MOTIVO2` (a categoria "6 - Sopro" reintroduz a mesma informação), `Convenio` (atributo administrativo), `ID` e as datas (resumidas pela idade).
- **Divisão:** 80% treino e 20% teste, estratificada pelo alvo.
- **Pré-processamento (dentro de um `Pipeline`, ajustado só no treino de cada fold):** medidas clínicas convertidas em z-score por faixa etária (e sexo), indicadores de ausência, one-hot nas categóricas e sintomas da anamnese como variáveis binárias.
- **Validação:** validação cruzada estratificada com 10 folds e ajuste de hiperparâmetros com `GridSearchCV`, usando o F1 como critério.
- **Modelos:** regressão logística (baseline), Random Forest e Gradient Boosting (`HistGradientBoostingClassifier`).
- **Avaliação final:** conjunto de teste usado uma única vez, com recall, precisão, F1, acurácia balanceada e ROC-AUC, além do teste de McNemar entre os modelos.

## Resultados no conjunto de teste

| Modelo | Recall | Precisão | F1 | Acurácia bal. | ROC-AUC |
|---|---|---|---|---|---|
| Regressão logística | 0,684 | 0,680 | 0,682 | 0,722 | 0,778 |
| Random Forest | 0,675 | 0,692 | 0,684 | 0,726 | 0,792 |
| Gradient Boosting | 0,693 | 0,695 | 0,694 | 0,733 | 0,789 |

Pelo teste de McNemar, nenhuma diferença entre os modelos foi estatisticamente significativa (p > 0,05).

## Como reproduzir

1. Abra o notebook desejado pelo botão **Open in Colab** acima.
2. Use **Ambiente de execução → Executar tudo**. Os dados são lidos diretamente deste repositório, sem precisar de nenhum arquivo local.

Todas as etapas aleatórias usam `random_state = 42` (`SEED`), então os resultados se repetem a cada execução. A Parte 1 roda em poucos minutos; a Parte 2 pode levar mais de uma hora no Colab gratuito, por causa das buscas em grade com 10 folds. Os notebooks já estão salvos com as saídas da execução completa, então os resultados podem ser consultados sem rodar o código.

**Bibliotecas:** pandas, NumPy, scikit-learn, SciPy, matplotlib e seaborn (todas já instaladas no Google Colab).
