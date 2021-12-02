# Tipografia no Design System: definições e estrutura

[![Edineide Oliveira](https://miro.medium.com/fit/c/96/96/1*ViwvRsONuhJxrzQJIfPR7w.png)](https://edineideoli.medium.com/?source=post_page-----ad557311d864-----------------------------------)

[Edineide Oliveira](https://edineideoli.medium.com/?source=post_page-----ad557311d864-----------------------------------)Follow

[Mar 19](https://medium.com/ux-strategy/tipografia-no-design-system-definições-e-estrutura-ad557311d864?source=post_page-----ad557311d864-----------------------------------) · 7 min read











![img](https://miro.medium.com/max/2000/1*uEJqLh3UwxUN6nuD_fAsRg.png)

Ilustração: Natasha Remarchuk, [Icons8](https://icons8.com/)

> *95% das informações na web está em linguagem escrita
> *– [*Oliver Reichensteisn*](https://ia.net/topics/the-web-is-all-about-typography-period)

Sabendo que a informação textual compõe grande parte do meio digital, demos uma atenção especial às definições de **Tipografia**, pois esta é a disciplina que atua diretamente na modelagem dessa informação escrita.

Aqui vou compartilhar um pouquinho do processo para entendimento e definições de tipografia feitas durante a criação do piloto do Design System da TOTVS.

# Entendendo o cenário

Para ter uma noção geral sobre os usos da tipografia dentro do cenário TOTVS, antes de iniciar a proposta em si, foi feito um levantamento dentro de 6 marcas, nos diferentes canais que cada uma tinha disponível, como Zeplin, Figma, Zeroheight e portal para desenvolvedor. O principal objetivo desse levantamento foi observar aspectos como famílias tipográficas, variações de pesos, tamanhos, etc, a fim de identificar padrões.

**Como foi o processo:** manual. Entrando nos canais, coletando e anotando as observações que ia encontrando. Depois, tudo foi levado para uma planilha única, para facilitar a análise.

![Imagem da tela do Miro, ferramenta onde foi feita a coleta inicial](https://miro.medium.com/max/1400/1*RpYReBjHk-fTs6Hz6hVPmg.png)

Coleta inicial no Miro, usando o recurso de grid, que permitiu exportar e compilar em uma planilha única depois

A partir desse levantamento, foi possível encontrar semelhanças e particularidades entre as marcas, e perceber aspectos como:

- Tamanhos de fonte e pesos mais utilizados
- Quais e quantas famílias tipográficas são usadas por marca
- Nomenclatura e unidades utilizadas pelos times no handoff

Foi uma etapa custosa, até porque nem todo achado era uma variação, às vezes era apenas uma inconsistência. Mas foi uma etapa de grande importância, pois foi a partir dela que conseguimos ter uma base para várias das decisões que viriam compor a proposta de estrutura e tokens de tipografia.

# A estrutura tipográfica proposta

Depois do levantamento, conseguimos ter uma maior clareza sobre quais aspectos referentes à tipografia poderiam ser tokens:

- **Font family** (família tipográfica)
- **Font weight** (variações de pesos)
- **Font size** (tamanhos)
- **Line height** (entrelinhas)
- **Letter spacing** (entreletras)
- **Transform** (modificações em geral)

Também ficou definido que alguns deles seriam comuns à todas as marcas (**global tokens**), como `line-height`*.* Outros poderiam se diferenciar de uma marca para a outra (**brand tokens**), como por exemplo `font-family`*.*

Há ainda as variáveis que estão na intersecção entre as duas camadas*.* Isto porque teriam tokens básicos na **camada global***,* mas também já teriam previstas algumas variações para atender a cenários específicos — que ficarão disponíveis apenas para a marca que tiver esta necessidade.

![Dois conjuntos com os tokens globais e os tokens de marca com uma intersecção contendo font size e font weight](https://miro.medium.com/max/60/1*qkaczHPamJ7BTvGEgenxOA.jpeg?q=20)

![Dois conjuntos com os tokens globais e os tokens de marca com uma intersecção contendo font size e font weight]()

Tokens globais e os tokens de marca

Assim, conseguimos ter uma estrutura geral, mas respeitando as particularidades e necessidades de cada marca ou produto que venha a utilizar o design system.

# Font family

> Família tipográfica, ou **font family**, é o nome que se dá ao conjunto de fontes que apresentam as mesmas características estilísticas, mas com variações de espessuras, larguras, etc.

![Três colunas com exemplos das fontes Roboto, Montserrat e Times New Roman em formato regular, negrito e itálico](https://miro.medium.com/max/1014/1*uG2yNSckliDi5XhoHjhnGg.png)

Exemplos de famílias tipográficas

Na estrutura atual, os tokens referentes à família tipográfica ficaram na camada da marca e é possível utilizar até 3 famílias. Isso porque percebemos que há três grupos principais onde pode haver variação: títulos, textos em geral (paragráfos, rótulos, placeholders) e trechos de código em documentação técnica, por exemplo.

- `font-family-heading`, para títulos
- `font-family-text`, para textos em geral
- `font-family-code`, para trechos de código

Não existe a obrigatoriedade de se trabalhar com famílias diferentes, alguns cenários inclusive utilizam a mesma família tipográfica para títulos e textos, por exemplo. Mas é interessante tratar de forma separada para outros cenários e caso haja alguma mudança futura.

![Dois exemplos um contendo fonte igual para títulos e textos e outra com fontes diferentes](https://miro.medium.com/max/60/1*C5QDVH5H84q7kCjgT4spVg.png?q=20)

![Dois exemplos um contendo fonte igual para títulos e textos e outra com fontes diferentes]()

# Font size: tamanhos e escala

> **Font size** é o tamanho da fonte propriamente dito, podendo ser especificado em diferentes medidas, como pixel, ponto, rem, em.

Para criar os tamanhos, utilizamos uma **escala modular**, por permitir criar relações mais harmoniosas entre os tamanhos utilizados, além de tornar o processo de tomada de decisão consciente e menos arbitrário.
(Tem ferramentas que ajudam nisso, como [**Modular Scale**](https://www.modularscale.com/?14&px&1.125) e [**Type Scale**](https://type-scale.com/))

Além disso, a progressão é gerada a partir de um **número base**, o que facilita o uso de medidas relativas (medidas em **rem**, ao invés de **pixels**), o que é um ponto bastante importante para acessibilidade.

Mas como a escala gera números quebrados, criamos uma regrinha para arredondamento dos valores.

Assim, temos tamanhos que variam de 10 a 96px, que engloba as variações que encontramos no levantamento inicial. Porém, ficam na camada global apenas a progressão do 12 ao 48px. Os demais tamanhos podem ser adicionados à camada das marcas que tiverem essa necessidade.

![Tabela de referência dos tamanhos e seus respectivos tokens.](https://miro.medium.com/max/60/1*mZO8oBVeQtpI25d47xeuhQ.png?q=20)

![Tabela de referência dos tamanhos e seus respectivos tokens.]()

Como as ferramentas de design utilizam pixel como unidade, este foi o ponto de partida e mantivemos essa informação visível. Mas sempre reforçando para, no desenvolvimento, considerar as medidas relativas.

# Font weight: variações de pesos

> **Font weight** se refere às variações de peso/ intensidade da fonte dentro de uma mesma família.

Essas variações às vezes aparecem identificadas como light, normal, bold, extrabold, etc; ou por meio de uma numeração padronizada (100 a 950), sendo que quando mais alto o número, mais "pesado" ou intenso é o traço daquela versão.

Vale ressaltar que nem todas as famílias tipográficas possuem todas as variações. Isso não é um impeditivo, mas é algo que deve ser considerado ao se escolher uma família para uma marca ou projeto.

![Exemplo da família Inter, com font-weight do 100 ao 900](https://miro.medium.com/max/60/1*Xe-rhmSzFybVxbTJv5PIxA.png?q=20)

![Exemplo da família Inter, com font-weight do 100 ao 900]()

Exemplo da família Inter, com font-weight do 100 ao 900

Dentro das variações possíveis, colocamos na camada global apenas os pesos básicos*.* Caso exista a necessidade de trabalhar com outros pesos, estes poderão ser incorporados e disponibilizados apenas para as marcas com esse cenário *(brand tokens)*.

Assim, evita-se de sobrecarregar com variações que não estão sendo utilizadas, já que cada variação adicionada aumenta o tamanho do arquivo para download para o usuário.

![Exemplo mostrando os diferentes tokens para font weight para as camadas global e de marca](https://miro.medium.com/max/60/1*ns325t2bK1O1e6KbgFRKtw.png?q=20)

![Exemplo mostrando os diferentes tokens para font weight para as camadas global e de marca]()

Exemplo mostrando os diferentes pesos para font-weight para as camadas global e de marca

# Line height: um respiro entre as linhas

> **Line height** (altura de linha ou entrelinha) na web é o espaço adicionado acima e abaixo acima e abaixo dos caracteres.

Isto significa que uma fonte de 16px com entrelinha de 24px, terá 4px acima e abaixo. Essa medida é importante pois é ela quem molda a aparência dos parágrafos e está diretamente relacionada a uma boa leitura.

![Esquema mostrando o font-size como a altura do texto e o line-height como os espaços acima e abaixo da fonte](https://miro.medium.com/max/60/1*qOG8YSzlO6sKZuAippaGIA.png?q=20)

![Esquema mostrando o font-size como a altura do texto e o line-height como os espaços acima e abaixo da fonte]()

No design system, o *line-height* pode variar muito, dependendo da situação. Por esse motivo, colocamos os tokens com valores em porcentagem e uma orientação de como utilizar em textos e em componentes.

## **Uso em texto corrido**

Para textos em geral, como parágrafos, legendas, etc, o uso de uma entrelinha adequada é essencial no conforto de leitura. Se o valor utilizado for muito justo ou muito espaçado, pode tornar a leitura mais difícil e cansativa.

![Exemplos um do tipo ideal outro com espaçamento maior que o ideal e outro com espaçamento menor que o ideal](https://miro.medium.com/max/2000/1*FTTcs7CbFxbXjC94n3XgAw.png)

Exemplos de entrelinha confortável, muito grande e muito estreita

Segundo a [WCAG](https://www.w3.org/TR/WCAG21/#text-spacing), todo conteúdo de texto precisa ter pelo menos 1.5x a altura do texto de entrelinha. Assim, se o texto possui 16px de tamanho, a entrelinha deve ser de pelo menos 24px, ou em porcentagens, no mínimo 150%.

Entretanto, essa medida está diretamente relacionada ao tamanho da fonte. Quanto menor o tamanho da fonte, maior precisa ser a entrelinha para garantir uma boa leitura.

## Uso em componentes

Em componentes, a entrelinha perde sua função principal e passa a impactar na construção do componente em si e os espaços que o estruturam.

![Exemplo de um botão com line-height compondo a estrutura](https://miro.medium.com/max/60/1*gJkPVl_CVUBl6LUUqyF-iA.png?q=20)

![Exemplo de um botão com line-height compondo a estrutura]()

Exemplo de um botão com line-height compondo a estrutura

Nesses casos, há uma maior flexibilidade de uso e é preciso avaliar caso a caso, para aplicar a entrelinha mais adequada à necessidade.

# Letter Spacing: o espaço entre caracteres

***Letter spacing\*** é a propriedade que permite definir o espaçamento entre caracteres de texto. Somada à outras características — como a família utilizada, tamanho e peso — impacta diretamente o conforto na leitura.

Para atender aos diferentes contextos observados, colocamos cinco valores positivos (mais espaçados) e cinco negativos (menos espaçados), além do automático (padrão da fonte).

![Escala com zero ao centro indicando automático, à esquerda os valores negativos e à direita os valores positivos](https://miro.medium.com/max/1012/1*ohwtB6viMRGn0nfjDkBsTA.png)

# Transform: modificações em texto

Aqui entrariam as transformações no texto para situações e necessidades específicas, como *uppercase*, que deixar todas as letras maiúsculas, ou número tabular, para uso em uma tabelas que exijam que todos os números ocupem o mesmo espaço.

Encontramos poucos modificadores deste tipo no levantamento, mas optamos por manter inicialmente, pois há tamanhos que teriam o pré-requisito de serem usados em conjunto com modificadores.
Mas foi um ponto que gerou muita dúvida, se valeria a pena ou não transformar em token.

(Se você já tiver passado por essa fase, conta como resolveu?)

# Concluindo…

Mesmo fazendo o levantamento com toda a atenção e cuidado, mesmo validando internamente, muitas dúvidas e incertezas rondaram todo o processo… Será que está fazendo sentido? Será que estamos contemplando todos os cenários? As nomenclaturas estão auto-explicativas? Será que faltou alguma variação?
Mas o que acalma um pouco meu coração ansioso é saber que com certeza vamos revisitar e melhorar muitas vezes, este foi só o primeiro passo.
Afinal, não é justamente este um dos propósitos do design system: facilitar as mudanças?

Para quem estiver estudando o assunto, ou buscando entender um pouquinho mais sobre tipografia, conteúdos que me ajudaram muito:

- Livros “***Tipos na tela”\*** e “***Pensar com tipos”\***, ambos da Ellen Lupton
- https://medium.com/eightshapes-llc/typography-in-design-systems-6ed771432f1e
- https://medium.com/sketch-app-sources/exploring-responsive-type-scales-cf1da541be54