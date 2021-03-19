## Algorithms - Miscellaneous

# 1. Introdução
Neste artigo, apresentaremos várias soluções para encontrar o k-ésimo maior elemento em uma sequência de números únicos. Usaremos uma matriz de inteiros para nossos exemplos.

Também falaremos sobre a complexidade de tempo média e de pior caso de cada algoritmo.

# 2. Soluções
Agora vamos explorar algumas soluções possíveis - uma usando uma classificação simples e duas usando o algoritmo de seleção rápida derivado da classificação rápida.

### 2.1. Ordenação
Quando pensamos sobre o problema, talvez a solução mais óbvia que vem à mente seja ordenar o array.

Vamos definir as etapas necessárias:

Classifique a matriz em ordem crescente
Como o último elemento da matriz seria o maior elemento, o kº maior elemento estaria no xº índice, onde x = comprimento (matriz) - k
Como podemos ver, a solução é direta, mas requer a classificação de todo o array. Portanto, a complexidade do tempo será O (n * logn):

```
public int findKthLargestBySorting(Integer[] arr, int k) {
    Arrays.sort(arr);
    int targetIndex = arr.length - k;
    return arr[targetIndex];
}
```

An alternative approach is to sort the array in descending order and simply return the element on (k-1)th index:

```
public int findKthLargestBySortingDesc(Integer[] arr, int k) {
    Arrays.sort(arr, Collections.reverseOrder());
    return arr[k-1];
}
```

### 2.2. Seleção rápida
Isso pode ser considerado uma otimização da abordagem anterior. Neste, escolhemos o QuickSort para classificação. Analisando a declaração do problema, percebemos que não precisamos realmente classificar todo o array - precisamos apenas reorganizar seu conteúdo para que o k-ésimo elemento do array seja o k-ésimo maior ou menor.

No QuickSort, escolhemos um elemento pivô e o movemos para sua posição correta. Também particionamos o array em torno dele. No QuickSelect, a ideia é parar no ponto onde o próprio pivô é o kº maior elemento.

Podemos otimizar ainda mais o algoritmo se não ocorrermos recorrentemente para os lados esquerdo e direito do pivô. Só precisamos repetir para um deles de acordo com a posição do pivô.

Vejamos as idéias básicas do algoritmo QuickSelect:

- Escolha um elemento pivô e particione a matriz de acordo;
- Escolha o elemento mais à direita como pivô;
- Reordene a matriz de modo que o elemento pivô seja colocado em seu devido lugar - todos os elementos menores que o pivô estariam em índices mais baixos, e os elementos maiores que o pivô seriam colocados em índices mais altos que o pivô;
- Se o pivô for colocado no k-ésimo elemento da matriz, saia do processo, pois o pivô é o k-ésimo maior elemento;
- Se a posição do pivô for maior que k, continue o processo com a submatriz esquerda, caso contrário, repita o processo com a submatriz direita.

Podemos escrever uma lógica genérica que também pode ser usada para encontrar o k-ésimo menor elemento. Definiremos um método findKthElementByQuickSelect () que retornará o k-ésimo elemento na matriz classificada.

Se classificarmos a matriz em ordem crescente, o k-ésimo elemento de uma matriz será o k-ésimo menor elemento. Para encontrar o maior elemento k, podemos passar k = comprimento (Array) - k.

Vamos implementar esta solução:

```
public int 
  findKthElementByQuickSelect(Integer[] arr, int left, int right, int k) {
    if (k >= 0 && k <= right - left + 1) {
        int pos = partition(arr, left, right);
        if (pos - left == k) {
            return arr[pos];
        }
        if (pos - left > k) {
            return findKthElementByQuickSelect(arr, left, pos - 1, k);
        }
        return findKthElementByQuickSelect(arr, pos + 1,
          right, k - pos + left - 1);
    }
    return 0;
}
```

Agora vamos implementar o método de partição, que escolhe o elemento mais à direita como um pivô, coloca-o no índice apropriado e particiona o array de forma que os elementos em índices mais baixos sejam menores que o elemento pivô.

Da mesma forma, os elementos com índices mais altos serão maiores do que o elemento pivô:

```
public int partition(Integer[] arr, int left, int right) {
    int pivot = arr[right];
    Integer[] leftArr;
    Integer[] rightArr;

    leftArr = IntStream.range(left, right)
      .filter(i -> arr[i] < pivot)
      .map(i -> arr[i])
      .boxed()
      .toArray(Integer[]::new);

    rightArr = IntStream.range(left, right)
      .filter(i -> arr[i] > pivot)
      .map(i -> arr[i])
      .boxed()
      .toArray(Integer[]::new);

    int leftArraySize = leftArr.length;
    System.arraycopy(leftArr, 0, arr, left, leftArraySize);
    arr[leftArraySize+left] = pivot;
    System.arraycopy(rightArr, 0, arr, left + leftArraySize + 1,
      rightArr.length);

    return left + leftArraySize;
}
```

Existe uma abordagem mais simples e iterativa para realizar o particionamento:

```
public int partitionIterative(Integer[] arr, int left, int right) {
    int pivot = arr[right], i = left;
    for (int j = left; j <= right - 1; j++) {
        if (arr[j] <= pivot) {
            swap(arr, i, j);
            i++;
        }
    }
    swap(arr, i, right);
    return i;
}

public void swap(Integer[] arr, int n1, int n2) {
    int temp = arr[n2];
    arr[n2] = arr[n1];
    arr[n1] = temp;
}
```

Esta solução funciona em tempo O (n) em média. No entanto, no pior caso, a complexidade do tempo será O (n ^ 2).

### 2.3. QuickSelect com partição aleatória

Esta abordagem é uma pequena modificação da abordagem anterior. Se o array estiver quase / totalmente classificado e se escolhermos o elemento mais à direita como um pivô, a partição dos subarrays esquerdo e direito será altamente desigual.

Este método sugere escolher o elemento pivô inicial de maneira aleatória. No entanto, não precisamos alterar a lógica de particionamento.

Em vez de chamar partição, chamamos o método randomPartition, que escolhe um elemento aleatório e o troca pelo elemento mais à direita antes de finalmente invocar o método de partição.

Vamos implementar o método randomPartition:

```
public int randomPartition(Integer arr[], int left, int right) {
    int n = right - left + 1;
    int pivot = (int) (Math.random()) * n;
    swap(arr, left + pivot, right);
    return partition(arr, left, right);
}
```

Esta solução funciona melhor do que o caso anterior na maioria dos casos.

A complexidade de tempo esperada de QuickSelect aleatório é O (n).

No entanto, a pior complexidade de tempo ainda permanece O (n ^ 2).

# 3. Conclusão
Neste artigo, discutimos diferentes soluções para encontrar o k-ésimo maior (ou menor) elemento em uma matriz de números únicos. A solução mais simples é classificar a matriz e retornar o k-ésimo elemento. Esta solução tem uma complexidade de tempo de O (n * logn).

Também discutimos duas variações do Quick Select. Este algoritmo não é direto, mas tem uma complexidade de tempo de O (n) em casos médios.