# DBA Learning Journey

## Revisão e Fortalecimento do conhecimento em SQL Server

### **Plano de Atividades: Semana 1 - Criação de Índices no SQL Server**

#### **Objetivo:**
Entender os diferentes tipos de índices no SQL Server e praticar a criação e otimização de índices para melhorar o desempenho das consultas.

#### **Atividades:**

1. **Estudo Teórico:**
   - **Leitura:**
     - Documentação oficial do SQL Server sobre índices (Clustered, Non-Clustered, Unique, Full-text).
   - **Vídeos/Artigos:**
     - Procure vídeos no YouTube ou artigos em blogs que expliquem boas práticas de indexação.

2. **Prática:**
   - **Criação de Banco de Dados de Teste:**
     - Crie um banco de dados com tabelas contendo um grande volume de dados (por exemplo, registros de vendas ou transações).
   - **Implementação de Índices:**
     - Crie índices Clustered e Non-Clustered nas tabelas e compare o tempo de execução de consultas antes e depois de adic"io"nar os índices.
     - Experimente criar índices Unique e Full-text para ver como eles afetam as operações de busca e integridade dos dados.
   - **Análise de Performance:**
     - Use o SQL Profiler ou o Execut"io"n Plan para analisar como os índices impactam as consultas.

3. **Revisão e Reflexão:**
   - **Documentação:**
     - Registre suas observações em um documento, anotando o impacto dos índices na performance e as lições aprendidas.
   - **Perguntas Reflexivas:**
     - "Quais índices melhoraram mais o desempenho?"
     - "Quando devo evitar o uso de certos tipos de índices?"

#### **Checkpoint:**
- **Data:** Final da Semana 1 (próxima terça-feira).
- **O que vamos revisar:**
  - Seus resultados práticos (criação de índices e análise de performance).
  - Qualquer dificuldade ou dúvida que você tenha encontrado durante a semana.
  - Ajustes necessários para a próxima etapa do plano de estudos.

Boa sorte nesta primeira semana de atividades! Se precisar de qualquer ajuda durante a semana, estou aqui para te apoiar. Vamos marcar um checkpoint para a próxima terça-feira?

---

## Dia 1

### Udemy | SQL Server Query Tuning (SQL Server Expert)

### Arquitetura de Tabelas e Índices no SQL Server

ATENÇÃO, ATENÇÃO!!! NECESSÁRIO COLOCAR AQUI O ASSUNTO QUE FOI ABORDADO

### O que é um Otimizador de Consultas?

- Otimizador com Base em Regras (**Rule Based Optimizer - RBO***)
- Otimizador com base em custo (**Cost Based Optimizer - CBO**)
    - Seleciona plano com menor consumo de recurso (**"IO", CPU e memória**), dentre aqueles analisados
    - Utiliza estatísticas de banco de dados para análise
    - Análise pode ser diferente entre Edições do SQL Server
    - Nem sempre é possível optar pelo "melhor plano de execução"

### Fases de Execução de Consultas

**Parse** >>> **Resolve** >>> **Optimize** >>> **Compile** >>> **Execute**

1. **Parse (análise)** - Verifica a sintaxe, construindo uma representação em árvore de operadores chamada "_**Parse Tree**_"
1. **Resolve (resolver)** - Identifica se os objetos referenciados na consulta realmente existem. O produto final desta fase se chama "_**Algebrized Tree**_"
1. **Optimize (otimizar)** - Seleciona o plano de execução
1. **Compile (compilar)** - Compila o plano de execução selecionado pela fase anterior, depois armazena na memória no Plan Cache
1. **Execute (execução)** - Execução da consulta

### O que é Estatística de Banco de Dados?

- Mantém informações referente a distribuição de valores de uma chave
- SQL Server cria estatística automaticamente quando criamos índice
- Controlada por duas propriedades de banco de dados
    - Auto Create Statistics
    - Auto Update Statistics
- SQL Server monitora atualização na tabela e atualiza as estatísticas
- Atualização das estatísticas pode ser feita por Sampling ou Full Scan

### Conceitos Importantes em Estatística de BD

- Seletividade
    - Quantidade de linhas retornada por uma consulta
    - Quanto **maior** a quantidade de linhas, **menor** é a seletividade
    - Consultas de alta seletividade tem maior probabilidade de utilizar índices

- Densidade
    - Indicador que reflete a repetição de valores
    - Quanto mais valor repetido uma chave tem, maior é a densidade e menor a probabilidade de utilizar índices
    - Costuma ser inversamente proporcional Seletividade e Densidade:
        - **Maior** Densidade **Menor** Seletividade
        - **Menor** Densidade **Maior** Seletividade

### Hands On

Comandos **SET** = Configura a sessão, a conexão, não interferindo no padrão do Servidor, nem modifica as demais conexões.

**SET STATISTICS PROFILE ON** = Com este parâmetro habilitado, ao executar uma query, teremos dois Grids de resultado, sendo que o primeiro é o resultado da própria query, já o segundo é o Plano de Execução.

**SET STATISTICS TIME ON** = Habilitado, mostra o tempo que a query levou para processar, entretanto, não é necessáriamente um bom indicador, já que o tempo depende do momento, por exemplo se o plano de execução não estiver em Cache ele levará um tempo maior para processar, se na sequência eu executar a mesma query, como o plano estará em Cache, seu tempo de processamento será muito menor.

**SET STATISTICS "IO ON** = Habilita a visualização das estatísticas, depois de executar este comando, estando no SSMS, no menu Query, selecione "Include Actual Execution Plan", agora toda vez que eu executar uma query, além de ter o resultado da mesma, terei também resultado do Plano de Execução gráfico, permitindo uma leitura mais amigável.

O Plano de Execução é lido da direita pra esquerda e de cima para baixo e a espessura das linhas indica o volume de linhas que está trafegando entre cada fase e quando paramos o mouse sobre cada fase, um quadro com informações detalhadas se abre.

Com esta opção habilitada, também teremos como resultado uma outra informação inportante que é o Estatística de "IO" (Guia Messages).

Quando estiver realizando um processo de Tuning, habitue-se a colocar um comentário logo abaixo da query que está sendo analisada e copie para o mesmo, o resultado mostrado em "_**Messages**_", lembrando que deste resultado, o que nos interessa é o "_**logical reads**_" (que é a leitura em memória) e representa o total de "IO" da query, sendo que este total significa a quantidade total de páginas de 8k e para transformar em "IO" devo pegar o total de "IO" e multiplicar por 8, para obter o total de "IO" em Kbytes.

### Estratégias de Indexação