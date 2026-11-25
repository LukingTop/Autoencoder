# Autoencoder para detecção de spam em SMS

Trabalho final — Redes Neurais | 6º período, manhã (2026.2) | 02/12/2026

**Integrantes:** Raniel Santos, Pedro Miguel, João Guilherme e Antônio Gabriel.

## Objetivo

Detectar mensagens SMS suspeitas em inglês por meio de um autoencoder treinado apenas com mensagens legítimas (`ham`). A decisão é baseada no erro de reconstrução e não em uma probabilidade de spam. A regressão logística supervisionada é a baseline.

## Dados e preparação

Fonte: [SMS Spam Collection (UCI)](https://archive.ics.uci.edu/dataset/228/sms). A execução carregou 5.574 mensagens; removeu 414 duplicatas exatas e nenhum texto ambíguo. Restaram 5.160 mensagens: 4.518 ham e 642 spam.

| Subconjunto | Quantidade |
|---|---:|
| Treino (70%) | 3.612 |
| Validação (15%) | 774 |
| Teste (15%) | 774 |

Divisão estratificada com semente 42. Vocabulários ajustados apenas no treino. Baseline: TF-IDF, 1.500 características, unigramas e bigramas, frequência sublinear, normalização L2 e `class_weight="balanced"`. Autoencoder: Bag-of-Words binário, 3.000 características, unigramas e bigramas. Não são embeddings contextuais.

## Arquitetura final

`3000 → 256 → 128 → 64 → 128 → 256 → 3000`

- Encoder e decoder densos; ReLU nas camadas ocultas e sigmoid na saída.
- 1.621.752 parâmetros treináveis.
- Binary Cross-Entropy ponderada: peso 1 para termos presentes e 0,005 para ausentes, normalizada pela soma dos pesos de cada exemplo.
- Adam, taxa 0,0005; lote 32; máximo de 60 épocas; Early Stopping com paciência 7 e restauração dos melhores pesos.
- Treino e monitoramento de perda somente com ham. Limite de anomalia: percentil 95 dos erros de ham na validação.

## Resultados registrados no Colab

TensorFlow 2.21.0, CPU. Foram executadas 17 épocas em 126,45 segundos; os pesos da época 10 foram restaurados (perda de validação 0,3627). Limite: 0,58169652. Medianas dos erros na validação: ham 0,37128737 e spam 1,43385740.

| Métrica | Regressão logística | Autoencoder final |
|---|---:|---:|
| Acurácia | 98,19% | 93,41% |
| Precisão (spam) | 91,00% | 66,67% |
| Recall (spam) | 94,79% | 93,75% |
| F1-score (spam) | 92,86% | 77,92% |
| ROC-AUC | 99,70% | 98,31% |
| Average Precision | 98,34% | 94,46% |

Matriz de confusão (linhas = classe real; colunas = prevista; ordem ham, spam):

| Modelo | VN | FP | FN | VP |
|---|---:|---:|---:|---:|
| Regressão logística | 669 | 9 | 5 | 91 |
| Autoencoder | 633 | 45 | 6 | 90 |

A versão inicial (TF-IDF e MSE) detectou 0 dos 96 spams; a versão final detectou 90. A baseline supervisionada ainda tem F1 e precisão superiores.

## Demonstração

- `Are we still meeting for lunch tomorrow?` → NORMAL, erro 0,31886733.
- `Congratulations! You have won a free prize. Claim now!` → SPAM, erro 3,34355076.
- `WINNER!` → SPAM, erro 1,07751383.

## Como executar

1. Abra o notebook `Autoencoder.ipynb` no Google Colab. No GitHub, o mesmo projeto está disponível no arquivo [`Projeto_Autoencoder_Deteccao_de_Spam.ipynb`](Projeto_Autoencoder_Deteccao_de_Spam.ipynb), que pode ser aberto pelo Colab (Arquivo → Abrir notebook → GitHub) ou baixado para upload.
2. Execute todas as células em ordem. A primeira instala as dependências, incluindo ipywidgets para a interface; o download da base requer internet.
3. Na seção 13, digite uma mensagem em inglês na interface e clique em **Analisar mensagem**. A classificação, o erro de reconstrução, o limite de anomalia e a interpretação serão exibidos na própria interface.
4. Confira o resumo da seção 14. As saídas da execução registrada estão preservadas; tempos e métricas podem variar em nova execução.

Não é necessário treinar novamente para consultar os resultados salvos. Os pesos do modelo não estão incluídos no notebook; execute o treinamento para uma nova demonstração.

## Limitações e cuidados

Dados históricos em inglês; vocabulário limitado, sem compreensão contextual; possibilidade de falsos positivos e falsos negativos. O limite P95 é uma heurística e não garante uma taxa de falsos alarmes de 5% em novos dados. O resultado é uma suspeita, não uma certeza. Respeitar privacidade e proteção de dados; evitar bloqueio automático sem revisão. Futuras melhorias: validar o desempenho em novos conjuntos de dados, ampliar o suporte ao português e explorar embeddings e outras estratégias de calibração.

## Referências

- UCI Machine Learning Repository. SMS Spam Collection. https://archive.ics.uci.edu/dataset/228/sms
- Almeida, T. A.; Gómez Hidalgo, J. M.; Yamakami, A. Contributions to the study of SMS spam filtering: new collection and results. 2011.
- Pedregosa et al. Scikit-learn: Machine Learning in Python. JMLR, 2011.
- Abadi et al. TensorFlow: A System for Large-Scale Machine Learning. 2016.

A declaração de uso de IA consta no notebook.
