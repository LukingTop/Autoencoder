# Roteiro da apresentação — Autoencoder para detecção de spam em SMS

Este roteiro corresponde à apresentação PowerPoint editável entregue junto ao projeto. Preencha os campos entre colchetes e, principalmente, insira os resultados obtidos ao executar o notebook.

## Slide 1 — Capa
- Autoencoder para detecção de spam em SMS
- Disciplina: Redes Neurais
- Integrantes: [nomes]
- Turma: [turma]
- Data: [data]

## Slide 2 — Problema
- SMS podem conter publicidade abusiva, golpes e links suspeitos.
- Solução proposta: receber o texto de uma mensagem e indicar normal (ham) ou suspeita de spam.
- Usuários e sistemas de triagem podem se beneficiar da sinalização.
- A decisão é uma indicação, não uma prova de fraude.

## Slide 3 — Objetivo
- Aprender padrões de mensagens legítimas e detectar mensagens que se afastam desse padrão.
- Tipo de tarefa: detecção de anomalias, avaliada como classificação binária.
- Treinamento one-class/semi-supervisionado: rótulos são usados para selecionar exemplos ham no treino.

## Slide 4 — Conjunto de dados
- SMS Spam Collection, UCI Machine Learning Repository.
- 5.574 mensagens em inglês; ham e spam.
- Formato: rótulo e mensagem separados por tabulação.
- Distribuição divulgada: 4.827 ham e 747 spam.
- Fonte: https://archive.ics.uci.edu/dataset/228/sms

## Slide 5 — Preparação dos dados
- Remoção de textos vazios, duplicatas e textos com rótulos conflitantes.
- Divisão estratificada: 70% treino, 15% validação e 15% teste.
- TF-IDF ajustado somente no treino; validação e teste apenas transformados.
- O conjunto de teste não participa do treino nem da escolha do limite.

## Slide 6 — Baseline
- Regressão logística com vetores TF-IDF.
- Treino supervisionado com rótulos ham/spam.
- class_weight=balanced para lidar com desbalanceamento.
- Inserir precisão, recall e F1 da classe spam obtidos no teste.

## Slide 7 — Representação dos dados
- TF-IDF converte cada SMS em vetor numérico.
- Cada posição representa um termo ou bigrama e seu peso.
- Vocabulário com até 1.500 características.
- TF-IDF não é embedding contextual: não compreende completamente significado e contexto.

## Slide 8 — Arquitetura
- Entrada: vetor TF-IDF.
- Encoder: Dense(128, ReLU) → Dense(64, ReLU) → Dense(32, ReLU).
- Gargalo latente com 32 dimensões.
- Decoder: Dense(64, ReLU) → Dense(128, ReLU) → Dense(n_features, sigmoid).
- Treinamento somente com exemplos ham.

## Slide 9 — Funções utilizadas
- ReLU nas camadas ocultas: permite aprender relações não lineares.
- Sigmoid na saída de reconstrução: valores entre 0 e 1.
- MSE: mede o erro entre vetor de entrada e reconstrução.
- Adam: otimizador que ajusta os pesos.
- Não usamos Binary Cross-Entropy: o autoencoder reconstrói vetores, não prevê diretamente o rótulo spam.

## Slide 10 — Treinamento
- Até 40 épocas; lote de 32.
- Adam com taxa 0,001.
- Early Stopping com paciência de 5 épocas.
- Limite de anomalia: percentil 95 do erro das mensagens ham de validação.
- Inserir curva de perda treino × validação, época final e tempo observado.

## Slide 11 — Resultados
- Inserir acurácia, precisão, recall e F1 da classe spam.
- Inserir ROC-AUC e Average Precision, se relevantes.
- Mostrar a matriz de confusão do autoencoder.
- Usar os números reais gerados no notebook.

## Slide 12 — Comparação com a baseline
- Comparar regressão logística e autoencoder com as mesmas métricas no teste.
- Responder: o autoencoder superou a baseline? A diferença é relevante?
- O custo adicional da rede neural se justifica?
- Qual solução seria escolhida em uma aplicação real?

## Slide 13 — Acertos e erros
- Mostrar um exemplo classificado corretamente.
- Mostrar um falso positivo (ham classificado como spam) ou falso negativo (spam classificado como ham).
- Informar resposta esperada, previsão e possível causa.
- Usar mensagens reais exibidas pela célula de análise de erros.

## Slide 14 — Limitações e questões éticas
- Base histórica, em inglês e desbalanceada.
- TF-IDF não captura contexto ou intenção completa.
- Falsos positivos podem ocultar mensagens legítimas; falsos negativos deixam spam passar.
- Proteger dados pessoais e evitar bloqueios automáticos sem revisão.
- Melhorias: dados recentes e em português, embeddings contextuais e calibração do limite.

## Slide 15 — Conclusão e demonstração
- Responder se o objetivo foi alcançado e se o modelo aprendeu padrões úteis.
- Comparar o resultado com a baseline e apontar melhorias.
- Demonstrar notebook no Colab, entrada de nova mensagem e previsão.
- Declarar o uso de IA como apoio; todos devem compreender o código.
