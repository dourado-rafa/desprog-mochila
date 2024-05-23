Mochila Binária
======

O problema
------

O problema da mochila binária é um problema de otimização combinatória em que se deseja preencher uma mochila com uma combinação de itens que possuem diferentes pesos e valores de forma que a soma de seus valores seja máxima e a soma de seus pesos não ultrapasse um certo limite (capacidade). 

Pensando de uma maneira menos técnica, imagine que você está indo viajar de avião e tem uma lista de coisas que precisa levar nessa viagem. Você levará uma *mochila* e uma *mala* que será despachada. Por uma questão de segurança, você pretende colocar o máximo de coisas de valor (monetário) na *mochila* até enchê-la, e o que sobrar será colocado na *mala*. Sua *mochila* não tem um volume infinito (óbvio), então há uma quantidade máxima de objetos que podem ser colocados nela até que ela fique cheia. Outro ponto importante é que você só tem um de cada objeto da lista.

Dessa forma, para organizar sua mochila da melhor maneira possível, você vai precisar saber o tamanho e o valor de cada uma das coisas que você precisa levar nessa viagem.

Antes de pensar em código propriamente, é importante fazer pelo menos um exemplo "na mão". Vamos pegar um caso mais fácil agora no começo. Então, vamos considerar que sua mochila possui **10 [unidades de volume] de capacidade** e a pequena lista de itens abaixo:

![Lista de Objetos Pequena e Mochila](lista_pequena_com_mochila.png)

Dada a lista de objetos, uma possível solução (e provavelmente a mais instintiva) seria pegar os objetos de maior valor primeiro, e complementar até bater o peso máximo. (ja adiantando que essa nem sempre é a solução mais viável).

??? Checkpoint

Monte uma solução baseada na lógica anterior e veja o resultado. Esta é a melhor solução? Por que?

::: Gabarito
Para essa lista, seriam escolhidos os item `md Travesseiro` seguido do `md Carregador`, obtendo um peso total de *10* e um valor total de *80*. 

![Lista Pequena com Solução Errada](lista_pequena_solucao_errada.png)

O que não é a melhor escolha de objetos e a escolha do `md Travesseiro` nos impede de sequer considerarmos outras soluções.
:::

???

??? Checkpoint

Tente então montar a solução com o maior valor possível para a mochila em questão.

::: Gabarito
A melhor solução para essa lista seria escolher os item `md Controle`, `md Carregador` e `md Garrafa` obtendo um peso total de $9$ e um valor total de $100$. 

![Lista Pequena com Solução Certa](lista_pequena_solucao_certa.png)
:::

???

Note que, esta solução nem sequer utiliza o `md Travesseiro`, e ainda sim obtivemos um valor maior que o anterior. Além disso, você deve ter percebido que, por mais que o peso da mochila não tenha sido atingido, conseguiu-se extrair o maior valor possíveis dos itens da lista. Isso ocorre porque **a melhor solução nem sempre é a que preenche a mochila completamente**

Em termos de lógica de resolução, existem algumas maneiras diferentes de se encontrar a melhor mochila possível. Uma delas é por um método de tentativa e erro (que muito provavelmente foi a que você usou no Checkpoint anterior), o que não é ruim para casos pequenos como aquele, mas definitivamente não é a mais rápido, principalmente quando considerarmos problemas em que a lista de ítens é extremamente extensa.

Entao vamos começar a pensar em uma estrutura um pouco mais voltada à código de uma função que resolva o problema. E para que essa função possa ser montada, precisamos conhecer quais são suas entradas e saídas.

??? Checkpoint

Com base no exemplo dado anteriormente, quais seriam as **entradas** necessárias para possibilitar a solução de um problema da mochila binária? Qual o formato da **saída** que é esperado?

::: Gabarito
Como *entradas* é necessário possuir:

- A capacidade da mochila;
- Lista dos pesos de cada item que se deseja por na mochila (necessariamente na mesma unidade de medida da capacidade da mochila);
- Lista dos valores de cada item que se deseja por na mochila ;

Como *saída* É esperado uma lista que informe para cada item se ele deve ser colocado (`md 1`) ou não na mochila (`md 0`).
:::

???

Pensando em termos mais técnicos:

A `md capacidade da mochila` é a quantidade máxima de um certo fator limitante que é definido pelo problema. Para o exemplo da mochila literal dado acima, a capacidade era o volume interno máximo que a mochila possuía. Dependendo do problema, nesse mesmo exemplo a capacidade também poderia ser o peso máximo suportado pela mochila.

O `md peso do item` é a quantidade do fator limitante que cada item possui. Esse *"peso"* não necessariamente é literalmente peso ( $g\cdot m$ ), pode ser qualquer unidade de medida de um fator limitante, desde que seja igual a unidade de medida da *capacidade da mochila*. Para o exemplo acima, o *"peso"* do item era o seu volume.

O `md valor do item` é fator de interesse que cada item possui e que se deseja maximizar. No exemplo acima, o *"valor"* era o valor monetário daquele item, mas esse *"valor"* pode ser de qualquer unidade de interesse.

O Algoritmo
------

A partir daqui, iremos mudar a lista de objetos para uma lista um pouco maior:

![Lista de Objetos com Mochila](lista_objetos_com_mochila.png)

Outra coisa que precisaremos fazer é simplificar um pouco a saída da função: **o algoritmo retornará o valor máximo encontrado para a mochila**. 
No caso do exemplo que você resolveu, o algoritmo retornaria *100*. Sei que acabei de dizer que a saída da função é uma lista de `md 1` e `md 0`, mas isso facilitará a compreeensão da solução e, após isso, modificar para encontrarmos essa lista não seria tão complicado.

??? Checkpoint
Agora que sabemos as entradas necessárias e a saída que queremos, monte o cabeçalho da função. 

Lembre-se que para trabalhar com listas em *C* é necessário saber o tamanho dessas listas.

::: Gabarito
```c
int mochila_binaria(int M, int n, int pesos[], int valores[]);
```
Sendo:

* **M**: capacidade da mochila
* **n**: número de objetos na lista
* **pesos**: lista que contem os pesos de cada objeto
* **valores**: lista que contém os valores de cada objeto 
:::

???

A ordem da lista de pesos e valores deve ser a mesma, ou seja, se o peso do Headset está na primeira posição da lista de pesos, o valor dele também deve estar na primeira posição da lista de valores. Perceba que isso significa que cada índice estará associado a um objeto, o primeiro objeto terá índice 0, o segundo índice 1, [...], por fim, o último objeto terá índice n-1 pois a lista tem tamanho n. Tenha isso em mente, pois vai ser muito importante mais pra frente.

Ordenando a lista do nosso problema teríamos:

![Lista com índices](lista_objetos_indices.png)

Agora vamos começar a estruturar o algoritmo. Se na solução do algoritmo um determinado objeto está na mochila é necessariamente verdade que:
1. A capacidade da mochila não é ultrapassada ao colocar aquele objeto
2. A mochila fica mais valiosa com aquele objeto dentro do que sem ele ( ou seja, se a mochila fosse preenchida até a capacidade máxima mas apenas com os outros objetos )

Para garantir o primeiro ponto, primeiramente não podemos adicionar um objeto que tenha capacidade maior que a capacidade máxima da mochila. Com isso, já sabemos que o algoritmo só irá conferir as outras condições se o objeto puder ser adicionado.

```python
se peso do objeto menor ou igual a M, então:
    verifica se o objeto entra na mochila M
se nao:
    retorna o valor da mochila M sem o objeto
```

Além disso, devemos ter o cuidado de adicionar o objeto em uma mochila que tenha peso total menor que a capacidade máxima, pois ao adicionarmos o objeto, o peso da mochila aumentará.

??? Checkpoint

Partindo do problema inicial, se quisermos adicionar o `md tablet` (peso 4) na mochila (capacidade 10), qual deve ser o peso máximo ocupado da mochila antes de colocarmos o `md tablet`?

::: Gabarito
O peso máximo da mochila antes do `md tablet` ser adicionado deve ser a capacidade máxima menos o peso do objeto. Nesse caso $10-4 = 6$. Caso fosse maior que isso, a capacidade da mochila seria ultrapassada ao colocarmos o `md tablet`.
:::

???

Generalizando para qualquer objeto, se a mochila tem capacidade $M$ o objeto só pode ser adicionado em uma mochila de capacidade $M -$ peso do objeto.

Garantimos que a capacidade da mochila não será ultrapassada ao adicionar o objeto, entretanto ainda devemos saber se aquele objeto deve ser adicionado. Para isso, devemos pensar no segundo ponto. Para saber se a mochila fica mais valiosa com o objeto ou sem ele, precisamos comparar o valor de uma mochila que tem o objeto com o valor de uma mochila que não tem o objeto.

```python
se peso do objeto menor ou igual a M, então:
    se valor da mochila M com objeto > valor da mochila M sem objeto:
        retorna o valor da mochila M com o objeto
    se não:
        retorna o valor da mochila M sem o objeto
se nao:
    retorna o valor da mochila sem o objeto
```
Aproveitando o checkpoint anterior, vamos analisar se o tablet deve entrar na mochila. Para fazer a comparação precisamos do peso das duas mochilas (com o `md tablet` e sem o `md tablet`). 

Como foi mostrado no checkpoint anterior, para montar uma mochila com o `md tablet`, adicionamos ele em uma mochila mais "vazia". Ao adicionar o `md tablet`, o valor da mochila também aumentará. Porém, para saber o valor total da mochila devemos saber também o valor da mochila mais "vazia" utilizada.

??? Checkpoint

Pensando em maximizar o valor da mochila, podemos adicionar o `md tablet` em qualquer mochila de peso máximo $6$?

::: Gabarito
Não, para a mochila ter valor máximo no final o `md tablet` deve ser adicionado em uma mochila de peso máximo $6$ que tenha o **valor máximo para essa capacidade**. 

Se isso ainda não ficou claro, pode ajudar pensar que após analisar o último objeto, teremos obtido a mochila final, que deve ter valor máximo. Se não adicionarmos o objeto em uma mochila com o maior valor possível, não teremos o maior valor possível na mochila final.
:::

???

Sabendo então que a mochila mais "vazia" deverá, também, ter o maior valor possível, podemos achar esse valor através do mesmo algoritmo. Daqui em diante chamaremos a mochila de menor capacidade de mochila $M_m$.

??? Checkpoint

Agora que já sabemos como encontrar o valor da mochila mais "vazia" ( suponha que esse valor seja $V_m$ ) qual será o valor da mochila após adicionar o tablet (peso 4 e valor 40)?

::: Gabarito
Ao adicionar algum objeto na mochila, o novo valor dela será o valor antigo mais o valor do objeto adicionado. Logo, nesse caso o novo valor seria $V_m + 40$.
:::

???

```python
se peso do objeto menor ou igual a M, então:
    se valor da mochila M_m + valor do objeto > valor da mochila M sem o objeto:
        retorna valor da mochila M_m + valor do objeto
    senão:
        retorna valor da mochila M sem o objeto
se nao:
    retorna o valor da mochila sem o objeto
```
Agora só precisamos descobrir como achar o valor de uma mochila sem um dos objetos. Perceba que, além do valor da mochila $M$ sem o objeto, o valor que pegaremos da mochila $M_m$ também é sem o objeto. Afinal, no problema da mochila binária só temos uma unidade de cada objeto e se esse objeto ainda vai ser adicionado na mochila, ele não pode estar na mochila $M_m$. Para terminar o pseudo-código, tem mais uma coisa que devemos reparar.

Como você já deve estar percebendo, o algoritmo utiliza de soluções de sub-problemas (mochilas de menores capacidade ou com menor quantidade de objetos) para resolver o problema final. Dessa forma, já podemos revelar uma das formas de resolver o problema da mochila binária: recursiva. Isso vai ajudar muito a construir o resto do código.

Caso você não lembre, em um algoritmo recursivo, resolvemos apenas o ultimo passo do problema assumindo que já temos a resposta dos passos anteriores. Nesse caso, isso equivale a decidir apenas se o último objeto vai entrar na mochila, assumindo que os outros objetos já foram analisados. 

??? Checkpoint

Agora vamos retomar algo que foi falado lá em cima: cada objeto está associado a um índice, se queremos a mochila mais valiosa sem o último objeto da lista, o que precisamos mudar nas entradas da função recursiva?

::: Gabarito
Para desconsiderar o objeto, é só não considerar o índice relacionado a ele, ou seja, não utilizar o último índice da lista de pesos e o último índice da lista de valores. Para isso é só passar para o algoritmo o tamanho $n-1$ ao invés de $n$.
:::

???

Agora que já sabemos que é uma função recursiva e que estamos resolvendo o problema apenas do último objeto, podemos utilizar o cabeçalho da função e acessar o valor do objeto na lista de valores e o peso do objeto na lista de pesos utilizando o índice correto:

```python
se peso[n-1] menor ou igual a M, então:
    se mochila_binaria(M - pesos[n-1], n-1, pesos, valores) + valores[n-1] > mochila_binaria(M, n-1, pesos, valores):
        retorna mochila_binaria(M - pesos[n-1], n-1, pesos, valores) + valores[n-1]
    se não:
        retorna mochila_binaria(M, n-1, pesos, valores)
se nao:
    retorna mochila_binaria(M, n-1, pesos, valores)
```

Por fim, como é uma função recursiva, iremos precisar de uma caso base.

??? Checkpoint

Vamos pensar na menor mochila possível, uma com capacidade $0$. Qual seria o valor maximo dessa mochila? E se a lista de objetos possíveis estiver vazia, qual o valor máximo da mochila?

::: Gabarito
Nos dois casos, $n = 0$ e $M = 0$, o valor máximo da mochila será $0$. 

Se a capacidade da mochila for $0$, qualquer objeto terá peso maior que a capacidade e não poderá ser adicionado. E se a lista de objetos for vazia, não temos nenhum objeto para adicionar na mochila.
:::

???

```python
se n igual a 0 ou M igual a 0, então:
    retorna 0

se não e peso[objeto] menor ou igual a M, então:
    se mochila_binaria(M - pesos[n-1], n-1, pesos, valores) + valores[n-1] > mochila_binaria(M, n-1, pesos, valores):
        retorna mochila_binaria(M - pesos[n-1], n-1, pesos, valores) + valores[n-1]
    se não:
        retorna mochila_binaria(M, n-1, pesos, valores)
se nao:
    retorna mochila_binaria(M, n-1, pesos, valores)
```

??? Checkpoint

Apenas com o pseudo-código, tente determinar a complexidade da solução recursiva;

::: Gabarito
Para chegarmos na solução, teremos sempre que comparar o valor da mochila com e sem o ítem em questão, e isso é feito para todos os itens da mochila, logo a complexidade dessa função é $O(2^n)$
:::

???

Agora sim, a solução recursiva está finalizada! Mas será que ela é a melhor?

Solução Dinâmica
------

Vimos como resolver o problema da mochila binária de forma recursiva, entretanto, essa forma tem uma complexidade muito alta devido a necessidade de ficar recalculando o valor de uma mesma mochila em diferentes momentos.  

Para melhorar essa complexidade podemos utilizar o fato da mochila binária ser resolvida por meio da solução de subproblemas menores. Esse fato nos permite utilizar uma solução dinâmica para minizar os cálculos dos subproblemas.

Em vez de calcular várias vezes o valor das mochilas menores ou com menos objetos, podemos preencher uma matriz com esses valores e apenas acessar quando necessário. Essa matriz terá os valores de todos os subproblemas, ou seja o valor máximo da mochila de capacidades $0$ até $M$ e considerando de $0$ a $n$ objetos. 

??? Checkpoint

Sabendo que as linhas da matriz serão quantos objetos estão sendo considerados para a solução e as colunas serão a capacidade da mochila que está sendo solucionada, de que tamanho será essa matriz? 

::: Gabarito
A matriz será $(M+1)\cdot(n+1)$

Considerando nosso exemplo de uma mochila de capacidade $10$ e uma lista de $5$ objetos, possuíriamos o seguinte esqueleto da matriz:

![Matriz Vazia](matriz_vazia.png)
:::

???

Assim, quando for necessário comparar duas mochilas menores para calcular uma maior essas mochilas menores já estarão prontas, basta acessá-las na matriz.

Agora, vamos entender a lógica de preenchimento dessa matriz. A lógica de escolha de cada mochila é bem similar a do algoritmo recursivo, mas a estrutura do algoritmo em si é um pouco diferente. 

Como vamos preencher a matriz com mochilas menores até chegar na desejada considerando cada vez mais objetos na lista, podemos fazer esse preenchimento por meio de dois loops aninhados (um dentro do outro).

Como apenas precisamos garantir que, quando formos calcular uma mochila, todas as mochilas de valor máximo menores que utilizam uma sublista menor já foram calculadas, a ordem desses loops não importam muito, contanto que ambos sejam em ordem crescente. Para facilitar o entendimento iremos preencher cada mochila de capacidade menor ou igual a $M$ para cada sublista de objetos que sejam menor ou igual a $n$, dessa forma:

``` python
para cada i menor ou igual a n, faça:
    para cada m menor ou igual a M, faça:
        // codigo de preenchimento da matriz[i][m] com a melhor mochila
```

Agora a matriz será preenchida na ordem correta, mas para que as comparações possam ser feitas ainda precisamos de um caso base.

??? Checkpoint

Podemos usar o mesmo caso base da função recursiva?

::: Gabarito
Sim. O caso base vem da lógica de que uma mochila com 0 de capacidade não suporta nenhum objeto, então tem valor 0. Assim como, se não temos nenhum objeto possível para colocar na mochila, ela terá valor 0 pois não terá nenhum objeto dentro. E isso continua sendo verdade na solução dinâmica. 

```python
para cada i menor ou igual a n, faça:
    para cada m menor ou igual a M, faça:
        se i igual a 0 ou m igual a 0, então:
            matriz[i][m] = 0
        se não:
            // codigo de preenchimento da matriz[i][m] com a mochila m de valor máximo 
        
```

Assim, obtemos a matriz preenchida dessa forma:

![Matriz Base](matriz_base.png)
:::

???

Se não estivermos no caso base, preencheremos a matriz utilizando a mesma lógica da solução recursiva.

```python
para cada i menor ou igual a n, faça:
    para cada m menor ou igual a M, faça:
        se i igual a 0 ou m igual a 0, então:
            matriz[n][M] = 0
        se não e peso[objeto] menor ou igual a m, então:
            se não e valor da mochila m com o objeto > valor da mochila m sem o objeto, então:
                matriz[n][M] = valor da melhor mochila m com o objeto
            se não:
                matriz[n][M] = valor da melhor mochila m sem o objeto
        se não:
            matriz[n][M] = valor da melhor mochila m sem o objeto
```

??? Checkpoint

Como pegamos o valor da mochila com o objeto? Consultar o algoritmo recursivo pode ajudar.

Vale ressaltar que, nesse algoritmo, a posição atual na matriz em relação a quantidade de itens analisados é dada por $i$, porém o ultimo objeto da sublista que está sendo analisada é representado por $i-1$, já que a matriz possui $n+1$ linhas e a lista total possui apenas $n$ objetos. ou seja:
* **Matriz**: $i$ -> último objeto da sublista; $i-1$ -> penúltimo objeto da sublista...
* **listas de pesos e valores**: $i-1$ -> ultimo objeto da sublista; $i-2$ -> penúltimo objeto da sublista

::: Gabarito
O valor da mochila com o objeto será o valor da mochila menor que está em $matriz[i-1][m - pesos[i-1]]$ mais o valor do objeto $valor[i-1]$
:::

???

??? Checkpoint

E o valor da mochila sem o objeto?

::: Gabarito
O valor da mochila sem o objeto será o valor da mochila de mesma capacidade, mas considerando apenas os objetos anteriores (matriz[i-1][m])
:::

???

Assim, o pseudo-código da solução dinâmica ficaria assim:

```python
para cada i menor ou igual a n, faça:
    para cada m menor ou igual a M, faça:
        se n igual a 0 ou M igual a 0, então:
            matriz[i][m] = 0
        se não e peso[i-1] menor ou igual a m, então:
            se não e matriz[n-1][m - pesos[i-1]] + valores[i-1] maior que matriz[i-1][m], então:
                matriz[i][m] = matriz[i-1][m - pesos[i-1]] + valores[i-1]
            se não:
                matriz[i][m] = matriz[i-1][m]
        se não:
            matriz[i][m] = matriz[i-1][m]
```

Agora que fizemos todas as alterações necessárias no algoritmo, vamos tentar preencher um pouco da matriz:

??? Checkpoint

Considerando o pseudo-código mostrado anteriomente, tente preencher a matriz quando $i=1$ e $m=1$ com a mochila de valor máximo possivel até então:

![Matriz[1][1] vazia](matriz_1_1_vazia.png)

::: Gabarito
Como o peso do `md headset` (peso 3) é maior que a capacidade da mochila ($m = 1$), não é possível coloca-lo dentro da mochila, então repetimos o estado da mochila na linha superior (matriz[i-1][m]).

:matriz_1_1_preenchimento
:::

???

Agora vamos pular um pouquinho para um caso diferente...

??? Checkpoint

Considerando o pseudo-código mostrado anteriomente, tente preencher a matriz quando $i=2$ e $m=6$ com a mochila de valor máximo possivel até então:

![Matriz[2][7] vazia](matriz_2_6_vazia.png)

::: Gabarito [passo 1]
Dessa vez, o peso do `md livro` (peso 4) cabe na mochila, então vamos comparar com o resultado de uma mochila menor mais o `md livro` com a mochila de mesma capacidade da linha anterior.

::: Gabarito [passo 2]
A melhor mochila que cabe o `md livro` é a mochila de capacidade $6-4=2$. O valor da mochila montada seria de $0+20=20$ que é pior que o valor da mochila da linha superior (valor 30), então repetimos o conteúdo da mochila de capacidade $m$ da linha anterior (matriz[i-1][m]).

:matriz_2_6_preenchimento
:::

???

Agora, vamos avançar mais um pouco na matriz e resolver outro caso possível...

??? Checkpoint

Considerando o pseudo-código mostrado anteriomente, tente preencher a matriz quando $i=2$ e $m=7$ com a mochila de valor máximo possivel até então:

![Matriz[2][7] vazia](matriz_2_7_vazia.png)

::: Gabarito
Novamente, o peso do `md livro` (peso 4) cabe na mochila, então vamos comparar com o resultado de uma mochila menor mais o `md livro` com a mochila de mesma capacidade da linha anterior.

A melhor mochila que cabe o `md livro` é a mochila de capacidade $7-4=2$. O valor da mochila montada seria de $30+20=50$ que é malhor que o valor da mochila da linha superior (valor 30), então vamos colocar esse resultado na mochila que estamos calculando (matriz[i][m]).

:matriz_2_7_preenchimento
:::

???

Não temos esse caso nesse exemplo, mas ainda assim não podemos esquecer da condição de que se o peso do objeto for maior que a capacidade da mochila, não podemos adicioná-lo.

Agora que já vimos todos os possíveis casos de escolha, verifique seu entendimento acompanhando o preenchimento completo da matriz.

!!! Aviso
Essa animação é realmente extensa. Se julgar que já entendeu o procedimento não precisa chegar ao final.
!!!

:preenchimento_completo

??? Checkpoint

Apenas com o pseudo-código, tente determinar a complexidade da solução dinâmica da mesma maneira que foi feito com a recursiva;

::: Gabarito
Teremos a solução do algoritmo definida apenas quando preenchermos a matriz, o que é feito em um looping de M (valor da capacidade máxima) com um looping interno de n (número de objetos). O que resulta em uma complexidade de $O(n\cdot M)$.
:::

???

Agora sim o o entendimento do algoritmo está finalizado, então só falta mais um tópico.

Comparação
------

Comparando a complexidade dos dois algoritmos apresentados para resolver o problema da mochila binária, é possível observar que o algoritmo de programação dinâmica possui uma complexidade melhor que o algoritmo recursivo, $O(n\cdot M)$ e $O(2^n)$. Isso acontece porque o algoritmo dinâmico apenas preencher sua matriz, enquanto o algoritmo recursivo precisa constantemente comparar duas mochilas que não estão calculadas (e para calculá-las, comparar mochilas menores que também não estão calculadas...), tornando sua complexidade exponencial.