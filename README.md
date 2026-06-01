# Relatório Técnico — Encadeamento de Prompts para Aplicativo de Questões de Concursos

## 4.1. Diagrama do Fluxo — Workflow

```text
[Usuário envia a questão]
              ↓
[Prompt 1 — Triagem/Classificação]
Identifica banca, matéria, tipo de questão e dificuldade
              ↓
[Prompt 2 — Verificação de Erros]
Verifica se a questão está completa, se há alternativas, ambiguidades ou problemas
              ↓
[Prompt 3 — Resolução e Diagnóstico]
Resolve a questão, justifica a resposta e avalia o desempenho do aluno
              ↓
[Resultado Final]
Exibe resposta correta, justificativa, erro provável do aluno e recomendação de estudo
```

## 4.2. Biblioteca de Prompts

### Prompt 1 — Triagem/Classificação

```text
Você é uma IA especializada em classificar questões de concursos públicos.

Analise a questão abaixo e identifique suas principais informações.

Questão:
{QUESTAO}

Banca:
{BANCA}

Alternativas:
{ALTERNATIVAS}

Classifique a questão seguindo esta estrutura:

{
  "Banca Identificada": "",
  "Matéria": "",
  "Tipo de Questão": "",
  "Dificuldade Estimada": "",
  "Quantidade de Alternativas": "",
  "Observações": ""
}

Regras:
1. Identifique a banca, caso ela seja informada.
2. Identifique a matéria da questão.
3. Classifique o tipo da questão.
4. Estime a dificuldade como fácil, média ou difícil.
5. Não altere o enunciado original.
6. Não altere as alternativas originais.
7. Caso algum dado esteja ausente, informe em "Observações".
```

### Prompt 2 — Verificação de Erros na Questão

```text
Você é uma IA responsável por verificar problemas em questões de concursos públicos antes da resolução.

Analise a questão abaixo:

Questão:
{QUESTAO}

Banca:
{BANCA}

Alternativas:
{ALTERNATIVAS}

Verifique se existe algum problema na questão, como:
- Enunciado incompleto;
- Ausência de alternativas;
- Alternativas duplicadas;
- Erro de formatação;
- Banca não informada;
- Ambiguidade;
- Falta de dados para resolver;
- Dependência de gabarito oficial.

Responda usando esta estrutura:

{
  "Enunciado Identificado": "",
  "Banca Informada": "",
  "Alternativas Encontradas": "",
  "Problema Detectado": "",
  "Tipo de Problema": "",
  "Pode Resolver com Segurança": "",
  "Ação Tomada pelo Sistema": "",
  "Observações": ""
}

Regras:
1. Não altere o texto original da questão.
2. Não invente alternativas originais.
3. Não indique resposta correta sem base suficiente.
4. Caso a questão esteja incompleta, informe claramente o problema.
5. Caso seja possível resolver com segurança, informe isso no campo adequado.
```

### Prompt 3 — Resolução e Diagnóstico do Aluno

```text
Você é uma IA especialista em concursos públicos, atuando como um mestre em concursos e um concurseiro experiente.

Resolva a questão abaixo de forma fria, técnica e objetiva.

Questão:
{QUESTAO}

Banca:
{BANCA}

Alternativas:
{ALTERNATIVAS}

Resposta marcada pelo aluno:
{RESPOSTA_DO_ALUNO}

Dados da verificação anterior:
{DADOS_DA_VERIFICACAO}

Siga esta estrutura:

{
  "Resposta Correta": "",
  "Justificativa": "",
  "Análise das Alternativas": "",
  "Possível Erro do Aluno": "",
  "Desempenho do Aluno": "",
  "Recomendação de Estudo": "",
  "Observações": ""
}

Regras:
1. Resolva a questão com base em lógica, conteúdo técnico e interpretação.
2. Justifique a resposta correta.
3. Aponte o erro provável do aluno, caso ele tenha errado.
4. Informe se o erro foi causado por falta de conteúdo, interpretação, desatenção ou erro de cálculo.
5. Faça uma breve análise do desempenho do aluno.
6. Recomende o que o aluno deve estudar.
7. Caso dependa de gabarito oficial, legislação atualizada ou informação externa, informe isso claramente.
8. Não altere o enunciado nem as alternativas.
```

## 4.3.1. Entrada do Teste — Prompt Utilizado com Saída Esperada

| Teste                                  | Entrada do Teste                                                                                                                                                                                | Saída Esperada                                                                                                                                                                                                                                                               |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Teste 1 — Questão completa             | `QUESTAO: Em uma prova com 60 questões, Pedro acertou 45. Qual foi o percentual de acertos? BANCA: FGV. ALTERNATIVAS: A) 60% B) 65% C) 70% D) 75% E) 80%. RESPOSTA_DO_ALUNO: C`                 | O sistema deve identificar a banca FGV, classificar a questão como Matemática/Porcentagem, verificar que a questão está completa, resolver corretamente e apontar que a resposta correta é D) 75%. Também deve informar que o aluno errou provavelmente por erro de cálculo. |
| Teste 2 — Questão sem alternativas     | `QUESTAO: Explique o princípio da legalidade na Administração Pública. BANCA: Não informada. ALTERNATIVAS: Não informadas. RESPOSTA_DO_ALUNO: Sem resposta`                                     | O sistema deve identificar que a questão não possui alternativas, classificar como questão incompleta ou discursiva, informar que não é possível marcar uma alternativa correta e indicar que a resposta depende do formato esperado ou do gabarito oficial.                 |
| Teste 3 — Usuário sem resposta         | `QUESTAO: Qual é o resultado de 12 x 8? BANCA: IBFC. ALTERNATIVAS: A) 80 B) 88 C) 96 D) 104. RESPOSTA_DO_ALUNO: Em branco`                                                                      | O sistema deve identificar que a questão está completa, resolver corretamente como C) 96 e registrar que o aluno deixou a questão em branco. Deve indicar possível insegurança, falta de tempo ou abandono da questão.                                                       |
| Teste 4 — Mais de uma resposta marcada | `QUESTAO: Qual alternativa apresenta um número primo? BANCA: VUNESP. ALTERNATIVAS: A) 4 B) 6 C) 7 D) 9. RESPOSTA_DO_ALUNO: C e D`                                                               | O sistema deve identificar que a questão possui apenas uma resposta correta, exibir aviso de que somente uma alternativa pode ser selecionada e considerar C como correta se for necessário validar o conteúdo.                                                              |
| Teste 5 — Questão ambígua              | `QUESTAO: Marque a alternativa correta sobre legislação atual. BANCA: FGV. ALTERNATIVAS: A) Texto incompleto B) Texto incompleto C) Texto incompleto D) Texto incompleto. RESPOSTA_DO_ALUNO: A` | O sistema deve detectar ambiguidade e falta de informações suficientes. Não deve indicar uma resposta correta sem base. Deve informar que a questão depende do texto completo, da lei atualizada ou do gabarito oficial.                                                     |

## 4.3.2. Saída Real da IA

| Teste                                  | Saída Real da IA                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Teste 1 — Questão completa             | `{ "Banca Identificada": "FGV", "Matéria": "Matemática", "Tipo de Questão": "Múltipla escolha", "Dificuldade Estimada": "Fácil", "Problema Detectado": "Nenhum", "Pode Resolver com Segurança": "Sim", "Resposta Correta": "D) 75%", "Justificativa": "45 acertos em 60 questões equivalem a 45 ÷ 60 = 0,75, ou seja, 75%.", "Possível Erro do Aluno": "Erro de cálculo ou escolha precipitada da alternativa.", "Desempenho do Aluno": "O aluno compreendeu parcialmente a ideia de porcentagem, mas errou a conversão final.", "Recomendação de Estudo": "Revisar porcentagem, frações e divisão aplicada a questões de prova.", "Observações": "Questão resolvida com segurança." }`                                                                                                                                          |
| Teste 2 — Questão sem alternativas     | `{ "Banca Identificada": "Não informada", "Matéria": "Direito Administrativo", "Tipo de Questão": "Discursiva ou incompleta", "Dificuldade Estimada": "Média", "Problema Detectado": "Ausência de alternativas", "Pode Resolver com Segurança": "Parcialmente", "Resposta Correta": "Não aplicável", "Justificativa": "A questão pede explicação sobre o princípio da legalidade, mas não apresenta alternativas nem padrão de correção.", "Possível Erro do Aluno": "O aluno não respondeu ou não recebeu uma questão completa.", "Desempenho do Aluno": "Não é possível avaliar desempenho objetivo sem resposta desenvolvida ou gabarito.", "Recomendação de Estudo": "Revisar princípios da Administração Pública, especialmente legalidade.", "Observações": "A resposta depende do formato esperado pelo avaliador." }`    |
| Teste 3 — Usuário sem resposta         | `{ "Banca Identificada": "IBFC", "Matéria": "Matemática", "Tipo de Questão": "Múltipla escolha", "Dificuldade Estimada": "Fácil", "Problema Detectado": "Resposta em branco", "Pode Resolver com Segurança": "Sim", "Resposta Correta": "C) 96", "Justificativa": "12 x 8 = 96.", "Possível Erro do Aluno": "Ausência de resposta, possível insegurança, distração ou falta de tempo.", "Desempenho do Aluno": "Não houve tentativa registrada, então o desempenho não pode ser medido pelo acerto ou erro.", "Recomendação de Estudo": "Treinar operações básicas e controle de tempo.", "Observações": "O sistema deve salvar a questão como não respondida." }`                                                                                                                                                               |
| Teste 4 — Mais de uma resposta marcada | `{ "Banca Identificada": "VUNESP", "Matéria": "Matemática", "Tipo de Questão": "Múltipla escolha", "Dificuldade Estimada": "Fácil", "Problema Detectado": "Mais de uma alternativa marcada em questão de resposta única", "Pode Resolver com Segurança": "Sim", "Resposta Correta": "C) 7", "Justificativa": "O número 7 é primo, pois possui apenas dois divisores: 1 e ele mesmo.", "Possível Erro do Aluno": "Marcação indevida de mais de uma alternativa.", "Desempenho do Aluno": "O aluno identificou a alternativa correta, mas cometeu erro operacional ao marcar também uma alternativa incorreta.", "Recomendação de Estudo": "Revisar números primos e prestar atenção ao formato da questão.", "Observações": "O sistema deve exibir um pop-up simples informando que esta questão permite apenas uma resposta." }` |
| Teste 5 — Questão ambígua              | `{ "Banca Identificada": "FGV", "Matéria": "Legislação", "Tipo de Questão": "Múltipla escolha", "Dificuldade Estimada": "Indefinida", "Problema Detectado": "Enunciado e alternativas incompletas", "Pode Resolver com Segurança": "Não", "Resposta Correta": "Não é possível determinar", "Justificativa": "A questão não apresenta conteúdo suficiente para análise técnica.", "Possível Erro do Aluno": "O aluno marcou uma alternativa sem dados suficientes para validar a resposta.", "Desempenho do Aluno": "Não é possível avaliar o desempenho com segurança.", "Recomendação de Estudo": "Utilizar a questão completa e consultar legislação atualizada quando necessário.", "Observações": "A resposta depende do enunciado completo, das alternativas reais e possivelmente do gabarito oficial." }`                 |
