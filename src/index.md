Mochila Binária
======

O problema
------

Você está indo viajar de avião e tem uma lista de coisas que precisa levar nessa viagem. Você levará uma *mochila* e uma *mala* que será despachada. Por uma questão de segurança, você pretende colocar o máximo de coisas de valor (monetário) na *mochila* até enchê-la, e o que sobrar será colocado na *mala*. Sua *mochila* não tem um volume infinito (óbvio), então há uma quantidade máxima de objetos que podem ser colocados nela até que ela fique cheia. Outro ponto importante é que você só tem um de cada objeto da lista.

Dessa forma, para organizar sua mochila da melhor maneira possível, você vai precisar saber o tamanho e o valor de cada uma das coisas que você precisa levar nessa viagem.

??? Checkpoint

Dada a lista de objetos abaixo e que sua mochila possui **10 [unidades de volume] de capacidade**, que lógica você seguiria para colocar os objetos na mochila de forma que a soma de valores seja máxima?

| Nome     | Volume | Valor |
|----------|--------|-------|
| Headset  | 3      | 30    |
| Livro    | 4      | 20    |
| Relógio  | 2      | 30    |
| Notebook | 7      | 50    |
| Tablet   | 4      | 40    |

::: Gabarito
Uma lógica possível é escolher os objetos de maior valor possível até que a mochila fique cheia. Para essa lista, seriam escolhidos os item `md Notebook` seguido do `md relógio`, obtendo um peso total de *9* e um valor total de *90*. O que não necessariamente é a melhor escolha de objetos.
:::

???

Em termos mais formais, o problema da mochila binária é um problema de otimização combinatória em que se deseja preencher uma mochila com uma combinação de itens que possuem diferentes pesos e valores de forma que a soma que a soma de seus valores seja máxima e a soma de seus pesos não ultrapasse um certo limite (capacidade).  

??? Checkpoint

Para que um problema da mochila seja solucionável, é necessário conhecer certos dados sobre a mochila e os itens que podem preenchê-la.

Com base no exemplo dado anteriormente, quais seriam as **entradas** necessárias para solucionar um problema da mochila binária e possibilitar sua solução. Qual o formato da **saída** que é esperado?

::: Gabarito
Como *entradas* é necessário possuir:

- A capacidade da mochila;
- Lista dos pesos de cada item que se deseja por na mochila (necessariamente na mesma unidade de medida da capacidade da mochila);
- Lista dos valores de cada item que se deseja por na mochila ;

Como *saída* É esperado uma lista que informe para cada item se ele deve ser colocado (`md 1`) ou não na mochila (`md 0`).
:::

???

A `md capacidade da mochila` é a quantidade máxima de um certo fator limitante que é definido pelo problema. Para o exemplo da mochila literal dado acima, a capacidade era o volume interno máximo que a mochila possuía. Dependendo do problema, nesse mesmo exemplo a capacidade também poderia ser o peso máximo suportado pela mochila.

O `md peso do item` é a quantidade do fator limitante que cada item possui. Esse *"peso"* não necessariamente é literalmente peso ( $g\cdot m$ ), pode ser qualquer unidade de medida de um fator limitante, desde que seja igual a unidade de medida da *capacidade da mochila*. Para o exemplo acima, o *"peso"* do item era o seu volume.

O `md valor do item` é fator de interesse que cada item possui e que se deseja maximizar. No exemplo acima, o *"valor"* era o valor monetário daquele item, mas esse

O Algoritmo
------

Para a aproximar a notação usada nesse handout com a notação encontrada nos algoritimos utilizaremos a seguinte notação:

* **M**: capacidade da mochila
* **n**: número de objetos na lista
* **pesos**: lista que contem os pesos de cada objeto
* **valores**: lista que contemm os valores de cada objeto 

Agora vamos começar a estruturar o algoritmo. Se na solução do algoritmo um determinado objeto está na mochila é necessariamente verdade que:
1. A capacidade da mochila não é ultrapassada ao colocar aquele objeto
2. A mochila fica mais valiosa com aquele objeto dentro do que sem ele ( ou seja, se a mochila fosse preenchida até a capacidade máxima mas apenas com os outros objetos )

Para garantir o primeiro ponto, primeiro devemos ter o cuidado de adicionar o objeto em uma mochila que não esteja completamente cheia pois ao adicionarmos o objeto, o peso da mochila aumentará.

??? Checkpoint

Se a mochila tem capacidade 10 e o objeto tem peso 4, qual deve ser o peso máximo da mochila antes do objeto ser adicionado?

::: Gabarito
O peso da mochila antes do objeto ser adicionado deve ser a capacidade máxima menos o peso do objeto. Nesse caso $10-6 = 4$.
:::

???

Além disso, há mais uma coisa que devemos checar. Se o objeto tiver peso maior que a capacidade da mochila, ele não pode ser adicionado.

Garantir o segundo ponto é um pouco mais complicado, nesse caso a lógica do algoritmo consiste em ir aumentando progressivamente a quantidade de objetos que está sendo considerada e fazer a solução para cada quantidade. Na prática, essa progressão é feita utilizando o índice das listas de pesos e valores ( que, por isso, devem estar na mesma ordem ). Perceba que isso significa que cada índice estará associado a um objeto. Considerando novamente o caso base e, relacionando cada objeto com um índice teríamos:

| Nome     | Volume | Valor |Índice|
|----------|--------|-------|------|
| Headset  | 3      | 30    | 0    |
| Livro    | 4      | 20    | 1    |
| Relógio  | 2      | 30    | 2    |
| Notebook | 7      | 50    | 3    |
| Tablet   | 4      | 40    | 4    |

Dessa forma, se estivermos por exemplo no índice 2 da lista, estaremos fazendo a solução considerando os objetos: Headset, Livro e Relógio.

Solução Recursiva
------
<!--
A ideia dessa solução é implementar exatamente o que foi apresentado até agora. Para isso vamos usar os passos dados em aula para se montar funções recursivas, se você já esqueceu como ou quais são os passos, revise a [aula 2](https://ensino.hashi.pro.br/desprog/aula/2/).

??? Checkpoint

Faça um pseudo-código (basicamente escrever em linguagem humana), a representação das condições dadas do problema da mochila binária.

::: Gabarito

Passo 1: Entenda o que a função recebe e o que deveria fazer.
```python
recebe o peso total, os pesos dos itens, os valores dos itens e o ítem que quero analisar
    a função deve retornar quais itens devem ser colocados na mochila
```

Passo 2: Adicione uma chamada recursiva ao código da função. 
```python
função recebe o peso total, os pesos dos ítems, os valores dos ítems e o ítem que quero analisar
    vou rodar a função de novo
```

Passo 3: Passe para a chamada recursiva um parâmetro menor.
```python
função recebe o peso total, os pesos dos ítems, os valores dos ítems e o ítem que quero analisar
    vou receber o mesmo peso, mesmos itens, mesmos valores, mas vou analisar o próximo item

```

Passo 4: Não simularás e terás fé.

(o código é o mesmo)


Passo 5: Você tem fé na resposta da chamada recursiva, então use-a.

(essa é um pouco menos intuitiva)

```python
função recebe o peso total, os pesos dos ítems, os valores dos ítems e o ítem que quero analisar


    se o peso do último item ultrapassar o valor máximo:
        vou receber o mesmo peso, mesmos itens, mesmos valores, mas vou analisar o próximo item

    caso o contrário:
        calcule o valor da mochila se eu considerar o item que estou analisando agora
        calcule o valor da mochila se eu NÃO considerar o item que estou analisando agora
        retorne o maior valor entre eles
```

Passo 6: Isole o caso em que o parâmetro é o menor possível.

```python
função recebe o peso total, os pesos dos ítems, os valores dos ítems e o ítem que quero analisar

    se o peso máximo for atingido ou todos os itens foram analisados:

    se o peso do último item ultrapassar o valor máximo:
        vou receber o mesmo peso, mesmos itens, mesmos valores, mas vou analisar o próximo item

    caso o contrário:
        calcule o valor da mochila se eu considerar o item que estou analisando agora
        calcule o valor da mochila se eu NÃO considerar o item que estou analisando agora
        retorne o maior valor entre eles
```

Passo 7: A solução desse caso é trivial, então calcule ela direto.

```python
função recebe o peso total, os pesos dos ítems, os valores dos ítems e o ítem que quero analisar

    se o peso máximo for atingido ou todos os itens foram analisados:
        não vou colocar o item que estou analisando agora na mochila

    se o peso do último item ultrapassar o valor máximo:
        vou receber o mesmo peso, mesmos itens, mesmos valores, mas vou analisar o próximo item

    caso o contrário:
        calcule o valor da mochila se eu considerar o item que estou analisando agora
        calcule o valor da mochila se eu NÃO considerar o item que estou analisando agora
        retorne o maior valor entre eles
```

:::

???

Sim, é complicado, mas infelizmente vai ficar mais ainda...

??? Checkpoint

Faça um código em c para cada um dos passos dados no checkpoint anterior.

::: Gabarito

Passo 1: entenda o que a função recebe e o que deveria fazer.
```c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]);
```

Passo 2: adicione uma chamada recursiva ao código da função. 
```c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]){
    res_mochila_bin(???);
};
```

Passo 3: passe para a chamada recursiva um parâmetro menor.
```c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]) {
    res_mochila_bin(capacidade, n-1, pesos, valores);
};
```


Passo 4: não simularás e terás fé.

(o código é o mesmo)


Passo 5: você tem fé na resposta da chamada recursiva, então use-a.

(essa é um pouco menos intuitiva)

```c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]){
    if (pesos[n - 1] > W){
        return res_mochila_bin(capacidade, n-1, pesos, valores);
    }

    else{
        return max(
            valores[n - 1] + res_mochila_bin(capacidade - pesos[n-1], n-1, pesos, valores),
            res_mochila_bin(capacidade, n-1, pesos, valores)
        );
    }
}
```

Passo 6: Isole o caso em que o parâmetro é o menor possível.

```c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]){
    if (n == 0 || capacidade == 0){

    }
    if (pesos[n - 1] > W){
        return res_mochila_bin(capacidade, n-1, pesos, valores);
    }

    else{
        return max(
            valores[n - 1] + res_mochila_bin(capacidade - pesos[n-1], n-1, pesos, valores),
            res_mochila_bin(capacidade, n-1, pesos, valores)
        );
    }
}
```

Passo 7: a solução desse caso é trivial, então calcule ela direto.

```c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]){
    if (n == 0 || capacidade == 0){
        return 0;
    }
    if (pesos[n - 1] > W){
        return res_mochila_bin(capacidade, n-1, pesos, valores);
    }

    else{
        return max(
            valores[n - 1] + res_mochila_bin(capacidade - pesos[n-1], n-1, pesos, valores),
            res_mochila_bin(capacidade, n-1, pesos, valores)
        );
    }
```

:::

??? -->

Solução Dinâmica
------

A solução dinâmica utiliza a mesma premissa da solução descursiva: para resolver uma mochila é necessesário comparar duas mochilas menores. Dessa forma, a metodologia dessa solução é, em vez de calcular cada mochila repetidas vezes quando preciso dela, calcular todas as mochilas menores até chegar na mochila desejada?

Ao longo desse processo o valor máximo obtidos para cada mochila será armazenado em uma matriz em que as linhas são os índices que poderão ser considerados na análise (começando por nenhum e terminando por $n-1$), e as colunas são as capacidades (começando da capacidade $0$ e terminando com $M$), como a imagem a seguir:

![Matriz Vazia](matriz_vazia.png)

Assim, quando for necessário comparar duas mochilas menores para calcular uma maior essas mochilas menores já estarão prontas, basta acessa-las na matriz.

Agora, vamos entender a lógica de preenchimento dessa matriz.

??? Checkpoint

O caso base é quando a mochila tem capacidade nula ou não há objetos a serem analizados. Nesse caso, como devemos preencher a matriz?

::: Gabarito
Nenhum objeto terá peso menor ou igual a 0, portanto, então a primeira coluna deve ser preenchida com 0. A primeira linha também deve ser preenchida com 0, já que, se não há objetos na lista, não há como preencher a mochila, ou seja, ela estará sempre vazia. Assim, obtemos a seguinte matriz:

![Matriz Base](matriz_base.png)

:::

???

:preenchimento_completo

<!-- ??? Checkpoint

Preencha o código do caso inicial de acordo com a conclusão anterior.

::: Gabarito

``` c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]) {
    int M_binaria[capacidade][n];
    int M_valores[capacidade][n];
    for (int j = 0; j =< capacidade; j++) {
        for (int i = 0; i < n; i ++) {
            if (j == 0) {
                M_valores[i][j] = 0;
            }
            // condições de escolha
            // preencher matriz binária
        }
    }
    // leitura do resultado
}
```
:::

??? -->

<!-- Agora iremos preencher a matriz verificando se cada objeto deve ser adicionado ou não. Lembre-se que o que queremos é maximizar o valor da mochila final. Tente preencher as condições de escolha baseado nisso e nas explicações anteriores.

??? Checkpoint

Preencha o código referente a condição de escolha de acordo com a conclusão anterior.

::: Gabarito

``` c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]) {
    int M_binaria[capacidade][n];
    int M_valores[capacidade][n];
    for (int j = 0; j =< capacidade; j++) {
        for (int i = 0; i < n; i ++) {
            if (j == 0) {
                M_valores[i][j] = 0;
            }
            if (pesos[i] > capacidade || M_valores[i-1][j] > (M_valores[i-1][j-pesos[i]] + valores[i])) {
                M_valores[i][j] = M_valores[i-1][j]
            } else {
                M_valores[i][j] = M_valores[i-1][j-pesos[i]] + valores[i]
            }
            // preencher matriz binária
        }
    }
    // leitura do resultado
}
```
:::

??? -->

<!-- Finalmente, vamos preencher a matriz que mais interessa, a matriz binária. Para isso, basta colocar 1 se o objeto foi escolhido e 0 se ele não foi.

``` c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]) {
    int M_binaria[capacidade][n];
    int M_valores[capacidade][M];
    for (int j = 0; j =< capacidade; j++) {
        for (int i = 0; i < n; i ++) {
            if (j == 0) {
                M_valores[i][j] = 0;
            }
            if (pesos[i] > capacidade || M_valores[i-1][j] > (M_valores[i-1][j-pesos[i]] + valores[i])) {
                M_valores[i][j] = M_valores[i-1][j]
                M_binaria[i][j] = 0;
            } else {
                M_valores[i][j] = M_valores[i-1][j-pesos[i]] + valores[i]
                M_binaria[i][j] = 1;
            }
        }
    }
    // leitura do resultado
}
```

Chegamos no último passo, agora só precisamos exibir o resultado. Essa parte não é tão intuitiva quanto parece, para ver a solução da capacidade 10, não podemos apenas ler a coluna de número 10 da matriz binária. Quando decidimos se um objeto vai entrar ou não na mochila, não usamos a mochila de capacidade 10 com outros objetos e sim uma mochila de capacidade menor e, consequentemente, os objetos escolhidos para montar aquela mochila menor. Por isso, devemos ler o resultado considerando as mochilas de capacidades menores que foram montadas.

Começaremos lendo a partir do último índice, decidimos se íamos ou não colocar ele na mochila montando a solução da capacidade máxima, portanto ele é lido na coluna da capacidade máxima. Se o último objeto foi escolhido na mochila de capacidade máxima, o penúltimo foi escolhido para uma mochila de capacidade máxima menos o peso do último objeto (pois partimos dessa mochila para montar a solução da mochila de capacidade máxima). Portanto o penúltimo objeto será lido na coluna da capacidade máxima menos o peso do último objeto. Fazendo isso até que termos lido todos os objetos teremos: 

``` c
int res_mochila_bin(int capacidade, int n, int pesos[], int valores[]) {
    int M_binaria[capacidade][n];
    int M_valores[capacidade][M];
    for (int j = 0; j =< capacidade; j++) {
        for (int i = 0; i < n; i ++) {
            if (j == 0) {
                M_valores[i][j] = 0;
            }
            if (pesos[i] > capacidade || M_valores[i-1][j] > (M_valores[i-1][j-pesos[i]] + valores[i])) {
                M_valores[i][j] = M_valores[i-1][j]
                M_binaria[i][j] = 0;
            } else {
                M_valores[i][j] = M_valores[i-1][j-pesos[i]] + valores[i]
                M_binaria[i][j] = 1;
            }
        }
    }
    
    coluna = capacidade;
    for (int obj = n-1; obj >= 0; obj--) {
        int val = matriz_binária[obj][capacidade];
        printf("%d", val);

        if (val == 1) {
            capacidade -= pesos[obj];
        }
    }
    return M_valores[capacidade][n];
}
``` -->
Com isso falta apenas mais um tópico.

Complexidade da Solução Dinâmica
------

Teremos a solução do algoritmo definida, quando preenchermos as matrizes, o que é feito em um looping de M (valor da capacidade máxima) com um looping interno de n (número de objetos). O que resulta em uma complexidade de  $O(n\cdot M)$.

Comparação
------

Comparando a complexidade dos dois algoritmos apresentados para resolver o problema da mochila binária, é possível observar que o algoritmo de programação dinâmica possui uma complexidade melhor que o algoritmo recursivo, $O(n\cdot M)$ e $O(2^n)$. Isso acontece porque o algoritmo dinâmico apenas preencher sua matriz, enquanto o algoritmo recursivo precisa constantemente comparar duas mochilas que não estão calculadas (e para calculá-las, comparar mochilas menores que também não estão calculadas), tornando sua complexidade exponencial.
