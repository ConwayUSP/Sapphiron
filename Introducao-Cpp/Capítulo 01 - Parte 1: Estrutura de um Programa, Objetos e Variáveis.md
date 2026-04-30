# Capítulo 01 - Parte 1: Estrutura de um Programa, Objetos e Variáveis

Sejam bem-vindos ao Capítulo 1 do nosso curso!

Agora que já vimos a “Hello, world!” e a instalação dos ambientes, é hora de compreender como se organiza, de fato, um programa em C++. 

Neste capítulo, daremos uma primeira olhada em alguns tópicos essenciais para qualquer programa em C++. 

Como há uma grande variedade de tópicos a serem abordados, abordaremos a maioria deles em um nível bastante superficial (apenas o suficiente para você se familiarizar). O objetivo deste capítulo é ajudar você a entender como programas básicos em C++ são construídos.

Além disso, nessa parte do capítulo veremos como o programa manipula os dados, a partir de **objetos** e **variáveis**, para alcançar o seu objetivo final.

## 1.1 A Função `main`

Em C++, as instruções são normalmente agrupadas em unidades chamadas funções. Uma função é um conjunto de instruções executadas sequencialmente (em ordem, de cima para baixo).

Todo código executável deve conter uma função `main` com a seguinte assinatura:

```cpp
#include <iostream>

int main() {
    // Aqui vai o código do programa
    return 0;
}
```
Vamos analisar o exemplo "Hello, world!" do Capítulo 0:

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, world!\n";
    return 0;
}
```

**Partes essenciais:**

* `#include <iostream>`: Inclui a biblioteca para entrada/saída (exibe mensagens e recebe dados do usuário).
* `int main() { ... }`: A função principal onde a execução começa. E `int` indica que a função retorna um valor inteiro.
* `std::cout`: Envia dados para a saída padrão (geralmente o terminal).
* `return 0`: Indica que o programa terminou com sucesso.

## 1.2 Comentários

Em C++, há dois estilos diferentes de comentários, ambos com o mesmo propósito: ajudar os programadores a documentar o código de alguma forma.

### Comentários de Linha Única

O `//` símbolo inicia um comentário de linha única em C++, que instrui o compilador a ignorar tudo, desde o `//` símbolo até o final da linha. Por exemplo:

```cpp
// Comentário de uma linha
int x = 5;  // atribui 5 à variável x
```

### Comentários de Múltiplas Linhas

O par de símbolos `/*` e `*/` denota um comentário multilinha. Tudo entre os símbolos é ignorado. Por exemplo:

```cpp
/*
   Isso é um comentário
   que ocupa várias linhas.
   Útil para explicações longas!
*/
```
> Dica: use comentários para explicar **"porquês"** e não **"o quê"**. Código claro costuma dispensar comentários óbvios.

## 1.3 Variáveis

Variáveis são nomes associados a locais de memória onde os dados desses objetos são armazenados. 

Declarar uma variável é dizer ao compilador: “reserve espaço para este tipo de dado e associe-o a este identificador”. 

Para usá-las, declare seu tipo e nome:

```cpp
int idade;            // Declara uma variável inteira chamada 'idade'
idade = 25;           // Atribui o valor 25 à variável através do operador de atribuição "="

double preco = 9.99;  // Declara e inicializa em uma única linha
```

### Exemplos de Tipos de Dados Comuns:

| Tipo     | Descrição                    | Exemplo     | Tamanho (bytes) |
| -------- | ---------------------------- | ----------- | --------------- |
| `int`    | Números inteiros             | 42, -7      | 4               |
| `double` | Números decimais             | 3.14, -0.5  | 8               |
| `char`   | Caractere único              | 'A', '\$'   | 1               |
| `bool`   | Valores lógicos              | true, false | 1               |

Outra declaração aceita para tipos de dados é unir as palavras reservadas signed e unsigned ao tipo, declarações que aceitam valores com representação negativa **(signed)** ou tipos que não aceitam valores negativos **(unsigned)**.

Recomendamos procurar mais sobre outros tipos de dados para se apronfundar e ter maior repertório.

## 1.4 Objetos

Objetos em C++ são instâncias de tipos, seja um tipo pré-definido pela linguagem (como `int`, `double` ou `std::string`), seja um tipo definido pelo próprio programador (uma classe).

Cada objeto ocupa um espaço de memória para armazenar dados. No caso de tipos definidos pelo programador, um objeto também pode agrupar atributos — os dados que ele carrega — e métodos — os comportamentos que ele oferece. 

Por exemplo, um objeto do tipo `Pessoa` carrega atributos como `nome` e `idade`, e tem acesso a métodos como `falar()` ou `aniversariar()`.

Já tipos primitivos como `int` e `double` armazenam apenas um valor simples, sem atributos ou métodos.

Veja os exemplos a seguir:

```cpp
int x = 5;                          // Objeto do tipo primitivo
std::string nome = "Alice";         // Objeto da classe std::string (biblioteca padrão)

Pessoa pessoa1;                     // Objeto de classe definida pelo programador
pessoa1.nome = "Alice";             // Acessando um atributo
pessoa1.falar();                    // Chamando um método
```

> Essa é só uma visão breve e geral sobre o tema, caso queira explorar mais os conceitos de objetos e classes, veja a primeira aula do capítulo 00 de POO.

## Questões complementares

Abaixo, estão alguns exercícios para se colocar em prática o que foi visto. Recomendamos que você tente fazer sem ajuda externa, principalmente se esse for o seu primeiro contato com o assunto. 

Qualquer dúvida, pode entrar em contato com qualquer um dos autores do curso. Bom aprendizado!

1) Dado o exemplo:

```cpp
std::string cidade = "São Paulo";
```
Identifique:
- O tipo do objeto.
- O valor armazenado.
- O nome (identificador) usado para acessá-lo.

2) Crie um pequeno programa que:
- Declare duas variáveis int para representar ano de nascimento e ano atual.
- Calcule a idade.
- Use std::cout para exibir o resultado com uma mensagem amigável. (Caso necessário veja a explicação sobre std::cout na Parte 2)
- Inclua comentários que expliquem o propósito de cada etapa.


## Conclusões

Nesta primeira parte do Capítulo 1, você conheceu a estrutura básica de todo programa em C++: da inclusão de bibliotecas com #include à definição da função main.

Também vimos como inserir comentários de linha única ou múltiplas linhas para documentar o “porquê” de cada trecho, mantendo o código legível e fácil de entender.

Vimos também que objetos em C++ representam instâncias concretas de tipos — sejam tipos primitivos como `int` e `double`, ou tipos definidos pelo programador, como o exemplo **Pessoa** visto na seção 1.4, e que cada objeto ocupa um espaço de memória, guardando um valor simples, no caso dos tipos primitivos, ou um conjunto de atributos, no caso de tipos definidos pelo programador.

Já as variáveis são simplesmente nomes associados a esses locais de memória, permitindo que seu programa armazene, modifique e recupere valores ao longo da execução.

Até breve!
