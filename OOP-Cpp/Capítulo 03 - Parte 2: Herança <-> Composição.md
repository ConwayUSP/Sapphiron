# Capítulo 03: Interfaces e Composição

## 3.4 "Prefira composição sobre herança"

Essa é uma das máximas mais famosas do desenvolvimento de software orientado a objetos. Mas, como toda regra, ela faz muito mais sentido quando você vê o problema que ela resolve na prática.

Vamos a um exemplo concreto. Continuando no nosso RPG: queremos modelar diferentes tipos de personagens que podem se **mover** pelo mapa. Alguns andam, outros voam, outros nadam. Parece simples, certo?

### A solução com herança

A primeira tentativa natural seria criar uma hierarquia:

```cpp
// Solução com herança

class Personagem {
protected:
    std::string nome;
public:
    Personagem(std::string n) : nome(n) {}
    virtual void mover() = 0;
    virtual void atacar() = 0;
};

class PersonagemTerrestre : public Personagem {
public:
    PersonagemTerrestre(std::string n) : Personagem(n) {}
    void mover() override {
        std::cout << nome << " caminha pelo terreno.\n";
    }
};

class PersonagemVoador : public Personagem {
public:
    PersonagemVoador(std::string n) : Personagem(n) {}
    void mover() override {
        std::cout << nome << " voa pelos céus.\n";
    }
};

class Guerreiro : public PersonagemTerrestre {
public:
    Guerreiro(std::string n) : PersonagemTerrestre(n) {}
    void atacar() override {
        std::cout << nome << " golpeia com a espada!\n";
    }
};

class Dragao : public PersonagemVoador {
public:
    Dragao(std::string n) : PersonagemVoador(n) {}
    void atacar() override {
        std::cout << nome << " cospe fogo!\n";
    }
};
```

Ok, funcionou! Mas agora o designer do jogo chega com uma novidade: *"precisamos de um Guerreiro Alado. Ele anda E voa".*

E aí começa o problema. Como `Guerreiro` já herda de `PersonagemTerrestre`, e `PersonagemVoador` é uma hierarquia separada... Voltamos ao Problema do Diamante. Poderíamos usar `virtual public` de novo, mas a hierarquia já começa a ficar torta.

E não para por aí. Uma semana depois: *"agora precisamos de um Guerreiro Anfíbio, que anda e nada"*. Depois: *"um Dragão que também nada"*. A cada nova combinação de comportamentos, precisamos criar uma nova classe na hierarquia, gerando uma **explosão de classes**.

A raiz do problema é que estamos usando herança para modelar um comportamento, a forma de se mover, e não uma relação de identidade. Um Guerreiro não é um Terrestre. Ele apenas se *move* de uma certa forma.

### A solução com composição

Com composição, separamos o comportamento de movimento em uma interface e deixamos cada personagem ter a implementação que fizer sentido para ele:

```
IMovimento.hpp
```
```cpp
class IMovimento {
public:
    virtual void mover(const std::string& nome) = 0;
    virtual ~IMovimento() = default;
};

// Implementações concretas do comportamento

class MovimentoTerrestre : public IMovimento {
public:
    void mover(const std::string& nome) override {
        std::cout << nome << " caminha pelo terreno.\n";
    }
};

class MovimentoVoo : public IMovimento {
public:
    void mover(const std::string& nome) override {
        std::cout << nome << " voa pelos céus.\n";
    }
};

class MovimentoNatacao : public IMovimento {
public:
    void mover(const std::string& nome) override {
        std::cout << nome << " nada pelas águas.\n";
    }
};
```

Agora, a classe `Personagem` tem um `IMovimento`, em vez de herdar um:

```
Personagem.hpp
```
```cpp
class Personagem {
protected:
    std::string nome;
    IMovimento* movimento; // composição!

public:
    Personagem(std::string n, IMovimento* mov)
        : nome(n), movimento(mov) {}

    void mover() {
        movimento->mover(nome);
    }

    virtual void atacar() = 0;
    virtual ~Personagem() = default;
};

class Guerreiro : public Personagem {
public:
    Guerreiro(std::string n, IMovimento* mov)
        : Personagem(n, mov) {}

    void atacar() override {
        std::cout << nome << " golpeia com o punho!\n";
    }
};

class Dragao : public Personagem {
public:
    Dragao(std::string n, IMovimento* mov)
        : Personagem(n, mov) {}

    void atacar() override {
        std::cout << nome << " cospe fogo!\n";
    }
};
```

E no `Main.cpp`, a combinação de comportamentos vira trivial:
```
Main.cpp
```
```cpp
// Main.cpp

int main() {
    MovimentoTerrestre aPe;
    MovimentoVoo       voando;
    MovimentoNatacao   nadando;

    Guerreiro guerreiro("Saitama", &aPe);
    Dragao    dragao("Shenlong", &voando);

    // Um Guerreiro Alado? Só trocar o movimento!
    Guerreiro guerreiroAlado("Meliodas", &voando);

    // Um Dragão Anfíbio? Igualzinho.
    Dragao dragaoAnfibio("Tambor Trovão", &nadando);

    guerreiro.mover();
    guerreiro.atacar();

    std::cout << std::endl;

    dragao.mover();
    dragao.atacar();

    std::cout << std::endl;

    guerreiroAlado.mover();
    guerreiroAlado.atacar();

    return 0;
}
```

Saída esperada do código acima:
```
Saitama caminha pelo terreno.
Saitama golpeia com o punho!

Shenlong voa pelos céus.
Shenlong cospe fogo!

Meliodas voa pelos céus.
Meliodas golpeia com o punho!
```

Perceba o que resolvemos:

- **(i) Sem explosão de classes**: `Guerreiro` e `Dragao` continuam sendo as únicas classes de personagem. Qualquer combinação de movimento é feita na hora de instanciar o objeto, sem criar uma nova classe para cada caso.
- **(ii) Sem Problema do Diamante**: Nenhuma herança múltipla complicada. Cada coisa na sua responsabilidade.
- **(iii) Comportamento trocável em runtime**: Se o jogo precisar que um personagem aprenda a voar no meio da partida, basta trocar o ponteiro `movimento`. Com herança, isso seria impossível sem recriar o objeto do zero.

É por situações assim que, ao considerar o uso de herança para reaproveitamento de comportamento, a composição acaba se revelando uma alternativa mais limpa e flexível.

## 3.5 Mas, então, por que usamos herança?

Depois de tudo isso, você pode estar pensando: "então a herança é ruim, não serve pra nada e vou usar composição pro resto da minha vida". Calma, não é bem assim (ou é?).

Herança e composição são ferramentas diferentes, e cada uma tem o seu lugar. O problema não é a herança em si, mas o uso dela nos contextos errados. Dito isso, considere utilizar herança quando:

**1. A classe filha expressar "um tipo especial de" e não "um papel desempenhado por"**

`Guerreiro` é um tipo especial de `Personagem`. Isso é uma relação de identidade genuína, e herança faz todo o sentido. Mas se `Guerreiro` apenas desempenha o papel de alguém que anda — isso é comportamento, e comportamento vai melhor com composição.

**2. Uma instância da classe filha nunca precisar se tornar um objeto de outra classe**

Um `Guerreiro` jamais vai se tornar um `Mago` no meio da execução. A identidade é fixa. Se a "classe" do personagem pudesse mudar em runtime, herança seria a escolha errada.

**3. A classe filha estender, e não substituir, as responsabilidades da classe pai**

A herança funciona bem quando a filha acrescenta ao comportamento da mãe. Se a filha acaba substituindo quase tudo que a mãe faz, é um sinal de que talvez não seja uma relação "é um" genuína.

**4. A hierarquia representar claramente um relacionamento "é um"**

Antes de criar uma herança, faça a pergunta em voz alta: "`EspadaMagica` *é uma* `Espada`?" —> faz sentido. "`Guerreiro`  é um `MovimentoTerrestre`?" —> não faz sentido nenhum. Se soar estranho, provavelmente é composição.

**5. Você precisar aplicar alterações globais às classes filhas mudando apenas a classe pai**

Se você adicionar um método `descansar()` na classe `Personagem`, automaticamente todos os seus filhos — `Guerreiro`, `Mago`, `Arqueiro` — herdam esse comportamento. Quando uma mudança centralizada precisa se propagar para toda uma família de classes, a herança é muito eficiente.

**6. Você precisar aplicar a mesma lógica a diferentes tipos de dados**

Quando várias classes compartilham a *mesma estrutura* e lógica, e não apenas o mesmo contrato, herança evita uma duplicação real de código. Interfaces definem *o quê*; a herança de uma classe concreta pode compartilhar *o como*.

**Em resumo: use herança para modelar **o que algo é**. Use composição para modelar **o que algo faz**. Na dúvida, composição costuma ser a escolha mais segura, é mais fácil partir dela e introduzir herança depois do que refatorar uma hierarquia torta no meio do projeto.**

## Exercício

**1) Refatorando com composição**

Retome o sistema de equipamentos do Capítulo 2 — especificamente a EspadaMagica, que herdava simultaneamente de Espada e de Magia.

- Refatore essa classe usando composição: em vez de herdar de Magia, a EspadaMagica deve ter uma IHabilidade (uma nova interface que você vai criar), com um método virtual puro void ativarHabilidade().
- Crie uma classe MagiaDeGelo e uma MagiaDeFogo que implementem IHabilidade, e instancie no Main.cpp duas EspadaMagicas com magias diferentes, chamando ativarHabilidade() em cada uma.

## Conclusão 

Vimos na prática por que "prefira composição sobre herança" é um dos conselhos mais valiosos da orientação a objetos. 

Mas o recado mais importante fica na seção 3.5: composição não é substituta universal da herança. São ferramentas para problemas diferentes. Quando a relação é genuinamente "é um", a herança é elegante e eficiente. Quando a relação é "faz algo" ou "tem um comportamento", a composição vai te poupar muita dor de cabeça.

Com isso, encerramos o Capítulo 3. 

Agora, você já tem mais que o suficiente para se aventurar em projetos pessoais usando C++! 

Esperamos que tenha aproveitado e gostado desta trilha :)

Sinta-se livre para elogiar ou criticar qualquer aspecto da trilha e para comentar sua experiência fazendo-a.

Ah, e se você é membro da Conway e ainda está no período de fazer as trilhas, não se esqueça de fazer o projeto final e mandar para a gente!

Nós vemos por aí... tchau!

## Bibliografia

- Site learncpp: https://www.learncpp.com/
- Herança x Composição: https://www.macoratti.net/11/05/oop_cph1.htm

<img src="../OOP-Cpp/imagens/03_adeus_miranha.jpg" width=500>
