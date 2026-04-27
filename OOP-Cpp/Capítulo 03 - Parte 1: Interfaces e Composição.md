# Capítulo 03: Interfaces e Composição

No capítulo anterior, construímos um sistema de equipamentos usando herança e aprendemos o conceito de classes abstratas. Mas agora, uma nova pergunta surge: como modelar um **Personagem** que usa esses equipamentos?

A primeira ideia que pode vir à cabeça é: "é só herdar de `Espada`!". Mas espera um segundo... faz sentido dizer que um **Guerreiro** é *uma* Espada? Claramente, não. O Guerreiro **tem uma** Espada.

Essa distinção fundamental é o que vamos tratar neste capítulo:

- `"É um"` → **Herança**: Espada é um Equipamento. EspadaMagica é uma Espada.
- `"Tem um"` → **Composição**: Personagem tem uma Espada. Personagem tem uma Armadura.

Quando a relação é de **"tem um"**, o caminho certo é a **composição**: em vez de herdar de uma classe, você a inclui como **atributo**.

## 3.1 O que é uma interface

Vimos no capítulo anterior que uma classe com pelo menos um método virtual puro (`= 0`) é chamada de classe abstrata e não pode ser instanciada. 

Em linguagens como Java ou C#, existe uma palavra-chave específica chamada `interface`, que é basicamente uma classe abstrata "pura", sem nenhum atributo nem implementação, apenas um contrato de métodos

```java
// Java — só para referência, não é C++!
interface IEquipavel {
    void equipar();
    void desequipar();
}
```

O `C++` não tem essa palavra-chave. Mas já sabemos o suficiente para emular exatamente esse comportamento! Basta criar uma **classe abstrata onde todos os métodos são virtuais puros**:


Como já vimos anteriormente, uma classe abstrata é uma classe que possui pelo menos um método virtual puro, que é declarado com `= 0`:

```cpp
class IEquipavel {
public:
  virtual void equipar() = 0;  
  virtual void desequipar() = 0;
  virtual std::string getNome() const = 0;
  virtual ~IEquipavel() = default; // destrutor virtual
};
```

> Nota 1: A convenção de colocar um I na frente do nome (de Interface) é bastante comum. Não é obrigação do compilador, mas vai deixar o seu código bem mais legível para outros devs!
> Nota 2: O destrutor `virtual ~IEquipavel() = default` é essencial. Quando você deleta um objeto através de um ponteiro da interface, o C++ precisa saber qual destrutor chamar. Sem o `virtual`, ele chamaria sempre o destrutor da interface, e a memória do objeto filho nunca seria liberada corretamente. Sempre coloque um destrutor virtual nas suas interfaces!

Isso é uma **interface** no C++: uma classe que só define o contrato, sem nenhum atributo e sem nenhuma implementação. Quem herdar dela é obrigado a implementar todos esses métodos, ou também virará uma classe abstrata.

### Criando a interface e implementando

Vamos à prática! Vamos construir um sistema de equipamentos que o nosso Personagem possa usar.

Primeiro, a nossa interface `IEquipavel`:

```
IEquipavel.hpp
```
```cpp
#ifndef IEQUIPAVEL_HPP
#define IEQUIPAVEL_HPP

#include <string>

class IEquipavel {
public:
  virtual void equipar() = 0;
  virtual void desequipar() = 0;
  virtual std::string getNome() const = 0;
  virtual ~IEquipavel() = default;
};

#endif
```

Agora, vamos criar uma `Espada` que implementa esse 'contrato':

```
Espada.hpp
```
```cpp
#ifndef ESPADA_HPP
#define ESPADA_HPP

#include "IEquipavel.hpp"
#include <iostream>

class Espada : public IEquipavel {
private:
  std::string nome;
  int dano;

public:
  Espada(std::string nome, int dano) : nome(nome), dano(dano) {}

  void equipar() override {
    std::cout << nome << " foi equipada! (+" << dano << " de ataque)\n";
  }

  void desequipar() override {
    std::cout << nome << " foi desequipada.\n";
  }

  std::string getNome() override { return nome; }
  int getDano() { return dano; }
};

#endif
```

Vamos fazer o mesmo para `Armadura`:

```
Armadura.hpp
```
```cpp
#ifndef ARMADURA_HPP
#define ARMADURA_HPP

#include "IEquipavel.hpp"
#include <iostream>

class Armadura : public IEquipavel {
private:
  std::string nome;
  int defesa;

public:
  Armadura(std::string nome, int defesa) : nome(nome), defesa(defesa) {}

  void equipar() override {
    std::cout << nome << " foi equipada! (+" << defesa << " de defesa)\n";
  }

  void desequipar() override {
    std::cout << nome << " foi desequipada.\n";
  }

  std::string getNome() override { return nome; }
  int getDefesa() { return defesa; }
};

#endif
```

Agora, a peça central: o `Personagem` que usa esses equipamentos:

```
Personagem.hpp
```
```cpp
#ifndef PERSONAGEM_HPP
#define PERSONAGEM_HPP

#include "IEquipavel.hpp"
#include <iostream>
#include <vector>
#include <string>

class Personagem {
private:
  std::string nome;
  int vida;
  std::vector<IEquipavel*> inventario; // "tem uma" lista de equipamentos!

public:
  Personagem(std::string nome, int vida) : nome(nome), vida(vida) {}

  void equiparItem(IEquipavel* item) {
    inventario.push_back(item);
    item->equipar();
  }

  void listarInventario() {
    std::cout << "=== Inventário de " << nome << " ===" << std::endl;
    for (IEquipavel* item : inventario) {
      std::cout << "- " << item->getNome() << std::endl;
    }
  }
};

#endif
```

O `Personagem` tem um *vector* de ponteiros para `IEquipavel`. Ele não sabe se o que está dentro é uma `Espada`, uma `Armadura` ou qualquer outra coisa.

Ele só sabe que **tudo lá dentro honra o contrato de `IEquipavel`**. Isso é incrivelmente útil, e já vimos de onde vem esse poder: polimorfismo!

## 3.2 Vantagens de usar composição

Lembra da "miopia" do compilador que vimos no capítulo anterior? Quando chamávamos `->atacar()` por um ponteiro de `Personagem`, o C++ consultava o tipo real do objeto em tempo de execução para decidir qual versão do método executar.

A mesma coisa acontece aqui com `item->equipar()`. O `Personagem` não tem a menor ideia se o `IEquipavel*` aponta para uma `Espada` ou uma `Armadura`, o C++ descobre isso em **runtime** e chama o método certo.

Veja a seguir o main.cpp:

```
Main.cpp
```
```cpp
#include "Personagem.hpp"
#include "Espada.hpp"
#include "Armadura.hpp"

int main() {
  Personagem guerreiro("Meliodas", 500);

  Espada   espada("Lostvayne", 120);
  Armadura armadura("Armadura de Pégaso", 80);

  guerreiro.equiparItem(&espada);
  guerreiro.equiparItem(&armadura);

  std::cout << std::endl;
  guerreiro.listarInventario();

  return 0;
}
```

Saída desse código:

```
Lostvayne foi equipada! (+120 de ataque)
Armadura de Pégaso foi equipada! (+80 de defesa)

=== Inventário de Arthas ===
- Lostvayne
- Armadura de Pégaso
```

Perceba que o método `equiparItem` recebe um `IEquipavel*` e ponto final. Você pode criar 50 tipos novos de equipamento amanhã, e o código do `Personagem` **não muda uma linha sequer**.

### Problema do Diamante desaparece

Lembra de toda aquela solução com `virtual public` no capítulo anterior? Com composição, esse problema simplesmente não existe.

O Problema do Diamante surge quando duas classes herdam de uma mesma base e uma terceira herda das duas. Com composição, isso nunca acontece, porque o `Personagem` não herda de `Espada` nem de `Armadura`, ele apenas *possui* ponteiros para elas:

```
Herança (problemático):          Composição (tranquilo):

     Equipamento                      IEquipavel
      /       \                        /      \
  Espada    Armadura              Espada    Armadura
      \       /
     Personagem                   Personagem
          ↑                       [ tem → Espada*  ]
  Qual Equipamento usar?          [ tem → Armadura* ]
```

`Espada` e `Armadura` têm suas próprias hierarquias independentes. O `Personagem` não se mistura nisso, ele só usa o contrato.

### Acoplamento fraco

Com herança, o acoplamento é **forte**. Se você muda o construtor de uma classe base, todas as filhas e netas quebram junto, elas vivem coladas.

Com composição via interface, o acoplamento é **fraco**. O `Personagem` só conhece o contrato de `IEquipavel` — os três métodos que todo equipável deve ter. 

Você pode reescrever a `Espada` completamente por dentro, mudar seus atributos, refatorar a lógica, e o `Personagem` **não vai nem perceber**, desde que os métodos da interface continuem funcionando.

Isso torna o sistema muito mais fácil de manter!

### Modularidade e flexibilidade

Com composição, também é fácil combinar comportamentos sem criar hierarquias cada vez mais complexas. Imagine que você queira um item que seja tanto **equipável** quanto **vendável**. É só criar uma segunda interface:

```cpp
class IVendavel {
public:
  virtual int getPreco() = 0;
  virtual ~IVendavel() = default;
};
```

E a Espada pode implementar as duas:

```cpp
class Espada : public IEquipavel, public IVendavel {
  // ...
  int getPreco() override { return 350; }
};
```

## 3.3 Problemas com composição

Honestamente? Não há grandes problemas com composição. É por isso que ela é geralmente preferida à herança para modelar relacionamentos entre classes.

O único ponto de atenção real surge em projetos grandes: a **proliferação de interfaces**. Se o seu sistema crescer e você acabar com `IEquipavel`, `IVendavel`, `ICraftavel`, `ISalvavel`... pode se tornar difícil de gerenciar e lembrar qual classe implementa qual contrato.

A solução, na prática, é boa organização: agrupar as interfaces em um diretório `interfaces/` e seguir convenções de nomenclatura facilita muito a navegação.

Mas pense por um lado, ter muitas peças pequenas e bem definidas é um problema infinitamente melhor do que ter uma hierarquia de herança gigante e frágil!

## Exercícios

**1) Criando um novo contrato (Interface e Implementação)**

Crie uma nova interface chamada IUsavel, que represente qualquer item que possa ser usado (consumido) pelo personagem.

Requisitos:
- A interface deve ter dois métodos virtuais puros: void usar() — executa o efeito do item; std::string getDescricao() — retorna uma descrição do item.
- Crie uma classe Pocao que implemente IUsavel. A Pocao deve ter um atributo quantidadeDeVidaRecuperada (do tipo int) e um nome. Ao chamar usar(), imprima no console uma mensagem informando o nome da poção e a quantidade de vida recuperada.
- No Main.cpp, instancie uma Pocao e chame usar() nela diretamente.

**2) Expandindo o Personagem (Composição com múltiplas interfaces)**

Expanda a classe Personagem para que ela também gerencie itens usáveis, além dos equipáveis.

Requisitos:
- Adicione ao Personagem um std::vector<IUsavel*> chamado mochila.
- Implemente um método void adicionarNaMochila(IUsavel* item) que adiciona um item à mochila.
- Implemente um método void usarItem(int indice) que chama usar() no item do índice correspondente no vetor.
- No Main.cpp, crie um Personagem, adicione algumas poções à mochila e use-as pelo índice.

## Conclusão

Com composição e interfaces, o sistema de RPG ficou muito mais robusto, flexível e fácil de expandir. 

Aprendemos a distinguir relações de "é um" e "tem um", a emular interfaces com classes abstratas em C++ e a enxergar, na prática, as vantagens do acoplamento fraco aliado ao polimorfismo. 

Na próxima aula veremos mais sobre a relação entre herança e composição, te esperamos lá :)
