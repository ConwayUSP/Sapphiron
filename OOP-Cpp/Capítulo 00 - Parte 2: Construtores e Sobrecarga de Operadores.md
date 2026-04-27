# Capítulo 00: Objetos e Classes

## 0.4 Como escrever um construtor

Na parte anterior, vimos que para criar um objeto Gato poderíamos escrever:

```cpp
Gato gatito;
gatito.nome = "Gatito";
gatito.idade = 7;
gatito.raca = PERSA;
```

Funciona, mas é bem verboso. E se pudéssemos passar essas informações na hora de criar o objeto, assim como fazemos com uma função? É exatamente para isso que existe o **construtor**.

Um construtor é um método especial que é chamado automaticamente toda vez que um objeto é criado. Ele é responsável por inicializar os atributos do objeto com os valores que quisermos.

### Construtor padrão

Em C++, se você não escrever nenhum construtor, o compilador gera um automaticamente, o chamado **construtor padrão** (default constructor). 

Ele não faz nada além de reservar espaço na memória para o objeto. É o equivalente a receber um gatinho sem nome, sem idade e sem raça definidos.

Mas, claro, podemos escrever o nosso próprio:

```cpp
class Gato {
public:
    std::string nome;
    int idade;
    std::string raca;

    // Construtor padrão escrito manualmente
    Gato() {
        nome = "Gatito Pereira";
        idade = 0;
        raca = "Indefinida";
    }
};

int main() {
    Gato gatito; // Chama o construtor padrão
    std::cout << gatito.nome << std::endl; // "Gatito Pereira"
}
```

### Construtor com parâmetros

Na maioria dos casos, queremos inicializar o objeto com valores específicos já na criação. Para isso, o construtor pode receber parâmetros como qualquer outra função:

```cpp
class Gato {
public:
    std::string nome;
    int idade;
    std::string raca;

    // Construtor com parâmetros
    Gato(std::string n, int i, std::string r) {
        nome = n;
        idade = i;
        raca = r;
    }
};

int main() {
    Gato gatito("Gatito MJ", 7, "Persa");
    std::cout << gatito.nome << std::endl; // "Gatito MJ"
}
```

Agora criamos e inicializamos o objeto em uma única linha.

### Lista de inicialização

Aqui chegamos em uma das formas mais idiomáticas do C++. Em vez de atribuir os valores dentro do corpo do construtor, podemos usar a **lista de inicialização**, separada por `:` logo após os parâmetros:

```cpp
class Gato {
public:
    std::string nome;
    int idade;
    std::string raca;

    // Construtor com lista de inicialização
    Gato(std::string n, int i, std::string r)
        : nome(n), idade(i), raca(r) {}
};
```

O resultado final é o mesmo, mas essa forma é preferida por alguns motivos:

1. **É mais eficiente:** na versão com atribuição dentro do corpo, os atributos são primeiro inicializados com valores padrão e depois sobrescritos. Com a lista de inicialização, eles são inicializados diretamente com o valor correto, sem o passo extra.

2. **É obrigatória em alguns casos:** atributos do tipo `const` ou referências (`&`) precisam ser inicializados na lista, pois não podem ser atribuídos depois de criados.

```cpp
class Gato {
public:
    const std::string especie; // 'const': não pode ser alterado depois
    std::string nome;

    Gato(std::string n)
        : especie("Maewtwo"), nome(n) {} // única forma válida para 'especie'
};
```

3. **É o padrão que você verá em código profissional:** Se você der uma bisbilhotada nos construtores dos próximos capítulo, verá que todos usam essa sintaxe.

### Múltiplos construtores

Uma classe pode ter mais de um construtor, desde que tenham assinaturas diferentes, ou seja, tipos ou quantidade de parâmetros distintos. Isso é chamado de **sobrecarga de construtores**:

```cpp
class Gato {
public:
    std::string nome;
    int idade;

    // Construtor padrão
    Gato() : nome("Sem nome"), idade(0) {}

    // Construtor com nome
    Gato(std::string n) : nome(n), idade(0) {}

    // Construtor completo
    Gato(std::string n, int i) : nome(n), idade(i) {}
};

int main() {
    Gato a;                    // Sem nome, idade 0
    Gato b("Mingau");          // Mingau, idade 0
    Gato c("Garfield", 7);     // Garfield, idade 7
}
```

O compilador decide automaticamente qual construtor chamar com base nos argumentos fornecidos.

## 0.5 Operator Overload (Sobrecarga de Operadores)

Outra característica bem marcante do C++ é permitir que o programador redefina o comportamento dos operadores da linguagem, como `+, -, *, <, ==,` e vários outros, para os seus próprios tipos.

Isso se chama **sobrecarga de operadores** (*operator overloading*).

Imagine que você criou uma classe `Vetor2D` para representar vetores matemáticos no plano:

```cpp
class Vetor2D {
public:
    float x, y;
    Vetor2D(float x, float y) : x(x), y(y) {}
};
```

Se você quiser somar dois vetores sem sobrecarga de operadores, teria que escrever algo como:

```cpp
Vetor2D somar(Vetor2D a, Vetor2D b) {
    return Vetor2D(a.x + b.x, a.y + b.y);
}

Vetor2D resultado = somar(v1, v2);
```

Funciona, mas perde toda a expressividade matemática. Com sobrecarga, podemos simplesmente escrever:

```cpp
Vetor2D resultado = v1 + v2;
````

Muito mais natural, certo?

### Como implementar

A sintaxe para sobrecarregar um operador usa a palavra-chave `operator` seguida do símbolo que queremos redefinir:

```cpp
#include <iostream>

class Vetor2D {
public:
    float x, y;

    Vetor2D(float x, float y) : x(x), y(y) {}

    // Sobrecarga do operador +
    Vetor2D operator+(const Vetor2D& outro) const {
        return Vetor2D(x + outro.x, y + outro.y);
    }

    // Sobrecarga do operador ==
    bool operator==(const Vetor2D& outro) const {
        return x == outro.x && y == outro.y;
    }
};

int main() {
    Vetor2D v1(1.0, 2.0);
    Vetor2D v2(3.0, 4.0);

    Vetor2D v3 = v1 + v2;   // Chama operator+
    std::cout << v3.x << ", " << v3.y << std::endl; // 4.0, 6.0

    if (v1 == v2) { // Chama operator==
        std::cout << "Iguais!" << std::endl;
    }
}
```

> O `const` no final da assinatura indica que o método não modifica o objeto. O `const Vetor2D&` outro indica que recebemos o outro objeto por referência, sem copiar, e sem modificá-lo. Boas práticas que você verá frequentemente em código C++

### Um exemplo mais próximo: o nosso Gato

Voltando ao nosso gatinho: suponha que queiramos comparar dois gatos pelo nome, ou imprimir um gato diretamente com `std::cout`. Podemos sobrecarregar `==` e o operador de saída `<<`:

```cpp
#include <iostream>
#include <string>

class Gato {
private:
    std::string nome;
    int idade;

public:
    Gato(std::string n, int i) : nome(n), idade(i) {}

    // Dois gatos são iguais se tiverem o mesmo nome e idade
    bool operator==(const Gato& outro) const {
        return nome == outro.nome && idade == outro.idade;
    }

    // Permite usar std::cout << gato
    friend std::ostream& operator<<(std::ostream& os, const Gato& g) {
        os << g.nome << " (" << g.idade << " anos)";
        return os;
    }
};

int main() {
    Gato gatito("Gatito007", 67);
    Gato clone("Gatito007", 67);
    Gato outro("Mingau", 3);

    std::cout << gatito << std::endl; // "Gatito007 (67 anos)"

    if (gatito == clone) {
        std::cout << "São o mesmo gato!" << std::endl;
    }

    if (!(gatito == outro)) {
        std::cout << "São gatos diferentes." << std::endl;
    }
}
```

A palavra-chave `friend` no operador `<<` merece atenção: como esse operador é tecnicamente definido fora da classe (ele pertence ao `std::ostream`), precisamos do `friend` para que ele tenha acesso aos atributos privados ou protegidos (veremos sobre isso melhor no próximo capítulo) do `Gato`.

### Prós e contras

A sobrecarga de operadores pode deixar o código muito mais expressivo, mas também pode ser usada de forma irresponsável. Antes de sobrecarregar um operador, vale se perguntar:

- **O comportamento é intuitivo?**
  
`v1 + v2` para vetores é óbvio. Mas `gato1 + gato2`, o que isso produziria? Um gatinho? Se o significado não for imediatamente claro, uma função com nome descritivo é melhor do que um operador.

- **O comportamento é consistente com a convenção?**
  
Se você define `==`, é esperado que você também defina `!=`. Se define `<`, espera-se `>`, `<=`, `>=`. Sobrecarregar apenas metade dos operadores relacionados pode gerar comportamentos confusos.

- **O código fica mais legível ou menos?**
  
Esse é o teste final. Se após a sobrecarga o `main` ficou mais fácil de ler e entender, ótimo. Se ficou mais difícil de rastrear o que está acontecendo, reconsidere.

## Exercícios

**1) Modelando uma Pokedex**

Crie uma classe Pokemon que represente um pokémon com os atributos nome (string), tipo (string) e nivel (int).

Requisitos:

- A classe deve ter três construtores diferentes usando lista de inicialização: um construtor padrão que inicialize nome como "Desconhecido", tipo como "Normal" e nivel como 1; um construtor que receba apenas nome e tipo, inicializando nivel como 1; um construtor completo que receba os três atributos.
- No Main.cpp, instancie um Pokemon com cada um dos três construtores e imprima os atributos de cada um no console. Confirme que o compilador está escolhendo o construtor correto em cada caso.


**2) Comparando Pokémons**

Expanda a classe Pokemon do exercício anterior com duas sobrecargas de operadores.

Requisitos:

- Sobrecarregue o operador > para comparar dois pokémons pelo nivel. A expressão p1 > p2 deve retornar true se p1 tiver nível maior que p2.
- Sobrecarregue o operador << para permitir imprimir um Pokemon diretamente com std::cout. O formato de saída deve ser: Charmander [Fogo] - Nível 12. Como os atributos da classe são públicos, o operador pode ser definido diretamente como uma função fora da classe, sem a necessidade de friend:
```cpp
std::ostream& operator<<(std::ostream& os, const Pokemon& p) {
    os << p.nome << " [" << p.tipo << "] - Nível " << p.nivel;
    return os;
}
```
- No Main.cpp, instancie dois pokémons com níveis diferentes, imprima ambos com std::cout e use o operador > para exibir no console qual dos dois está em nível mais alto.

## Conclusão

Chegamos ao fim do Capítulo 0! Saímos do conceito abstrato de "o que é orientação a objetos" e chegamos até mecanismos bem concretos do C++. 

Você agora sabe o que são classes e objetos, como inicializá-los de forma eficiente com construtores e listas de inicialização, e como tornar seus tipos mais expressivos com sobrecarga de operadores. 

No próximo capítulo, vamos nos aprofundar em encapsulamento e modificadores de acesso.

Te vejo lá!

