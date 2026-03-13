# Explorações de otimizações e hierarquia de memória (cache) e pthreads

## Você sabe multiplicar uma matriz?

Construa um código em C/C++ que faça a multiplicação entre matrizes. Primeiramente, garanta que seu algoritmo funcione (por exemplo, utilizando matrizes de 3x3) e, em seguida, explore seu algoritmo em matrizes maiores (por exemplo, 1000x1000).

Por isso, os valores de sua matrizes são determinados (por exemplo, números sequenciais) para garantir que seu algoritmo funcione corretamente. Exemplo de como inicializar as matrizes:

## Item 1. Inicialize as matrizes A e B com:
```
      a[i][j]= i + j;

       b[i][j] = i * j; 
```
ou então utilize números aleatórios entre 10 e 40 para ambas as matrizes.

## Item 2. Depois de inicializadas as matrizes, faça um loop que percorra toda a matriz A e verifique quantos elementos são pares, imprimindo o número de pares ao final.  
   *veja exemplo no código fornecido*.

Você deve implementar o item 2 de duas formas: percorrendo a matriz em ordem de coluna e percorrendo em ordem de linha. Abaixo, 2 trechos de códigos de exemplos:

**Ordem de Coluna**

```
Matriz a[i][j]
for (j = 0; j < N; j++)
   for (i = 0; i < N; i++) {
     /* faz as operações com os elementos da matriz a */
      a[i][j]
```

**Ordem de Linha**

```
Matriz a[i][j]
for (i = 0; i < N; i++)
   for (j = 0; j < N; j++) {
     /* faz as operações com os elementos da matriz a */
      a[i][j]
   }
```
Para cada opção, você deve medir o tempo de execução, seguindo o exemplo no código fornecido.

**Crie dois executáveis para este item 2: um que percorre em ordem de linha e outro que percorre em ordem de coluna.**

## Item 3. Faça agora a multiplicação de matrizes considerando:  
   A * B = C, onde:
    1. Dimensões de A: M x N
    2. Dimensões de B: N x X (qq dimensão razoável, mas não um vetor)
    3. Consequentemente, dimensões de C: M x X

 se quiser, pode utilizar matrizes quadradas.

Para o item 3, implemente seu algoritmo de duas formas (**crie dois executáveis, um para cada algoritmo**):

 1. Percorrendo as matrizes em ordem de linha
 2. Para utilizar corretamente o cache (L1, L2) utilizando a abordagem de **blocagem** apresentada no [artigo da Intel](https://www.intel.com/content/www/us/en/developer/articles/technical/putting-your-data-and-code-in-order-optimization-and-memory-part-1.html).


Você deve fazer a análise da velocidade do seu algoritmo compilando o código de duas formas:

* utilize o parâmetro do compilador que desliga as opções de otimização: -O0
* utilize o parâmetro do compilador que liga o máximo de otimizações: -O3

Para cada opção, você deve medir o tempo de execução, seguindo o exemplo no código fornecido.

Você deve fazer essa medição utilizando o utilitário `valgrind`, para analisar também o padrão de acesso ao cache de todas as versões do código que implementou. Veja abaixo um exemplo de saída que o **`valgrind`** dá:

```
$ valgrind --tool=cachegrind ./matmulcol
==23104== Cachegrind, a cache and branch-prediction profiler
==23104== Copyright (C) 2002-2015, and GNU GPL'd, by Nicholas Nethercote et al.
==23104== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info
==23104== Command: ./matmulcol
==23104== 
--23104-- warning: L3 cache found, using its data for the LL simulation.
Column order; tempo para multiplicar coluna: 0.085526 secs 
==23104== 
==23104== I   refs:      17,994,092
==23104== I1  misses:         1,051
==23104== LLi misses:         1,040
==23104== I1  miss rate:       0.01%
==23104== LLi miss rate:       0.01%
==23104== 
==23104== D   refs:      12,641,331  (7,385,328 rd   + 5,256,003 wr)
==23104== D1  misses:     1,117,327  (    2,580 rd   + 1,114,747 wr)
==23104== LLd misses:       134,009  (    2,378 rd   +   131,631 wr)
==23104== D1  miss rate:        8.8% (      0.0%     +      21.2%  )
==23104== LLd miss rate:        1.1% (      0.0%     +       2.5%  )
==23104== 
==23104== LL refs:        1,118,378  (    3,631 rd   + 1,114,747 wr)
==23104== LL misses:        135,049  (    3,418 rd   +   131,631 wr)
==23104== LL miss rate:         0.4% (      0.0%     +       2.5%  )


$ valgrind --tool=cachegrind ./matmulrow
==23161== Cachegrind, a cache and branch-prediction profiler
==23161== Copyright (C) 2002-2015, and GNU GPL'd, by Nicholas Nethercote et al.
==23161== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info
==23161== Command: ./matmulrow
==23161== 
--23161-- warning: L3 cache found, using its data for the LL simulation.
Row order; tempo para multiplicar: 0.053935 secs 
==23161== 
==23161== I   refs:      17,993,582
==23161== I1  misses:         1,049
==23161== LLi misses:         1,038
==23161== I1  miss rate:       0.01%
==23161== LLi miss rate:       0.01%
==23161== 
==23161== D   refs:      12,641,128  (7,385,188 rd   + 5,255,940 wr)
==23161== D1  misses:       134,287  (    2,579 rd   +   131,708 wr)
==23161== LLd misses:       134,009  (    2,378 rd   +   131,631 wr)
==23161== D1  miss rate:        1.1% (      0.0%     +       2.5%  )
==23161== LLd miss rate:        1.1% (      0.0%     +       2.5%  )
==23161== 
==23161== LL refs:          135,336  (    3,628 rd   +   131,708 wr)
==23161== LL misses:        135,047  (    3,416 rd   +   131,631 wr)
==23161== LL miss rate:         0.4% (      0.0%     +       2.5%  )
```

## Item 4: Adicionando Paralelismo com Pthreads

Até esta etapa, focamos em como a organização do acesso à hierarquia de memória (técnica de blocagem) afeta o desempenho da multiplicação de matrizes de forma sequencial. Agora, vamos dar um passo além e explorar o **paralelismo no nível de thread**.

Como vocês já construíram a base de criação e sincronização no **Laboratório de Pthreads**, o objetivo neste desafio é aplicar esse conhecimento para dividir o esforço computacional da multiplicação entre múltiplas threads, observando o comportamento da aplicação.

### O que deve ser feito:

1. **Implementação Paralela**: Crie uma nova versão do código (ex: `matmul_pthreads.c`) que divida o cálculo da matriz resultante $C$ entre $T$ threads. 
   * *Sugestão de divisão:* Uma abordagem clássica e eficiente é dividir as linhas da matriz $A$ igualmente entre as threads disponíveis.
2. **Combinação de Técnicas (Avançado)**: Integre a técnica de blocagem (*tiling*) desenvolvida na etapa anterior com a sua nova versão paralela em Pthreads. Cada thread deverá ser responsável por processar um subconjunto de blocos.
3. **Bateria de Testes**: Execute seu programa variando o número de threads (ex: 1, 2, 4, 8, 16, 20, 32). 
   * *Nota de Ambiente:* Lembrem-se de considerar a arquitetura de hardware onde estão executando os testes. Por exemplo, se utilizarem nosso servidor Dual Xeon (total de 20 cores físicos e 128GB de RAM), testem como o programa se comporta ao ultrapassar a barreira de 20 threads.
4. **Coleta de Métricas**: Registre os tempos de execução da versão paralela (com e sem blocagem) e adicione-os aos seus gráficos e tabelas de análise.

### Questões para Reflexão e Relatório:

Ao documentar seus resultados, abordem os seguintes pontos:

* **Speedup e Escalabilidade:** Como o tempo de execução se comporta ao aumentar o número de threads? O ganho de desempenho (*speedup*) é linear? Em que ponto adicionar mais threads deixa de trazer benefícios ou passa a degradar o tempo de execução?
* **Overhead de Threads:** Existe um custo inerente ao sistema operacional para criar, gerenciar e destruir threads. Como esse *overhead* afeta o desempenho ao multiplicar matrizes pequenas em comparação com matrizes muito grandes?
* **Pthreads + Blocagem:** Se você implementou a versão combinada, os ganhos de desempenho se somaram? A otimização de uso da memória cache potencializou o trabalho paralelo das threads?
* **Falso Compartilhamento (*False Sharing*):** Dependendo de como você dividiu a gravação dos dados na matriz $C$, sua aplicação pode sofrer com a invalidação de linhas de cache entre os núcleos do processador. Vocês notaram algum indício disso?

> **Dica de Implementação:** Tenha atenção redobrada com as condições de corrida (*race conditions*). Se a divisão de trabalho for feita de forma que cada thread calcule regiões estritamente independentes da matriz de destino $C$, o uso de *mutexes* no laço principal de cálculo se torna desnecessário, garantindo um desempenho consideravelmente superior.

## Entregas

1. Você deve entregar um código fonte para cada versão do algoritmo implementado.
2. Você deve entregar um relatório em PDF com as evidências de todas as **compilações** e **execuções** dos códigos.
3. Você deve fazer uma análise da saída do **`valgrind`**  para o **item 3** e para o **item 4** discutindo os resultados.
