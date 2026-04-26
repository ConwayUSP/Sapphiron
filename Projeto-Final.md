# Projeto Final

Sejam bem-vindos ao projeto final da nossa trilha!

Se você chegou até aqui, significa que todos os capítulos foram lidos, ou que você acha de **tens o que é necessário** para garantir a sua certificação sem passar pelos nossos ensinamentos diretamente (nós respeitamos isso, sinceramente).

https://github.com/user-attachments/assets/762d09fe-a7ee-48a2-92ce-4ac1f470a804

> Link para o perfil com o vídeo: https://www.tiktok.com/@techroastshow/video/7311866672929852714

No caso, se você for `um membro da Conway`, a submissão do projeto final e sua eventual aprovação resultará no ganho do seu **certificado oficial de conclusão desta trilha**, fornecido pela nossa liga acadêmica!

Então, você terá um documento que **provará suas habilidades** com a nossa querida linguagem de programação C++, enriquecendo o seu currículo.

Sem mais delongas, vamos lá:

# Projeto de RPG

![RPG](/Media/imagemRPG.jpg)

Certamente, você já deve ter pelo menos ouvido falar a respeito de como funciona um [`Role-Playing Game (RPG)`](https://brasilescola.uol.com.br/curiosidades/rpg.htm). Existem vários exemplos por aí, desde os mais clássicos, como [`Dungeons & Dragons`](https://en-wikipedia-org.translate.goog/wiki/Dungeons_%26_Dragons?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc), até os que tiveram uma retomada de popularidade razoavelmente recente, como o [`Cyberpunk`](<https://en-wikipedia-org.translate.goog/wiki/Cyberpunk_(role-playing_game)?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc>). Caso você viva debaixo de uma pedra, recomendamos dar uma olhada, porque é bem bacana.

Aqui, para a nossa trilha, pensamos em estabelecer o projeto de conclusão baseado nisso. Então, a proposta é:

`Construa um RPG de Texto`, com sistema para: **construção de fichas de personagens, narrativas com tomadas de decisão e mecânica de combate.**

## 1. Construindo Fichas

A construção de fichas é parte crucial para qualquer RPG. É aqui que você vai **nomear seu personagem**, definir sua **classe (guerreiro, mago, bardo, etc)**, seus **atributos** e afins.

A programação em C++ aqui possui um sentido bem nítido a ser seguido e é perfeito para aplicar orientação a objetos.

Entretanto, como até o momento da produção deste projeto final não foi finalizado o material de **POO** em C++, **não é obrigatório** a aplicação de paradigmas baseados em objetos.

Então, caso você queira utilizar uma abordagem diferente, com **estruturas** por exemplo, fique à vontade. Aqui, vamos avaliar a qualidade da sua implementação de modo geral.

Obviamente, o objetivo é que o usuário consiga interagir com o terminal de modo que seja possível construir diferentes personagens.

Siga seu coração na hora de definir esse sistema. Não é necessário um padrão específico. Diferentes RPGs podem possuir maneiras diferentes de estruturar as fichas de personagens. Alguns, por exemplo, podem ser baseados puramente em sorte na distribuição de atributos, através da rolagem de dados.

## 2. Construindo Narrativa

A narrativa não precisa ser um **calhamaço gigantesco**. Caso você tenha tido tempo para produzir algo nesse sentido, recomendamos que procure publicar seu próprio livro!

(Ok, chega de piadas, por favor)

Porém, faça uma narrativa do tamanho que você preferir!

Como estamos tratando de algo de menor escopo, a construção de algo que envolva a **tomada de decisão do usuário** que estiver jogando, selecionando dentre opções limitadas no terminal, é ideal. Aqui, é bom fazer uma sequência bem **determinística**, com alguns **finais distintos**.

Buscaremos avaliar o seu domínio a respeito de **_loops_ e _condicionais_**. A estruturação em código das "etapas" da história pode ser feita de diferentes maneiras, também.

## 3. Construindo Mecânicas de Combate

Eventualmente, é provável que ocorram combates no seu jogo. Por isso, é necessário mecânicas que dizem respeito a isso.

Por se tratar de um RPG, **o fator sorte** deverá estar incluso. É necessário, assim, simular a rolagem de dados, que podem ter **diferentes quantidades de lados** dependendo da sua abordagem.

Recomendamos utilizar a biblioteca [`ctime`](https://cplusplus.com/reference/ctime/) para brincar com a "aleatoriedade" no seu programa. Utilize, preferencialmente, **o sistema de turnos**.

## 4. Compilação

Seu projeto certamente (e esperamos que sim, pelo menos) estará contido em uma **organização com múltiplos arquivos e diferentes diretórios**. O método de compilação a ser escolhido por você deve ser documentado corretamente em um `README`, para que o avaliador consiga rodar sem ter que quebrar muito a cabeça.

Como ensinamos diferentes maneiras de **compilar código C++**, isso também será parte da sua avaliação.

Você pode, também, utilizar outros métodos que não ensinamos aqui, como `MakeFile`. Apenas, deixe **muito claro** o método de compilação. (Com Zig é bem mais simples, não deixe de conferir nosso [Apêndice](https://github.com/ConwayUSP/Intro_Cpp/blob/main/Ap%C3%AAndices/Ziggy-Stardust.md))

## 5. Entrega

A entrega deve ser feita com o envio para o nosso **email institucional**: `conway-si-each@usp.br`

Utilize o **título**: `"Entrega de Projeto Final para Intro_Cpp"`.

No email, você pode enviar um link para seu repositório no **GitHub**, ou anexar um envio em uma pasta **zipada**.

Para facilitar a sua identificação, não deixe de colocar no email o seu nome completo e identifique quais são os setores da **Conway** (OP, DLC, GG, DPS) que você participa!

# Links Úteis!

Aqui, separamos alguns links que poderão ajudar a deixar os seus **prints** no terminal mais bonitos e estilizados.

## Para Estética:

1. Códigos ANSI: https://gist.github.com/fnky/458719343aabd01cfb17a3a4f7296797

2. Biblioteca {fmt}: https://github.com/fmtlib/fmt

3. FTXUI: https://github.com/ArthurSonzogni/FTXUI

## Para Delays:

1. `std::this_thread::sleep_for`: https://en.cppreference.com/w/cpp/thread/sleep_for

2. `std::chrono`: https://en.cppreference.com/w/cpp/chrono

3. `std::flush`: https://en.cppreference.com/w/cpp/io/manip/flush

# Conclusões

Esperamos que você tenha tido uma **boa experiência de aprendizado** com a nossa trilha e este projeto final. Qualquer dúvida a respeito, não deixe de comunicar aqui **pelo GitHub, pelo email ou diretamente com um dos responsáveis pela trilha**.

**Boa diversão!!**
