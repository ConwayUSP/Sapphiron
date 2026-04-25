# Capítulo 02: Herança e Polimorfismo

## 2.2 Polimorfismo

## 2.3 Como sobrescrever métodos

Quando uma classe herda de outra, ela recebe todos os seus atributos e métodos. Mas e quando o comportamento herdado não é adequado para a classe filha? É aí que entram os **métodos virtuais**.

Imagine um RPG com diferentes classes de personagens: todos podem atacar, mas a forma de executar o ataque varia completamente. Um Guerreiro desfere golpes físicos com sua espada, um Mago lança feitiços consumindo mana, e um Arqueiro dispara flechas à distância.

Se simplesmente herdarmos um método `atacar()` sem nenhum mecanismo especial, todos os personagens executarão o mesmo ataque, o que claramente não faria sentido para um jogo desse tipo.

Para lidar com situações como essa, utilizamos a **sobrescrita de métodos** (_override_). Ela consiste em redefinir, na classe derivada, uma função que já foi declarada na classe base, permitindo que cada herói execute sua ação de forma única.

Para que o C++ entenda que um método pode ser substituído e, mais importante, para que o polimorfismo funcione, precisamos de duas palavras-chave:

- `virtual`: Declarada na classe base, ela avisa ao compilador: *"Ei, este método pode ser transformado nas classes filhas. Decida qual versão usar apenas durante a execução (*runtime*)".*

- `override`: Usada na classe filha, ela indica explicitamente que você está sobrescrevendo um método da base. Isso ajuda o compilador a te avisar caso você erre o nome ou a assinatura do método.

Imagine que você criou suas classes de RPG. O Guerreiro e o Mago herdam de Personagem. Você quer que o jogo seja capaz de lidar com qualquer herói de forma genérica, usando um ponteiro de Personagem para comandar os ataques.

No código abaixo, não usaremos métodos virtuais. Veja o que acontece:

```cpp
#include <iostream>
#include <string>

class Personagem {
protected:
    std::string nome;
public:
    Personagem(std::string n) : nome(n) {}

    // SEM VIRTUAL: O método está "preso" à classe Personagem
    void atacar() {
        std::cout << nome << " realiza um ataque basico!\n";
    }
};

class Guerreiro : public Personagem {
public:
    Guerreiro(std::string n) : Personagem(n) {}

    // Tentamos redefinir o ataque, mas sem o mecanismo de sobrescrita oficial
    void atacar() {
        std::cout << nome << " golpeia com a espada! [35 de dano]\n";
    }
};

class Mago : public Personagem {
public:
    Mago(std::string n) : Personagem(n) {}

    void atacar() {
        std::cout << nome << " conjura Bola de Fogo! [75 de dano]\n";
    }
};

int main() {
    Guerreiro gladiador("Coutinho");
    Mago mestre("Digi");

    // Criamos ponteiros do tipo Personagem (a classe base)
    Personagem* p1 = &gladiador;
    Personagem* p2 = &mestre;

    p1->atacar();
    p2->atacar();

    return 0;
}
```

Saída desse código:

```
Coutinho realiza um ataque basico generico!
Digi realiza um ataque basico generico!
```

Repare que, mesmo `p1` apontando para um **Guerreiro**, ele executou o ataque básico da classe pai. Isso acontece porque, sem a palavra-chave `virtual`, o C++ decide qual função chamar baseado no tipo do ponteiro (`Personagem*`) e não no tipo do objeto real que está lá dentro.

Basicamente, o compilador olha para o ponteiro e pensa: "Se é um ponteiro de Personagem, eu vou chamar o atacar() de Personagem". É para resolver essa "miopia" do compilador que usamos a sobrescrita com **métodos virtuais**.

No código abaixo, observe como tratamos diferentes heróis através de um ponteiro da classe base, mas cada um executa seu próprio comportamento único:

```cpp
#include <iostream>
#include <string>

// Classe Base
class Personagem {
protected:
    std::string nome;

public:
    Personagem(std::string n) : nome(n) {}

    // O 'virtual' avisa que este método pode ser sobrescrito
    virtual void atacar() {
        std::cout << nome << " realiza um ataque basico!\n";
    }

    virtual ~Personagem() {}
};

// Classes Derivadas
class Guerreiro : public Personagem {
public:
    Guerreiro(std::string n) : Personagem(n) {}

    // 'override' indica que estamos redefinindo o comportamento da base
    void atacar() override {
        std::cout << nome << " golpeia com a espada! [35 de dano]\n";
    }
};

class Mago : public Personagem {
public:
    Mago(std::string n) : Personagem(n) {}

    // usamos 'override' novamente
    void atacar() override {
        std::cout << nome << " conjura Bola de Fogo! [75 de dano]\n";
    }
};

int main() {
    // Criando os objetos
    Guerreiro gladiador("Coutinho");
    Mago mestre("Digi");

    // Usando ponteiros da classe base para demonstrar a sobrescrita em ação
    Personagem* p1 = &gladiador;
    Personagem* p2 = &mestre;

    p1->atacar(); // Chama a versao do Guerreiro
    p2->atacar(); // Chama a versao do Mago

    return 0;
}
```

Saída desse código:

```
Coutinho golpeia com a espada! [35 de dano]
Digi conjura Bola de Fogo! [75 de dano]
```

Agora sim! Ao adicionar `virtual` na classe base e `override` nas filhas, o C++ passou a consultar o tipo real do objeto em tempo de execução, e cada herói executou exatamente o ataque que lhe pertence.

Isso é a ligação dinâmica em ação: o compilador, que antes era "míope" e só enxergava o tipo do ponteiro, agora sabe que precisa verificar quem está realmente do outro lado antes de chamar o método.

## 2.4 Classes abstratas

No exemplo anterior, vimos que o método `atacar()` da classe `Personagem` existe apenas como um "ataque básico genérico", uma versão que, na prática, nunca deveria ser chamada diretamente.

Afinal, em nenhum momento do jogo o jogador controla um `Personagem puro`, pois, ele sempre será um Guerreiro, um Mago, ou qualquer outra classe específica.

Isso nos leva a uma pergunta natural: **faz sentido instanciar um `Personagem` diretamente?**

Na maioria dos jogos, não. `Personagem` existe apenas como um molde, uma base comum para organizar o que todos os heróis compartilham. É exatamente para modelar essa ideia que o C++ oferece as **classes abstratas**.

Uma classe abstrata é uma classe que **não pode ser instanciada diretamente**. Ela existe para ser herdada, servindo como um contrato: toda classe que herdar dela é obrigada a implementar determinados métodos.

Esse contrato é definido por meio de métodos puramente virtuais, declarados com `= 0` no final:

```cpp
virtual void atacar() = 0;
```

Ao declarar um método assim, você está dizendo: _"Toda classe filha concreta deve implementar esse comportamento. Eu, classe base, não tenho uma versão padrão para oferecer."_

Vamos seguir retomando o exemplo anterior, mas, dessa vez, usando **classe abstrata**:

```cpp
#include <iostream>
#include <string>

class Personagem {
protected:
    std::string nome;
public:
    Personagem(std::string n) : nome(n) {}

    // Método puramente virtual: sem implementação aqui!
    // Cada classe filha OBRIGATORIAMENTE deve implementar sua versão
    virtual void atacar() = 0;

};

class Guerreiro : public Personagem {
public:
    Guerreiro(std::string n) : Personagem(n) {}

    void atacar() override {
        std::cout << nome << " golpeia com a espada! [35 de dano]\n";
    }
};

class Mago : public Personagem {
public:
    Mago(std::string n) : Personagem(n) {}

    void atacar() override {
        std::cout << nome << " conjura Bola de Fogo! [75 de dano]\n";
    }
};

int main() {
    // Personagem p("xxx"); // ERRO de compilação! Não é possível instanciar

    Guerreiro gladiador("Coutinho");
    Mago mestre("Digi");

    gladiador.atacar();
    mestre.atacar();
}
```

### O que acontece se a classe filha não implementar o método?

Se uma classe herdar de `Personagem` sem implementar `atacar()`, ela também se tornará abstrata, e da mesma forma não poderá ser instanciada:

```cpp
// Arqueiro herda de Personagem, mas esqueceu de implementar atacar()
class Arqueiro : public Personagem {
public:
    Arqueiro(std::string n) : Personagem(n) {}

    // atacar() não foi implementado!
};

int main() {
    Arqueiro sniper("Auil"); // ERRO: Arqueiro também é abstrata!
}
```

O compilador não deixará o código sequer compilar. É uma proteção em tempo de compilação que evita que um herói "incompleto" entre em jogo.
