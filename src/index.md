Mochila Binária
======

O problema
------

O problema da mochila binária é um problema de otimização combinatória em que se deseja preencher uma mochila com uma combinação de itens que possuem diferentes pesos e valores de forma que a soma de seus valores seja máxima e a soma de seus pesos não ultrapasse um certo limite (capacidade). 

Pensando de uma maneira menos técnica, imagine que você está indo viajar de avião e tem uma lista de coisas que precisa levar nessa viagem. Você levará uma *mochila* e uma *mala* que será despachada. Por uma questão de segurança, você pretende colocar o máximo de coisas de valor (monetário) na *mochila* até enchê-la, e o que sobrar será colocado na *mala*. Sua *mochila* não tem um volume infinito (óbvio), então há uma quantidade máxima de objetos que podem ser colocados nela até que ela fique cheia. Outro ponto importante é que você só tem um de cada objeto da lista.

Dessa forma, para organizar sua mochila da melhor maneira possível, você vai precisar saber o tamanho e o valor de cada uma das coisas que você precisa levar nessa viagem.

Antes de pensar em código propriamente, é importante fazer pelo menos um exemplo "na mão". Vamos pegar um caso mais fácil agora no começo. Então, vamos considerar que sua mochila possui **10 [unidades de volume] de capacidade** e a pequena lista de ítems abaixo:

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
A melhor solção para essa lista seria escolher os item `md Controle`, `md Carregador` e `md Garrafa` obtendo um peso total de *9* e um valor total de *100*. 

![Lista Pequena com Solução Certa](lista_pequena_solucao_certa.png)
:::

???

Note que, esta solução nem sequer utiliza o `md Travesseiro`, e ainda sim obtivemos um valor maior que o anterior. Além disso, você deve ter percebido que, por mais que o peso da mochila não tenha sido atingido, conseguiu-se extrair o maior valor possíveis dos ítems da lista. Isso ocorre porque **a melhor solução nem sempre é a que preenche a mochila completamente**

Em termos de lógica de resolução, existem algumas maneiras diferentes de se encontrar a melhor mochila possível. Uma delas é por um método de tentativa e erro (que muito provavelmente foi a que você usou no Checkpoint anterior), o que não é ruim para casos pequenos como aquele, mas definitivamente não é a mais rápido, principalmente quando considerarmos problemas em que a lista de ítens é extremamente extensa.

Entao vamos começar a pensar em uma estrutura um pouco mais voltada à código de uma função que resolva o problema. E para que essa função possa ser montada, precisamos conhecer quais são suas entradas e saídas.

??? Checkpoint

Com base no exemplo dado anteriormente, quais seriam as **entradas** necessárias para possibilitar a solução de um problema da mochila binária. Qual o formato da **saída** que é esperado?

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

O `md valor do item` é fator de interesse que cada item possui e que se deseja maximizar. No exemplo acima, o *"valor"* era o valor monetário daquele item, mas esse

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

A ordem da lista de pesos e valores deve ser a mesma, ou seja, se o peso do Headset está na primeira posição da lista de pesos, o valor dele também deve estar na primeira posição da lista de valores. Perceba que isso significa que cada índice estará associado a um objeto. Tenha isso em mente, pois vai ser muito importante mais pra frente.

Ordenando a lista do nosso problema teríamos:

![Lista com índices](lista_objetos_indices.png)

Agora vamos começar a estruturar o algoritmo. Se na solução do algoritmo um determinado objeto está na mochila é necessariamente verdade que:
1. A capacidade da mochila não é ultrapassada ao colocar aquele objeto
2. A mochila fica mais valiosa com aquele objeto dentro do que sem ele ( ou seja, se a mochila fosse preenchida até a capacidade máxima mas apenas com os outros objetos )

Para garantir o primeiro ponto, primeiramente não podemos adicionar um objeto que tenha capacidade maior que a capacidade máxima da mochila. Com isso, já sabemos que o algoritmo só irá conferir as outras condições se o objeto puder ser adicionado.

```python
se pesos[objeto] <= M, então:
    ve se o objeto entra na mochila
se nao:
    retorna o valor da mochila sem o objeto
```

Além disso, devemos ter o cuidado de adicionar o objeto em uma mochila que tenha peso total menor que a capacidade máxima, pois ao adicionarmos o objeto, o peso da mochila aumentará.

??? Checkpoint

Partindo do problema inicial, se quisermos adicionar o tablet (peso 4) na mochila (capacidade 10), qual deve ser o peso máximo da mochila antes de colocarmos o tablet?

::: Gabarito
O peso máximo da mochila antes do tablet ser adicionado deve ser a capacidade máxima menos o peso do objeto. Nesse caso $10-4 = 6$. Caso fosse maior que isso, a capacidade da mochila seria ultrapassada ao colocarmos o tablet.
:::

???

Generalizando para qualquer objeto, se a mochila tem capacidade $M$ o objeto só pode ser adicionado em uma mochila de capacidade $M - peso[objeto]$.

Garantimos que a capacidade da mochila não será ultrapassada ao adicionar o objeto, entretanto ainda devemos saber se aquele objeto deve ser adicionado. Para isso, devemos pensar no segundo ponto. Para saber se a mochila fica mais valiosa com o objeto ou sem ele, precisamos comparar o valor de uma mochila que tem o objeto com o valor de uma mochila que não tem o objeto.

```python
se peso[objeto] <= M, então:
    se valor da mochila M com objeto > valor da mochila M sem objeto:
        retorna o valor da mochila com o objeto
    se não:
        retorna o valor da mochila sem o objeto
se nao:
    retorna o valor da mochila sem o objeto
```
Aproveitando o checkpoint anterior, vamos analisar se o tablet deve entrar na mochila. Para fazer a comparação precisamos do peso das duas mochilas (com o tablet e sem o tablet). 

Como foi mostrado no checkpoint anterior, para montar uma mochila com o tablet, adicionamos ele em uma mochila mais "vazia". Ao adicionar o tablet, o valor da mochila também aumentará. Porém, para saber o valor total da mochila devemos saber também o valor da mochila mais "vazia" utilizada. 

??? Checkpoint

Pensando em maximizar o valor da mochila, podemos adicionar o tablet em qualquer mochila de peso máximo 6?

::: Gabarito
Não, para a mochila ter valor máximo no final o tablet deve ser adicionado em uma mochila de peso máximo 6 que tenha o valor máximo para essa capacidade. 

Se isso ainda não ficou claro, pode ajudar pensar que após analisar o último objeto, teremos obtido a mochila final, que deve ter valor máximo. Se não adicionarmos o objeto em uma mochila com o maior valor possível, não teremos o maior valor possível na mochila final.
:::

???

Sabendo então que a mochila mais "vazia" deverá, também, ter o maior valor possível, podemos achar esse valor através do mesmo algoritmo. E tendo esse valor, podemos calcular o valor da mochila após adicionado o objeto (nesse caso o tablet).

```python
se peso[objeto] <= M, então:
    se (valor da mochila M - pesos[objeto]) + valores[objeto] > valor da mochila sem o objeto:
        retorna o valor da mochila M - pesos[objeto]
    se não:
        retorna o valor da mochila sem o objeto
```

Agora, para finalizar a escolha, basta descobrir o valor máximo da mochila sem o objeto que está sendo analisado. Com isso e considerando o que já foi preenchido do pseudo-código, você já deve ter percebido que o algoritmo utiliza soluções de sub-problemas menores, mochilas de menores capacidades ou com menos objetos possíveis. Dessa forma, já podemos revelar que uma das formas de resolver o problema da mochila binária é recursiva.

??? Checkpoint

Pensando na solução recursiva e nas entradas do algoritmo, o que precisaria ser alterado na(s) entrada(s) do algoritmo para obter a mochila de máximo valor sem considerar um dos objetos? Caso não lembre, os objetos estão relacionados com os indexes das listas de pesos e valores.

::: Gabarito
Para encontrar a solução da mochila sem um objeto, basta passar uma lista menor de pesos e valores como entrada do algoritmo, de forma que o valor e peso do objeto que não deve estar na mochila não estejam nas listas.
:::

???

Sabendo disso, podemos terminar a lógica de escolha dos objetos. Lembrando que na solução recursiva sempre solucionamos a ultima iteração (nesse caso colocar ou não o último objeto) assumindo que temos a solução anterior (já foi tomada a decisão de colocar ou não todos os outros objetos).

```python
se peso[objeto] <= M, então:
    se (valor da mochila M - pesos[objeto]) + valores[objeto] > valor da mochila M (pesos, valores, n-1, M):
        retorna o valor da mochila M - pesos[objeto]
    se não:
        retorna o valor da mochila sem o objeto
```

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

Com isso falta apenas mais um tópico.

Complexidade da Solução Dinâmica
------

Teremos a solução do algoritmo definida, quando preenchermos as matrizes, o que é feito em um looping de M (valor da capacidade máxima) com um looping interno de n (número de objetos). O que resulta em uma complexidade de  $O(n\cdot M)$.

Comparação
------

Comparando a complexidade dos dois algoritmos apresentados para resolver o problema da mochila binária, é possível observar que o algoritmo de programação dinâmica possui uma complexidade melhor que o algoritmo recursivo, $O(n\cdot M)$ e $O(2^n)$. Isso acontece porque o algoritmo dinâmico apenas preencher sua matriz, enquanto o algoritmo recursivo precisa constantemente comparar duas mochilas que não estão calculadas (e para calculá-las, comparar mochilas menores que também não estão calculadas), tornando sua complexidade exponencial.
