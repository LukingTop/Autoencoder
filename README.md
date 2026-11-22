# Autoencoder para detecção de spam em SMS

Projeto acadêmico de Redes Neurais. O protótipo recebe uma mensagem de texto e indica se ela parece **normal (ham)** ou **spam**. A solução usa um autoencoder como detector de anomalias: a rede aprende a reconstruir mensagens legítimas e sinaliza mensagens com erro de reconstrução elevado.

> **Escopo:** o conjunto de dados é composto por mensagens em inglês. O sistema não foi validado para português e não deve ser tratado como filtro comercial pronto para uso.

## Integrantes

- Integrante 1: `[preencher]`
- Integrante 2: `[preencher]`
- Integrante 3: `[preencher]`
- Turma: `[preencher]`
- Disciplina: Redes Neurais

## Problema e aplicação

Mensagens SMS indesejadas podem conter publicidade, tentativas de fraude ou links suspeitos. O projeto investiga se padrões de mensagens legítimas podem ser aprendidos por uma rede neural e usados para identificar mensagens que se afastam desse padrão.

**Entrada:** texto de uma mensagem SMS.

**Saída:** `NORMAL (ham)` ou `SPAM (suspeita)`, acompanhada do erro de reconstrução e do limite aplicado.

**Tipo de tarefa:** detecção de anomalias com treinamento **one-class/semi-supervisionado** (o rótulo é usado para separar exemplos legítimos no treino), avaliada como classificação binária usando os rótulos disponíveis no teste.

## Conjunto de dados

- **Nome:** SMS Spam Collection.
- **Origem:** UCI Machine Learning Repository.
- **Link:** https://archive.ics.uci.edu/dataset/228/sms
- **Tamanho divulgado:** 5.574 mensagens.
- **Classes:** `ham` (mensagem legítima) e `spam`.
- **Formato:** arquivo de texto separado por tabulação, com rótulo e mensagem.
- **Idioma:** inglês.
- **Distribuição divulgada:** 4.827 mensagens legítimas e 747 spam.

A base é desbalanceada. Por esse motivo, o projeto analisa precisão, recall e F1 da classe spam, além da acurácia.

## Preparação dos dados

1. Download do arquivo oficial da UCI.
2. Remoção de textos vazios e registros duplicados; textos com rótulos conflitantes são descartados.
3. Divisão estratificada em 70% treino, 15% validação e 15% teste.
4. Ajuste do vocabulário TF-IDF exclusivamente com o treino; validação e teste apenas são transformados com esse vocabulário.
5. Conversão das matrizes esparsas para matrizes densas para entrada no Keras.

A separação estratificada preserva aproximadamente as proporções de classe. O conjunto de teste não participa do treinamento nem da calibração do limite.

## Representação dos textos

É utilizado **TF-IDF** com vocabulário de até 1.500 características e unigramas/bigramas. Cada mensagem é convertida em um vetor numérico que pondera a importância dos termos. TF-IDF não é um embedding semântico: não representa, por si só, o significado contextual das frases.

## Modelos e ferramentas

### Baseline

Regressão logística com vetores TF-IDF, `class_weight="balanced"` e `max_iter=1000`. A baseline é supervisionada e aprende com os rótulos `ham` e `spam`.

### Autoencoder

- Entrada: vetor TF-IDF.
- Encoder: Dense(128, ReLU) → Dense(64, ReLU) → Dense(32, ReLU), formando o gargalo latente.
- Decoder: Dense(64, ReLU) → Dense(128, ReLU) → Dense(n_features, sigmoid).
- Treinamento: somente mensagens `ham` do subconjunto de treino.
- Perda: erro quadrático médio (MSE), pois o objetivo é reconstruir o vetor.
- Otimizador: Adam, taxa de aprendizado 0,001.
- Épocas: até 40, com lote de 32 e Early Stopping.
- Limite: percentil 95 do erro de reconstrução das mensagens `ham` da validação.
- Regra: erro acima do limite = suspeita de spam.

A função sigmoid está na camada de reconstrução, não como uma saída de probabilidade de spam. A classificação é feita comparando o erro com o limite calibrado.

## Etapas do projeto

1. Carregamento e inspeção da base.
2. Limpeza e deduplicação.
3. Divisão treino/validação/teste.
4. Vetorização TF-IDF.
5. Treinamento da baseline de regressão logística.
6. Treinamento do autoencoder somente com mensagens legítimas.
7. Calibração do limite de anomalia na validação.
8. Avaliação no teste usando acurácia, precisão, recall, F1, ROC-AUC, average precision e matriz de confusão.
9. Análise de falsos positivos e falsos negativos.
10. Demonstração com uma nova mensagem.

## Resultados da execução no Google Colab

Na execução registrada pela equipe, o conjunto de teste apresentou os seguintes resultados:

| Métrica | Regressão logística | Autoencoder |
|---|---:|---:|
| Acurácia | 98,19% | 85,79% |
| Precisão (spam) | 91,00% | 0,00% |
| Recall (spam) | 94,79% | 0,00% |
| F1-score (spam) | 92,86% | 0,00% |
| ROC-AUC | 99,70% | 33,96% |
| Average Precision | 98,34% | 8,83% |

O autoencoder previu todas as 774 mensagens de teste como normais. A matriz de confusão foi: 664 verdadeiros negativos, 14 falsos positivos, 96 falsos negativos e nenhum verdadeiro positivo. Portanto, nesta configuração, o autoencoder não detectou mensagens spam e ficou abaixo da baseline.

O treinamento executou 6 épocas em aproximadamente 9,42 segundos. O Early Stopping restaurou os pesos da melhor época. Esses resultados devem ser interpretados como uma limitação observada nesta experiência, e não como prova de que autoencoders não podem ser usados para detecção de spam.

## Como executar no Google Colab

1. Faça upload de `Projeto_Autoencoder_Deteccao_de_Spam.ipynb` no Google Colab ou abra o arquivo pelo Google Drive.
2. Selecione **Ambiente de execução → Executar tudo**.
3. Autorize a instalação das bibliotecas, se solicitado.
4. O notebook fará o download da base da UCI automaticamente; portanto, a sessão precisa de acesso à internet.
5. Execute todas as células em ordem. Ao final, digite uma mensagem em inglês na demonstração interativa.

Não é necessário baixar o conjunto de dados manualmente.

A apresentação PowerPoint editável é fornecida como arquivo de entrega separado; o roteiro completo dos 15 slides também está no repositório em Markdown.\n\n## Estrutura do repositório

```text
.
├── Projeto_Autoencoder_Deteccao_de_Spam.ipynb
├── README.md
├── requirements.txt
├── .gitignore
└── Apresentacao_Autoencoder_Deteccao_de_Spam.md
```

## Limitações e questões éticas

- Os dados estão em inglês e podem não representar mensagens atuais ou mensagens em português.
- A base é desbalanceada e relativamente pequena para representar todo o universo de SMS.
- TF-IDF não captura plenamente contexto, intenção ou ordem das palavras.
- Mensagens legítimas incomuns podem ser sinalizadas como spam; spams com vocabulário comum podem não ser detectados.
- O limite de reconstrução é uma heurística calibrada na validação, não uma garantia.
- Mensagens podem conter informações pessoais. Uma aplicação real deve minimizar coleta e retenção, proteger os dados e respeitar a legislação aplicável.
- O protótipo deve apoiar, e não substituir sem revisão, a decisão do usuário.

## Declaração de uso de IA

A inteligência artificial generativa foi utilizada como apoio à organização do projeto, à explicação dos conceitos e à elaboração inicial do notebook e da documentação. Os integrantes devem revisar, executar, adaptar e compreender o código e os resultados apresentados.

## Referências

- UCI MACHINE LEARNING REPOSITORY. **SMS Spam Collection**. Disponível em: <https://archive.ics.uci.edu/dataset/228/sms>. Acesso em: 17 nov. 2026.
- ALMEIDA, T. A.; GÓMEZ HIDALGO, J. M.; YAMAKAMI, A. **Contributions to the study of SMS Spam Filtering: New Collection and Results**. Proceedings of the 2011 ACM Symposium on Document Engineering, 2011.
- PEDREGOSA, F. et al. Scikit-learn: Machine Learning in Python. *Journal of Machine Learning Research*, v. 12, p. 2825–2830, 2011.
- ABADI, M. et al. TensorFlow: A System for Large-Scale Machine Learning. 2016.
