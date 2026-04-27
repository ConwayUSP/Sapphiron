# Capítulo 02: Herança e Polimorfismo

## 2.2 Polimorfismo

"**Polimorfismo**" vem do grego e tem como significado "muitas formas" (poli = muitas, morphos = formas).

Ele ocorre quando diversas classes relacionam entre si através da **Herança**.

Na parte anterior, nos debruçamos a respeito de como utilizar esse mecanismo para que classes filhas possam herdar atributos e métodos das mães pode ser útil. O **Polimorfismo**, por sua vez, funciona de maneira diferente: ele permite com que possamos usar esses métodos para realizar tarefas diferentes.

O exemplo mais clássico quando estamos tratando deste ponto é a programação de entidades que se baseiam no mesmo "molde" (classe), mas possuem um jeito específico de se comportar.

Imagine uma classe `Canino`, que possui um método `acao()`. Todas as classes derivadas poderiam realizar ações diferentes, ainda mais se estamos projetando algo totalmente baseado na mais pura realidade. Veja:

```cpp
//Classe base
class Canino{
public:
    void acao(){
        std::cout << "Realiza ação" << std::endl;
    }
};

class Raposa : public Canino{
public:
    void acao(){
        std::cout << "A Raposa ROUBA" << std::endl;
    }
};

class Cachorro : public Canino{
public:
    void acao(){
        std::cout << "O Cachorro TOMA" << std::endl;
    }
};

class Lobo : public Canino{
public:
    void acao(){
        std::cout << "O Lobo PEDE" << std::endl;
    }
};
```

Agora, podemos instanciar objetos de `Raposa`, `Cachorro` e `Lobo`:

```cpp
int main() {
    Canino canino;
    Raposa kyubi;
    Cachorro isnupi;
    Lobo pidao;

    canino.acao();
    kyubi.acao();
    isnupi.acao();
    pidao.acao();
    return 0;
}
```

Compilando e rodando, teremos:

```
Realiza ação
A Raposa ROUBA
O Cachorro TOMA
O Lobo PEDE
```

<img src="../OOP-Cpp/imagens/01_lobopidao.jpg" width=300>

Bacana, né? Porém...

Como os métodos não têm `virtual`, o que está acontecendo acima não é polimorfismo, é apenas ocultação de nomes (Name Hiding ou Shadowing). O polimorfismo de verdade no C++ só acontece quando usamos Ponteiros ou Referências da classe base apontando para a classe filha em conjunto com a palavra `virtual`.

Vamos aplicar o polimorfismo real a partir de agora.

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
        std::cout << nome << " realiza um ataque basico generico!\n";
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
        std::cout << nome << " realiza um ataque basico generico!\n";
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

    virtual ~Personagem() {}
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

## 2.5 Vantagens/Desvantagens de Usar Herança/Polimorfismo

Ok! Avançamos e concluímos a teoria a respeito de `Herança` e `Polimorfismo`. Porém, nem tudo são flores, não é mesmo? Não podemos assumir que existem apenas vantagens na utilização dessas ferramentas, mesmo que elas sejam muito **poderosas**.

Nós já mencionamos algumas coisas no decorrer desses capítulos, mas decidimos compilar o principal aqui, para que fiquem mais centralizadas.

Novamente, a herança é uma das ferramentas mais poderosas da Orientação a Objetos, mas costuma ser descrita pelos engenheiros de software como uma "faca de dois gumes" (ou "dois legumes", para os mais íntimos). Ela resolve muitos problemas de **redundância**, mas cobra um **preço estrutural** se for usada no contexto errado.

### Vantagens

**(i) Reaproveitamento de Código (DRY - Don't Repeat Yourself):** Esta é a vantagem mais imediata. Você escreve a lógica comum apenas uma vez na classe base (como os atributos de Equipamento) e todas as classes derivadas herdam esse código gratuitamente, reduzindo a duplicação e o tamanho do arquivo.

**(ii) Manutenção Centralizada:** Se você descobrir um bug na forma como o ataque básico é calculado, basta corrigir o método na classe mãe. Todas as dezenas de classes filhas herdarão a correção automaticamente, sem que você precise alterar arquivo por arquivo.

**(iii) Modelagem Intuitiva (Relação "É um"):** A herança permite traduzir taxonomias do mundo real diretamente para o código de forma lógica. Um Guerreiro **é um** Personagem, um Cachorro **é um** Canino.

### Desvantagens

**(i) Acoplamento Forte (O maior vilão):** A herança cria a relação mais forte e rígida que existe entre duas classes. Uma classe filha é intimamente dependente da implementação da classe mãe. Qualquer alteração na classe base (mudar um construtor, adicionar um parâmetro) pode "quebrar" todas as classes derivadas de uma só vez.

**(ii) Inflexibilidade e Árvores Profundas:** Se você começar a criar hierarquias muito profundas (ex: Personagem -> Heroi -> Guerreiro -> Paladino -> PaladinoSagrado), o código se torna um pesadelo de refatoração. O comportamento fica fragmentado por vários arquivos e entender o que a classe faz exige ler cinco níveis de herança.

**(iii) O Problema do Diamante:** Especificamente em linguagens que suportam herança múltipla (como o C++), herdar de múltiplas classes pode gerar ambiguidades de memória e de métodos, exigindo o uso de sintaxes mais complexas, como a herança virtual.

### Resumo

Use a Herança quando você tiver certeza absoluta de que a relação é estritamente **"É UM"** e quando o polimorfismo for necessário para o sistema. Caso a relação seja **"TEM UM"** ou **"USA UM"**, o caminho mais moderno, seguro e escalável é sempre a `Composição`.

## Conclusões

Agora, você tem noção de como funciona mais um dos pilares da **orientação a objetos**! Aproveitando o gancho que deixamos logo acima, falaremos de `Interfaces e Composição` no próximo capítulo! Não deixe de conferir!

Te vejo lá!
