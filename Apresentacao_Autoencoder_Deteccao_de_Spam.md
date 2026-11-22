# Apresentação — Autoencoder para detecção de spam em SMS

## Slide 1 — Capa
- Projeto: Autoencoder para detecção de spam em SMS
- Disciplina: Redes Neurais
- Integrantes: Raniel Santos, Pedro Miguel, João Guilherme e Antônio Gabriel
- Turma: 6º período — Manhã (2026.2)
- Data: 02/12/2026

## Slide 2 — Problema
- SMS podem conter publicidade abusiva, golpes e links suspeitos.
- Entrada: uma mensagem de texto SMS.
- Saída: NORMAL (ham) ou SPAM (suspeita).
- Indicador: erro de reconstrução comparado a um limite.

## Slide 3 — Objetivo
- Aprender o padrão de mensagens legítimas e sinalizar textos com reconstrução incomum.
- Tarefa: detecção de anomalias, avaliada como classificação binária.
- Treinamento one-class/semi-supervisionado: usa mensagens ham no treino e rótulos para avaliar o teste.

## Slide 4 — Conjunto de dados
- SMS Spam Collection, UCI Machine Learning Repository.
- 5.574 mensagens em inglês: 4.827 ham e 747 spam na distribuição divulgada pela base.
- Formato: rótulo e texto separados por tabulação.
- Fonte: https://archive.ics.uci.edu/dataset/228/sms

## Slide 5 — Preparação dos dados
- Remoção de textos vazios, duplicatas e mensagens com rótulos conflitantes.
- Divisão estratificada: 70% treino, 15% validação e 15% teste.
- TF-IDF ajustado somente no treino; validação e teste apenas transformados.
- Teste isolado até a avaliação final.

## Slide 6 — Baseline
- Regressão logística supervisionada com TF-IDF e pesos balanceados.
- Acurácia: 98,19%; precisão spam: 91,00%; recall spam: 94,79%; F1 spam: 92,86%.
- ROC-AUC: 99,70%; Average Precision: 98,34%.

## Slide 7 — Representação dos dados
- TF-IDF transforma cada SMS em vetor numérico.
- Cada posição representa um termo ou bigrama e seu peso relativo.
- Até 1.500 características, normalização L2.
- TF-IDF não é embedding contextual.

## Slide 8 — Arquitetura
- Entrada: vetor TF-IDF.
- Encoder: Dense(128, ReLU) → Dense(64, ReLU) → Dense(32, ReLU).
- Gargalo latente de 32 dimensões.
- Decoder: Dense(64, ReLU) → Dense(128, ReLU) → Dense(n_features, sigmoid).
- Treinamento somente com mensagens ham.

## Slide 9 — Funções e configurações
- ReLU nas camadas ocultas; sigmoid na saída de reconstrução.
- MSE como perda de reconstrução.
- Adam como otimizador.
- Não foi usada Binary Cross-Entropy, pois o modelo reconstrói vetores, não prevê diretamente o rótulo.

## Slide 10 — Treinamento
- Até 40 épocas; lote 32; Adam com taxa 0,001; Early Stopping com paciência 5.
- Resultado observado: 6 épocas, 9,42 segundos.
- Perda de treino caiu de 0,0665 para cerca de 0,0006637 na 2ª época; validação ficou próxima de 0,0006617.
- Pesos restaurados da melhor época (1).

## Slide 11 — Resultados do autoencoder
- Acurácia: 85,79%.
- Precisão spam: 0%; recall spam: 0%; F1 spam: 0%.
- ROC-AUC: 33,96%; Average Precision: 8,83%.
- Matriz: 664 verdadeiros negativos, 14 falsos positivos, 96 falsos negativos e 0 verdadeiros positivos.
- O autoencoder classificou todas as mensagens como normais.

## Slide 12 — Comparação com a baseline
| Modelo | Precisão (spam) | Recall (spam) | F1-score (spam) |
|---|---:|---:|---:|
| Regressão logística | 91,00% | 94,79% | 92,86% |
| Autoencoder | 0,00% | 0,00% | 0,00% |

Nesta execução, a regressão logística foi superior em todas as métricas comparadas.

## Slide 13 — Acertos e erros
- Acerto: usar uma mensagem ham que o notebook apresente como ham; 664 mensagens normais foram classificadas corretamente.
- Erro: usar uma mensagem spam que o notebook apresente como ham; houve 96 falsos negativos.
- Possível causa: o erro de reconstrução não diferenciou os exemplos spam dos padrões ham aprendidos.
- Executar a célula extra do notebook para imprimir o texto completo das mensagens de exemplo.

## Slide 14 — Limitações e questões éticas
- Base histórica, em inglês e desbalanceada.
- TF-IDF não captura plenamente contexto e intenção.
- Falsos positivos e falsos negativos têm consequências diferentes.
- Proteger dados pessoais e evitar bloqueios automáticos sem revisão.

## Slide 15 — Conclusão e demonstração
- O pipeline foi executado, mas o autoencoder não identificou spam nesta configuração.
- A baseline alcançou F1 de 92,86%; o autoencoder, 0%.
- Demonstrar o Colab, as métricas e a previsão de uma nova mensagem.
- Principal aprendizado: a representação e o limite de reconstrução precisam ser avaliados; uma rede mais complexa não garante melhor desempenho.
