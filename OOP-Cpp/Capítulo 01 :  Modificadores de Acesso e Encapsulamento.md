# Capítulo 01: Encapsulamento

## 1.1 Modificadores de acesso

No que tange aos modificadores de acesso, certamente você já se deparou com algumas declarações adicionais em **atributos** e **métodos** em um código orientado a objetos. Estamos falando aqui daquelas palavras que aparecem antes dos tipos e dos nomes. Eles são os benditos **Modificadores de Acesso** e são bem importantes, diga-se de passagem.

O nome passa uma boa intuição a respeito do que seria isso. É um estabelecimento a respeito do quão acessível um bloco de código será para o restante da estrutura de um projeto. Eles controlam a visibilidade dos membros de uma classe.

No caso, em **C++**, temos três tipos diferentes:

### Public

Especificadores públicos fazem com que **atributos** e **métodos** sejam acessíveis em **toda a parte**. Ou seja, aqui não tem restrição nenhuma. Qualquer classe, função, em qualquer arquivo do projeto, pode acessar um atributo/método público de alguma classe em específico.

A **sintaxe** funciona da seguinte maneira:

```cpp
#include <iostream>
#include <string>

class Banda {
public:
    std::string nome;
};
```

Ou seja, digitamos `"public:"` na linha acima dos atributos/métodos que desejamos tornar públicos. Diferente de outras linguagens como `Java`, no `C++` precisamos declarar apenas uma vez e agrupá-los. Por exemplo:

```cpp
class Banda {
public:
  std::string nome;
  int anoFundacao;
  std::vector<std::string> listaDeMusicas;

  void imprimeListaDeMusicas(std::vector<std::string> listaDeMusicas) {
    for (int i = 0; i < listaDeMusicas.size(); i++) {
      std::cout << listaDeMusicas[i] << std::endl;
    }
  }
};
```

E se quiséssemos colocar a nossa classe em um arquivo separado e utilizar ela diretamente na `main.cpp`? O uso do `public` possibilita isso.

Vamos criar um arquivo separado, chamado `Bandas.cpp` e colocar o nosso código acima nele (e aproveitar para adicionar um construtor para nossa classe):

```
Bandas.cpp
```

```cpp
#include <iostream>
#include <string>
#include <vector>

class Banda {
public:
  std::string nome;
  int anoFundacao;
  std::vector<std::string> listaDeMusicas;

  Banda(std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas) {
    this->nome = nome;
    this->anoFundacao = anoFundacao;
    this->listaDeMusicas = listaDeMusicas;
  }

  void imprimeListaDeMusicas() {
    for (int i = 0; i < listaDeMusicas.size(); i++) {
      std::cout << listaDeMusicas[i] << std::endl;
    }
  }
};
```

Agora, vamos incluir ele no nosso `Main.cpp` e brincar com a programação. Lembrando que, em projetos reais, separamos em arquivos de cabeçalho (.h ou .hpp). Mas, para fins didáticos, faremos o include direto:

```
Main.cpp
```

```cpp
#include "Bandas.cpp"
#include <iostream>
#include <string>
#include <vector>

int main() {
  Banda RATM("Rage Against The Machine", 1995,
             {"Bombtrack", "Killing in the Name", "Take the Power Back"});

  RATM.imprimeListaDeMusicas();

  return 0;
}
```

O compilador, em nenhum momento, reclamará do que fizemos aqui (pelo menos, é o que esperamos).

### Protected

Aqui, começamos a colocar algumas restrições nas coisas. Diferentemente do `public`, o `protected` faz com que métodos/atributos sejam **acessíveis apenas na própria classe e nas suas classes derivadas**. O que são classes derivadas? Já já vamos mostrar!

Na nossa classe `Banda`, vamos colocar um novo atributo, só que dessa vez ele não será público:

```
Bandas.cpp
```

```cpp
class Banda {
protected:
  std::string genero;

public:
  std::string nome;
  int anoFundacao;
  std::vector<std::string> listaDeMusicas;

  Banda(std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas, std::string genero) {
    this->nome = nome;
    this->anoFundacao = anoFundacao;
    this->listaDeMusicas = listaDeMusicas;
    this->genero = genero;
  }

  void imprimeListaDeMusicas() {
    for (int i = 0; i < listaDeMusicas.size(); i++) {
      std::cout << listaDeMusicas[i] << std::endl;
    }
  }
};
```

Colocamos um atributo protegido chamado de `genero`. Agora, vamos adicionar uma classe derivada:

```
Bandas.cpp
```

```cpp
//Classe derivada
class Metal : public Banda {
public:
  Metal(std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas)
      : Banda(nome, anoFundacao, listaDeMusicas, "METAL") {}

  void info() {
    std::cout << this->nome << std::endl;
    std::cout << this->anoFundacao << std::endl;
    std::cout << this->genero << std::endl;
  }
};
```

Explicaremos herança de um jeito melhor no próximo capítulo do curso. Mas, basicamente, criamos uma classe "filha" da classe "Banda". Ela herda as mesmas características da classe pai e, além disso, podemos adicionar novos atributos/métodos exclusivos da classe nova. Isso foi feito com a criação do método `info()` definido acima.

Além disso, aquelas linhas meio esquisitas logo depois de `public` dizem respeito ao construtor da nossa classe, que é único e será baseado no original. De maneira simples, elas dizem mais ou menos: "Esta nova classe aceitará esses atributos aqui e eles vão no construtor da classe pai, no qual eu me baseio, da seguinte maneira".

Prometemos que isso vai ser melhor explicado depois. Mas, achamos que, por ora, deu para entender.

Enfim. Se tentarmos acessar o atributo `genero` diretamente na `main()`, que está em um arquivo separado, teremos um problema na compilação. Fique livre para testar.

Porém, podemos realizar esse acesso através da nova classe, tendo em vista que ela tem uma função que imprime essa informação na tela.

Por exemplo:

```cpp
#include "Bandas.cpp"
#include <iostream>
#include <string>
#include <vector>

int main() {
  Metal RATM("Rage Against The Machine", 1995,
             {"Bombtrack", "Killing in the Name", "Take the Power Back"},
             "METAL");

  RATM.imprimeListaDeMusicas();
  RATM.info();

  return 0;
}
```

Compilando tudo, teremos a seguinte saída:

```
Bombtrack
Killing in the Name
Take the Power Back
Rage Against The Machine
1995
METAL
```

Utilizamos como exemplo um atributo, mas o mesmo vale para métodos. Fique livre para testar na sua máquina!

### Private

O especificador `private` faz com que atributos/métodos da classe sejam acessíveis apenas dentro da própria classe em si. Frequentemente, isso é utilizado para ocultar dados da parte externa.

Inclusive, todos os membros de uma classe são privados caso nenhum especificador seja mencionado.

Vamos adicionar um atributo privado na nossa classe `Banda`:

```
Banda.cpp
```

```cpp
class Banda {
private:
  std::string id;

protected:
  std::string genero;

public:
  std::string nome;
  int anoFundacao;
  std::vector<std::string> listaDeMusicas;

  Banda(std::string id, std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas, std::string genero) {
    this->id = id;
    this->nome = nome;
    this->anoFundacao = anoFundacao;
    this->listaDeMusicas = listaDeMusicas;
    this->genero = genero;
  }

  void imprimeListaDeMusicas() {
    for (int i = 0; i < listaDeMusicas.size(); i++) {
      std::cout << listaDeMusicas[i] << std::endl;
    }
  }
};
```

Do jeito que o código está, o que acontece se tentarmos acessar `id` na nossa classe filha?

```cpp
class Metal : public Banda {
public:
  Metal(std::string id, std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas)
      : Banda(id, nome, anoFundacao, listaDeMusicas, "METAL") {}

  void info() {
    std::cout << this->nome << std::endl;
    std::cout << this->anoFundacao << std::endl;
    std::cout << this->genero << std::endl;
    std::cout << this->id << std::endl; // Colocamos para printar aqui
  }
};
```

Teremos o seguinte erro de compilação:

```
Bandas.cpp: In member function ‘void Metal::info()’:
Bandas.cpp:43:24: error: ‘std::string Banda::id’ is private within this context
   43 |     std::cout << this->id << std::endl;
      |                        ^~
Bandas.cpp:7:15: note: declared private here
    7 |   std::string id;
      |               ^~
Bandas.cpp: In member function ‘void Metal::info()’:
Bandas.cpp:43:24: error: ‘std::string Banda::id’ is private within this context
   43 |     std::cout << this->id << std::endl;
      |                        ^~
Bandas.cpp:7:15: note: declared private here
    7 |   std::string id;
      |               ^~
```

Ou seja, inacessível para o exterior.

### Resumo de Visibilidade

A tabela abaixo resume como cada modificador de acesso se comporta no C++:

| Modificador     | Própria Classe | Classe Filha (Herança) | Resto do Projeto |
| :-------------- | :------------: | :--------------------: | :--------------: |
| **`public`**    |       ✅       |           ✅           |        ✅        |
| **`protected`** |       ✅       |           ✅           |        ❌        |
| **`private`**   |       ✅       |           ❌           |        ❌        |

O uso do modificador de acesso `private` melhora bastante o encapsulamento de informações.

Pera, **encapsulamento**???

## 1.2 O que é encapsulamento?

No contexto de orientação a objetos, é basicamente unir os dados (atributos) e os métodos que manipulam esses dados em uma classe e restringir o acesso direto aos detalhes internos do objeto. O estado interno do objeto estará protegido.

O mundo exterior não sabe e, na verdade, não precisa saber dos pormenores a respeito de como aquela classe faz o que faz, apenas o que ele faz através da sua [API](https://pt.wikipedia.org/wiki/Interface_de_programa%C3%A7%C3%A3o_de_aplica%C3%A7%C3%B5es) pública.

Por isso, a analogia com cápsulas!

Mas, como, então, acessar esses atributos privados, por exemplo?

## 1.3 Getters e Setters

Para isso, vamos construir a nossa própria API através de `Getters` e `Setters`! Ambos serão métodos bem simples, porém muito eficientes na hora de encapsular a nossa classe.

Os nomes são intuitivos:

(i) Getters: servem para que possamos recuperar o conteúdo de um atributo em específico;

(ii) Setters: servem para que possamos redefinir o conteúdo de um atributo em específico.

Cada atributo terá um `Getter/Setter` próprio. Isso é uma boa prática e um passo fundamental para um bom encapsulamento: deixe tudo privado, a não ser que haja um motivo específico para expor!

**Exemplo: Adapte o código anterior para uma versão encapsulada, com getters e setters**

Então, pelo que programamos anteriormente, precisamos mudar a maioria dos atributos para `private`. Então:

```cpp
class Banda {
private:
  std::string id;
  std::string genero;
  std::string nome;
  int anoFundacao;
  std::vector<std::string> listaDeMusicas;
};
```

Agora, como mencionamos anteriormente, para criar uma boa API, vamos definir `Getters/Setters` públicos! Isso aqui vai adicionar uma boa quantidade de linhas, mas a ideia é bem simples:

(i) Os métodos `Getters` retornam os valores guardados. Literalmente, é apenas um return com o tipo do atributo:

```cpp
class Banda {
private:
  std::string id;
  std::string genero;
  std::string nome;
  int anoFundacao;
  std::vector<std::string> listaDeMusicas;

public:
  Banda(std::string id, std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas, std::string genero) {
    this->id = id;
    this->nome = nome;
    this->anoFundacao = anoFundacao;
    this->listaDeMusicas = listaDeMusicas;
    this->genero = genero;
  }

  // Getters
  std::string getId() { return id; }

  std::string getGenero() { return genero; }

  std::string getNome() { return nome; }

  int getAnoFundacao() { return anoFundacao; }

  std::vector<std::string> getListaDeMusicas() { return listaDeMusicas; }

};
```

(ii) Os métodos `Setters` atribuirão valores aos nossos atributos privados. Literalmente, é apenas uma atribuição em cada método:

```cpp
class Banda {
private:
  std::string id;
  std::string genero;
  std::string nome;
  int anoFundacao;
  std::vector<std::string> listaDeMusicas;

public:
  Banda(std::string id, std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas, std::string genero) {
    this->id = id;
    this->nome = nome;
    this->anoFundacao = anoFundacao;
    this->listaDeMusicas = listaDeMusicas;
    this->genero = genero;
  }

  // Getters
  std::string getId() { return id; }

  std::string getGenero() { return genero; }

  std::string getNome() { return nome; }

  int getAnoFundacao() { return anoFundacao; }

  std::vector<std::string> getListaDeMusicas() { return listaDeMusicas; }

  //Setters
  void setId(std::string id) { this->id = id; }

  void setGenero(std::string genero) { this->genero = genero; }

  void setNome(std::string nome) { this->nome = nome; }

  void setAnoFundacao(int anoFundacao) { this->anoFundacao = anoFundacao; }

  void setListaDeMusicas(std::vector<std::string> listaDeMusicas) {
    this->listaDeMusicas = listaDeMusicas;
  }

  // Método antigo
  void imprimeListaDeMusicas() {
    for (int i = 0; i < listaDeMusicas.size(); i++) {
      std::cout << listaDeMusicas[i] << std::endl;
    }
  }
};
```

Aproveitamos e já colocamos o método antigo também.

E, voilá! Temos a nossa API bonitinha e funcional! Agora, sempre que quisermos alterar alguma coisa dos atributos, basta utilizar os nossos métodos novos.

Para finalizar, vamos refatorar a nossa classe filha:

```cpp
class Metal : public Banda {
public:
  Metal(std::string id, std::string nome, int anoFundacao,
        std::vector<std::string> listaDeMusicas)
      : Banda(id, nome, anoFundacao, listaDeMusicas, "METAL") {}

  void info() {
    std::cout << this->getNome() << std::endl;
    std::cout << this->getAnoFundacao() << std::endl;
    std::cout << this->getGenero() << std::endl;
    std::cout << this->getId() << std::endl;
  }
}
```

Pode parecer um pouco bobo, caso você esteja no seu primeiro contato com as ideias aqui apresentadas, mas a utilização dessa API e do encapsulamento é de suma importância para a construção de códigos mais robustos, escaláveis e limpos.

Setters podem restringir, filtrar e moldar a modificação de atributos, enquanto Getters podem também restringir ou formatar os dados requisitados pelo usuário.

Um bom projeto orientado a objetos leva esses pontos em consideração!

## Exercícios

### 1) O Rig do Baixista

Tarefa: Crie uma classe chamada Instrumento.

Transforme os atributos modelo, marca e volume em privados

Crie um construtor que inicialize esses valores

Implemente Getters e Setters para todos os atributos

Regra de Negócio (Setter): No método setVolume(int v), garanta que o volume nunca seja menor que 0 nem maior que 11 (afinal, rockeiros vão até o 11). Se o valor for fora desse intervalo, ignore a alteração ou ajuste para o limite.

### 2) Refatoração de Acesso

Observe o código abaixo que apresenta erros de visibilidade:

```cpp
class Pedal {
    std::string nome;
public:
    Pedal(std::string n) : nome(n) {}
};

int main() {
    Pedal overdrive("TS9");
    overdrive.nome = "Big Muff";
    return 0;
}
```

Tarefa: Refatore a classe Pedal utilizando os conceitos de Getters e Setters para que o nome do pedal possa ser alterado e lido na função main() sem que o atributo precise ser público.

## Conclusões

Aqui, na primeira parte, você aprendeu a respeito de modificadores de acesso e, logo em seguida, encapsulamento e API com getters/setters. Isso é de grande importância para suas habilidades de programação e é um dos pilares da linguagem `C++`.

No próximo capítulo, começaremos falando de herança. Não deixe de conferir!
