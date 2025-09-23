# Roteiro de Atividade Prática: Lista Duplamente Ligada
-----

## 1\. Objetivos de Aprendizagem

Ao concluir esta atividade, você deverá ser capaz de:

  * **Explicar** o conceito, as vantagens e as aplicações de uma **lista duplamente ligada**.
  * **Implementar** operações essenciais: inserção no final, remoção no início, remoção no fim e varredura em ambas as direções (frente→trás e trás→frente).
  * **Analisar e raciocinar** sobre **invariantes** da estrutura, tratar **casos de borda** (lista vazia, lista com um elemento) e entender a **complexidade** das operações.
  * **Praticar** a manipulação de ponteiros, incluindo **alocação e liberação de memória dinâmica** de forma segura para evitar vazamentos (`memory leaks`).

-----

## 2\. Visão Geral: A Lista Duplamente Ligada

Imagine uma lista de compras onde cada item, além de saber qual é o próximo da lista, também sabe qual era o anterior. Ou pense em um trem, onde de qualquer vagão você pode se mover tanto para o vagão da frente quanto para o de trás. Essa é a ideia central de uma lista duplamente ligada.

É uma coleção de elementos, chamados de **nós** (`nodes`), conectados de forma sequencial. A grande diferença em relação a uma lista simplesmente ligada é que cada nó possui **dois** ponteiros:

  * Um ponteiro (`prox`) que aponta para o **próximo** nó da sequência.
  * Um ponteiro (`ant`) que aponta para o nó **anterior**.

<!-- end list -->

```
          +------+      +------+      +------+
NULL <--- | ant  | <--> | ant  | <--> | ant  |
          | valor|      | valor|      | valor|
          | prox | ---> | prox | ---> | prox | ---> NULL
          +------+      +------+      +------+
             ^                             ^
             |                             |
          primeiro                       ultimo
```

No nosso código, a estrutura de cada nó é definida como:

```cpp
struct NO {
    int valor;
    NO* prox; // Ponteiro para o próximo
    NO* ant;  // Ponteiro para o anterior
};
```

Para gerenciar a lista, usamos dois ponteiros globais que nos dão acesso rápido às extremidades:

  * `primeiro`: Guarda o endereço do primeiro nó da lista. Se a lista está vazia, ele é `NULL`.
  * `ultimo`: Guarda o endereço do último nó. Se a lista está vazia, ele também é `NULL`.

### Vantagens Principais

1.  **Navegação Bidirecional**: É a vantagem mais óbvia. Você pode percorrer a lista tanto do início para o fim (usando `prox`) quanto do fim para o início (usando `ant`) com a mesma eficiência.
2.  **Eficiência na Remoção**: Remover o último elemento se torna uma operação de tempo constante, **O(1)**, pois temos um ponteiro direto para ele (`ultimo`) e, a partir dele, acesso imediato ao seu antecessor (`ultimo->ant`). Em uma lista simples, essa operação seria O(n).

### Invariantes e Regras da Estrutura

Para que a lista funcione corretamente, certas "regras" (invariantes) devem ser sempre verdadeiras:

  * **Lista Vazia**: `primeiro == NULL` e `ultimo == NULL`.
  * **Lista Não Vazia**:
      * O ponteiro `ant` do primeiro nó deve ser sempre `NULL`: `primeiro->ant == NULL`.
      * O ponteiro `prox` do último nó deve ser sempre `NULL`: `ultimo->prox == NULL`.
  * **Consistência dos Elos**: Para qualquer nó `X` que não seja o último, o ponteiro `ant` de seu sucessor deve apontar de volta para `X`. Ou seja: `X->prox->ant == X`. Da mesma forma, para qualquer nó que não seja o primeiro, `X->ant->prox == X`.

### Complexidade das Operações

  * **Inserir no fim**: `O(1)` (pois já temos o ponteiro `ultimo`).
  * **Remover do início/fim**: `O(1)` (pois `primeiro` e `ultimo` nos dão acesso direto).
  * **Buscar um elemento (pelo valor)**: `O(n)` (no pior caso, precisamos percorrer toda a lista).
  * **Percorrer a lista (em qualquer direção)**: `O(n)`.

-----

## 3\. Código Base Fornecido

O arquivo `ListaDuplamenteLigada.cpp` já contém a estrutura inicial:

  * A definição da `struct NO` e dos ponteiros globais `primeiro` e `ultimo`.
  * Uma função `menu()` para interagir com o usuário.
  * Implementações prontas para as seguintes funções:
      * `inicializar()`: Limpa a lista, liberando a memória e zerando os ponteiros.
      * `exibirQuantidadeElementos()`: Conta e mostra quantos nós existem.
      * `exibirElementos()`: Percorre e exibe os elementos do início para o fim.
      * `inserirElemento()`: Adiciona um novo nó sempre no **final** da lista.

> **Nota Importante:** O código utiliza `malloc` e `free` para gerenciamento de memória. Para esta atividade, mantenha este padrão. Em projetos C++ mais modernos, é comum usar `new`/`delete` ou ponteiros inteligentes (`std::unique_ptr`), mas a mistura de `malloc` com `delete` ou `new` com `free` pode causar erros graves.

-----

## 4\. Atividade Proposta: Implemente as Funções

Sua tarefa é implementar as três funções a seguir. Preste muita atenção aos casos de borda e às invariantes da estrutura.

### 4.1. `exibirReverso()`

Esta função deve percorrer a lista do fim para o início, exibindo o valor de cada nó.

**Roteiro de Raciocínio:**

1.  **Ponto de Partida**: Por onde você deve começar a percorrer a lista? Com o ponteiro `primeiro` ou `ultimo`?
2.  **Condição de Parada**: Como você sabe que chegou ao "início" ao percorrer de trás para frente? Qual será a condição do seu laço `while`?
3.  **Passo da Iteração**: Se você está em um nó `aux`, como você se move para o nó anterior a ele?
4.  **Caso de Borda**: O que sua função deve fazer se a lista estiver vazia (`ultimo == NULL`)?

### 4.2. `excluirPrimeiroElemento()`

Esta função deve remover o primeiro nó da lista.

**Roteiro de Raciocínio:**

1.  **Lista Vazia**: A primeira verificação deve ser: a lista está vazia? Se sim, apenas avise o usuário e encerre a função.
2.  **Identifique os Casos**: Existem dois cenários principais quando a lista não está vazia:
      * **Caso 1: Apenas um elemento.** Como você pode verificar se a lista tem só um nó? (Dica: compare os ponteiros `primeiro` e `ultimo`). Se for o caso, como os ponteiros `primeiro` e `ultimo` devem ficar após a remoção?
      * **Caso 2: Múltiplos elementos.**
          * Para onde o ponteiro `primeiro` deve apontar após a remoção?
          * O novo primeiro nó precisa ter seu elo com o passado rompido. Qual ponteiro do novo `primeiro` nó (`ant` ou `prox`) precisa ser ajustado para `NULL`?
3.  **Liberação de Memória**: Em ambos os casos (um ou múltiplos elementos), você precisa guardar o endereço do nó a ser removido antes de alterar os ponteiros da lista. Ao final, não se esqueça de usar `free()` para liberar a memória e evitar `memory leaks`.

### 4.3. `excluirUltimoElemento()`

Esta função é simétrica à anterior e deve remover o último nó da lista.

**Roteiro de Raciocínio:**

1.  **Lista Vazia**: Assim como antes, verifique primeiro se a lista está vazia.
2.  **Identifique os Casos**:
      * **Caso 1: Apenas um elemento.** A lógica é idêntica à da exclusão do primeiro. O que acontece com `primeiro` e `ultimo`?
      * **Caso 2: Múltiplos elementos.**
          * Para onde o ponteiro `ultimo` deve apontar após a remoção?
          * O novo último nó precisa ter seu elo com o futuro rompido. Qual ponteiro do novo `ultimo` nó (`ant` ou `prox`) precisa ser ajustado para `NULL`?
3.  **Liberação de Memória**: Novamente, guarde o ponteiro do nó a ser removido e use `free()` no final.

-----

## 5\. Roteiro de Testes (Recomendado)

Para garantir que sua implementação está correta, execute **exatamente** esta sequência de operações usando o menu do programa e verifique se as saídas são as esperadas:

1.  **Opção 1 (Inicializar Lista)**: Garante que a lista comece limpa.
2.  **Opção 4 (Exibir elementos)** → A saída deve ser: `Lista vazia`.
3.  **Opção 2 (Inserir)**: Insira os valores `10`, `20` e `30`, nesta ordem.
      * **Opção 4 (Exibir elementos)** ⇒ Deve mostrar `10`, `20`, `30`.
      * **Opção 5 (Exibir reverso)** ⇒ Deve mostrar `30`, `20`, `10`.
4.  **Opção 6 (Excluir primeiro)**
      * A lista agora deve conter `20`, `30`.
      * **Opção 5 (Exibir reverso)** ⇒ Deve mostrar `30`, `20`.
5.  **Opção 7 (Excluir último)**
      * A lista agora deve conter apenas `20`.
      * **Opção 5 (Exibir reverso)** ⇒ Deve mostrar `20`.
6.  **Opção 7 (Excluir último)** novamente. A lista agora deve ficar vazia.
      * **Opção 3 (Exibir quantidade)** ⇒ A saída deve ser `Quantidade de elementos: 0`.
      * **Opção 4 (Exibir elementos)** ⇒ Deve mostrar `Lista vazia`.
      * **Opção 5 (Exibir reverso)** ⇒ Deve mostrar `Lista vazia`.

> **Dica**: Crie outros testes\! Por exemplo, insira 3 elementos e remova o primeiro 3 vezes seguidas. Ou insira apenas 1 elemento e tente removê-lo com a opção 6 e depois com a opção 7 (em execuções diferentes).

-----

## 6\. Erros Comuns e Como Evitar

  * **Acessar Ponteiros Nulos**: Sempre verifique se um ponteiro é `NULL` antes de tentar acessar um de seus membros (ex: `primeiro->prox`). Isso é a principal causa de erros de "Access Violation".
  * **Esquecer de Atualizar um Elo**: Ao remover um nó, lembre-se que os vizinhos dele precisam ser "religados". Em uma lista dupla, isso pode envolver atualizar tanto o `prox` do nó anterior quanto o `ant` do nó seguinte.
  * **Vazamento de Memória (`Memory Leak`)**: Sempre que um nó é removido da lista, sua memória precisa ser devolvida ao sistema usando `free()`. Se você apenas alterar os ponteiros sem liberar a memória, ela se torna inacessível e perdida.
  * **Lógica Incorreta para Casos de Borda**: Tratar a remoção de uma lista com um único elemento de forma errada pode deixar os ponteiros `primeiro` ou `ultimo` apontando para memória inválida (`dangling pointers`).

-----

## 7\. Material Complementar para Estudo

Para aprofundar seu conhecimento sobre o tema, recomendamos os seguintes recursos:

  * **Visualização Interativa**:

      * **VisuAlgo - Doubly Linked List**: Uma ferramenta fantástica que permite criar uma lista duplamente ligada e executar operações passo a passo, visualizando como os ponteiros são alterados.
          * [https://visualgo.net/en/list](https://visualgo.net/en/list) (Selecione "Doubly Linked List" na barra superior).

  * **Artigos e Tutoriais**:

      * **GeeksforGeeks - Doubly Linked List**: Um artigo completo com explicações detalhadas e exemplos de código para todas as operações.
          * [https://www.geeksforgeeks.org/doubly-linked-list/](https://www.geeksforgeeks.org/doubly-linked-list/)
      * **Programiz - Doubly Linked List**: Outro tutorial excelente com ilustrações e código passo a passo.
          * [https://www.programiz.com/dsa/doubly-linked-list](https://www.programiz.com/dsa/doubly-linked-list)


-----

## 8\. O Que Entregar

  * Link para um fork deste repositório com a sua resolução
