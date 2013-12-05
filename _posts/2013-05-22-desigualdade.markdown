---
layout: post
title: Experimentando com modelos de desigualdade
category: experiments
---

Recentemente assisti à um video que faz um bom trabalho ao colocar em
perspectiva a desigualdade mundial. Fiquei bastante intrigado com os dados, é
bastante impressionante saber que 1% das pessoas mais ricas do mundo possuem o
equivalente a soma da riqueza dos três bilhões mais pobres!

O mais interessante, é que a distribuição de riqueza da população mundial,
parece seguir uma Lei de Potência.

Pensando nisso, resolvi criar alguns modelos simples pra saber como – sem
entrar em detalhes – um sistema de trocas (como é o financeiro) levaria à essa
situação. Nada muito conclusivo, mas é legal ver como regras simples aplicadas
em um Método de Monte Carlo revelam dinâmicas atrativas curiosas.

O primeiro modelo, assume uma população de dez mil participantes com uma certa
quantia de “riqueza” inicial (_i.e._ dinheiro) de exatas 100 unidades. Ou seja,
todos os habitantes começam com a mesma quantia.

Em seguida, eu realizo um milhão de trocas aleatórias entre os participantes.
Nesta primeira simulação, a troca é uma transferência de uma percentagem
(também aleatória) da riqueza de um pagador à um credor. Neste caso, o pagador
poderá transferir de zero (0%) a toda sua riqueza (100%) à um credor.

Após um milhão de trocas aleatórias, chegamos à uma proporção bastante
desigual. Similar à que se encontra a riqueza mundial real (Lei de Potência).
Com uma alta concentração de riqueza onde 10% da população detém 40% da
riqueza. O histograma a seguir dá uma ideia do resultado:

![Distribuição de Riqueza](/images/distrib-riqueza-1.png?raw=true)

É importante notar, que esse primeiro experimento é curiosamente  acumulador,
porém volátil (_i.e._ as fortunas trocam bastante de mãos): acontece de um
pagador rico transferir quase toda sua fortuna a um credor.

O segundo experimento é  mais acumulador, e menos volátil. Nesse caso, as
trocas são ditadas pelo credor (não pelo pagador): O credor requer que o
pagador transfira uma percentagem aleatória da riqueza do primeiro (ou seja, do
credor). No caso do pagador não ter dinheiro (_i.e._ o credor tem mais dinheiro,
e pede mais que o pagador pode transferir), então todo o dinheiro vai para o
credor – e o pagador fica com nada.

Comos se prevê, ocorre uma concentração altíssima de riqueza, pois os credores
– quanto mais ricos, mais altos valores cobram. De fato, 1% da população acaba
por deter cerca de 65% da riqueza total. O histograma linear não revela muito
sobre os dados:

![Distribuição de Riqueza](/images/distrib-riqueza-2.png?raw=true)

Finalmente, no terceiro experimento, as coisas se revelaram um pouco mais
justas. Desta vez, as trocas sempre ocorreram com base em um valor constante
(ao invés de uma percentagem). O valor constante é simplesmente uma unidade, e
se o pagador não pode transferir esse unidade (_i.e._ tem menos que 1), então
simplesmente transfere todo seu dinheiro ao credor. O resultado, é um
histograma com Distribuição Normal. A maioria das pessoas tem 100 (valor
inicial, mantido como média), enquanto que existem poucos ricos e poucos pobres
– um resultado com uma grande “classe média”:

![Distribuição de Riqueza](/images/distrib-riqueza-3.png?raw=true)

Ainda não é o ideal, pois o sistema produziu ricos e pobres. No pouco que
experimentei – trivialmente – os modelos que não produzem diferença social são
aqueles em que não ocorrem trocas nenhuma, ou então, onde as trocas são sempre
do mais rico para o mais pobre.

