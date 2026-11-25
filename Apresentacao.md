# Apresentação — Autoencoder para detecção de spam em SMS

## 1. Capa
Redes Neurais — Autoencoder para detecção de spam em SMS.
Integrantes: Raniel Santos, Pedro Miguel, João Guilherme e Antônio Gabriel.
6º período, manhã (2026.2), 02/12/2026.

## 2. Problema
SMS indesejados: publicidade abusiva, golpes e links suspeitos. Entrada: mensagem SMS. Saída: normal (ham) ou suspeita de spam, conforme erro de reconstrução.

## 3. Objetivo
Aprender padrões legítimos e sinalizar anomalias. Treinamento one-class com ham e avaliação binária.

## 4. Dados
SMS Spam Collection (UCI), 5.574 SMS em inglês. Após a limpeza: 5.160, sendo 4.518 ham e 642 spam.

## 5. Preparação
Remoção de 414 duplicatas; divisão estratificada 3.612/774/774 (70/15/15). Vocabulários ajustados apenas no treino.

## 6. Baseline
Regressão logística com TF-IDF e pesos balanceados. Acurácia 98,19%; precisão 91%; recall 94,79%; F1 92,86%; ROC-AUC 99,70%; AP 98,34%.

## 7. Representação
TF-IDF com 1.500 atributos e normalização L2 para a baseline; Bag-of-Words binário com 3.000 atributos para o autoencoder. Unigramas e bigramas. Não são embeddings contextuais.

## 8. Arquitetura
3000 → 256 → 128 → 64 → 128 → 256 → 3000; 1.621.752 parâmetros treináveis. Treino com ham.

## 9. Funções
ReLU, sigmoid, BCE ponderada (peso 1 para termos presentes e 0,005 para ausentes), Adam. A saída sigmoid reconstrói a presença de termos, não a probabilidade de spam.

## 10. Treinamento
Até 60 épocas, lote 32, taxa 0,0005, Early Stopping com paciência 7. Execução: 17 épocas em 126,45 s na CPU. Melhor época 10, perda de validação 0,3627. Curva original salva no notebook.

## 11. Resultados
Acurácia 93,41%; precisão 66,67%; recall 93,75%; F1 77,92%; ROC-AUC 98,31%; AP 94,46%. Matriz: VN=633, FP=45, FN=6, VP=90. 90 de 96 spams identificados.

## 12. Comparação
Baseline: F1 92,86%; autoencoder: F1 77,92%. A primeira versão detectou 0 spams. Limite P95 de ham: 0,58169652. Medianas de validação: ham 0,37128737; spam 1,43385740. Baseline ainda tem maior precisão e F1.

## 13. Acertos e erros
Exemplos do teste, abreviados e sem números de telefone:
- Ham correto: “Sez, hows u & de arab boy? Hope u r all good…” — erro 0,41295141.
- Spam correto: “WINNER!! As a valued network customer…” — erro 2,13195375.
- Falso positivo: “I've got it down to a tea. not sure which flavour” — erro 0,67480151.
- Falso negativo: “You have 1 new message. Please call…” — erro aproximado 0,471907.
Limite: 0,58169652.

## 14. Limitações e ética
A base contém mensagens históricas em inglês e apresenta desbalanceamento entre as classes. O vocabulário limitado pode gerar falsos positivos e falsos negativos. O tratamento das mensagens deve respeitar a privacidade dos usuários. A validação com novos dados é uma possibilidade de trabalho futuro.

## 15. Conclusão e demonstração
A versão final identifica spam por anomalia, embora a baseline seja superior em F1. Demonstração registrada: WINNER! → SPAM, erro 1,07751383 acima de 0,58169652.
