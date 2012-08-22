Endpoints e Sitemap
==================

Iniciando os trabalhos dos Endpoints jeito que o Cron faria, alguem vai revisar isso!

Para escrever estes endpoints eu precisei pensar no workflow de como eu quero comprar café online!

  * Acesso o site, kafeka.com.br
  * Vejo as imagens dos cafes, o nome e o preço deles.
  *
      * podemos pensar em adicionar: comentarios dos compradores, reviews, cadastro de lojas, disponibilidade de entrega, previsao de entrega, muitos problemas. Precisamos alinhar até onde vai ser feito.
  * Abro a pagina de algum cafe
  * Clico em comprar
  * recebo um aviso que o codigo Equinocio2012 tem desconto de 100%! =)
  * uma tela me pergunta se eu ja sou cadastrado, email/senha. ou E-mail/senha + nome + endereço para criar e pagar.
  * recebo um e-mail com o meu café!


Acho que estes passos são suficiente para podemos dar exemplos de pelo menos um dos Backing Services [queue para email].

Para construir este site, e demonstrar mais coisas do 12factor, também precisaremos de um painel de controle admin.

# Endpoints API

## REST pra valer! :)

Isso aqui é um apanhado geral das várias leituras e discussões recententes.  Como o prazo do equinócio está ficando curto, estou colocando aqui logo para começarmos a discutir e refinarmos juntos. -- nuba.

Para que a gente exercite REST dentro do que Roy Fielding definiu como o estilo de arquitetura http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm , e no nível mais alto de maturidade proposto por Richardson em http://www.crummy.com/writing/speaking/2008-QCon/ (e discutido extensivamente no REST in Practice http://restinpractice.com/book/), sugiro:

  * URLs sem verbos, versões, enxutos, e passando longe do REST como tunelamento de RPC
  * CRUD para recursos estáticos
  * Uso parcimonioso dos recursos do servidor, e da rede, com uso de etag ou last-modified + requests condicionais para updates e gets
  * Fazermos o mapa, dentro do possível, da semantica dos verbos e dos status HTTP para a nossa aplicação
  * Evitar ao máximo a manutenção de estado no servidor
  * API com um ou poucos pontos de entrada
  * Oferecer hiperlinks junto às representações para que a API seja navegável (aqui entra o HATEOAS, ou Hypertext as the Engine of Application State).
  * HAL+JSON ou HAL+XML como formato que nos permita entregar a representação dos recursos, recursos embutidos, e seus links.
  * Uso do nosso próprio internet media type para apoiar a escolha do formato de representação e versão da api
     * por exemplo: application/vnd.kafeka.v1+json 

Uma proposta do ciclo de vida de uma compra é a seguinte:

![Ciclo de vida de um pedido](http://stastu.com/stuff/kafeka/kafeka.png)

Onde os links em vermelho são transições "nos bastidores", e os outros links são transições oferecidas ao usuáro em momentos pertinentes e junto das representações dos recursos pedidos.

Segue uma proposta pra o contrato da nossa API REST:


<table>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>recurso</td>
<td>url</td>
<td>verbo</td>
<td>semântica</td>
<td>links ou recursos embutidos na resposta</td>
<td>notas</td>
<td>re: cache</td>
<td>requer autenticacao, autorizacao?</td>
</tr>
<tr>
<td>Produtos</td>
<td>/produto</td>
<td>get</td>
<td>a lista de produtos</td>
<td></td>
<td></td>
<td></td>
<td>amarrar autenticacao em openid ou oauth,<br />autorizar apenas os pedidos do usuario</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Um produto</td>
<td>/produto/\{UUID\_produto\}</td>
<td>get</td>
<td>um produto</td>
<td>\* opcionais<br />\* tamanhos</td>
<td></td>
<td>e-tag relativo ao conjunto: produto, opcionais e tamanhos. ou last-modified</td>
<td>não</td>
</tr>
<tr>
<td>Tamanhos de um produto</td>
<td>/produto/\{UUID\_produto\}/tamanhos</td>
<td>get</td>
<td>os tamanhos em que um produto está disponível</td>
<td>\* produto</td>
<td></td>
<td>e-tag relativo ao conjunto: produto, opcionais e tamanhos. ou last-modified</td>
<td>não</td>
</tr>
<tr>
<td>Opcionais de um produto</td>
<td>/produto/\{UUID\_produto\}/opcionais</td>
<td>get</td>
<td>Os opcionais disponíveis para um produto</td>
<td>\* produto</td>
<td></td>
<td>e-tag relativo ao conjunto: produto, opcionais e tamanhos. ou last-modified</td>
<td>não</td>
</tr>
<tr>
<td>Opcionais de um produto</td>
<td>/produto/\{UUID\_produto\}/opcionais/\{UUID\_opcional\}</td>
<td>get</td>
<td>Um opcional disponível para um produto</td>
<td>\* produto</td>
<td></td>
<td>e-tag relativo ao conjunto: produto, opcionais e tamanhos. ou last-modified</td>
<td>não</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Pedidos</td>
<td>/pedido</td>
<td>get</td>
<td>retorna os pedidos</td>
<td></td>
<td></td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Pedidos</td>
<td>/pedido</td>
<td>post</td>
<td>cria um novo pedido, UUID gerado e informado pelo servidor, put é recomendado apenas se o cliente sempre puder gerar o UUID, não é o caso</td>
<td></td>
<td>retorna no header Location: o endereço do pedido criado</td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Um pedido</td>
<td>/pedido/\{UUID\_pedido\}</td>
<td>get</td>
<td>retorna um pedido</td>
<td>\* Itens de um pedido<br />\* a cobrança<br />\* o recibo ou pagamento</td>
<td></td>
<td>usar e-tag ou last-modified</td>
<td>sim</td>
</tr>
<tr>
<td>Um pedido</td>
<td>/pedido/\{UUID\_pedido\}</td>
<td>put</td>
<td>modifica um pedido</td>
<td>\* Itens de um pedido<br />\* a cobrança<br />\* o recibo ou pagamento</td>
<td>a representação completa do pedido é enviada e recebida</td>
<td>usar e-tag ou last-modified</td>
<td>sim</td>
</tr>
<tr>
<td>Um pedido</td>
<td>/pedido/\{UUID\_pedido\}</td>
<td>delete</td>
<td>apaga um pedido</td>
<td></td>
<td></td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Itens em um pedido</td>
<td>/pedido/\{UUID\_pedido\}/item</td>
<td>get</td>
<td>retorna os itens em um pedido</td>
<td>lista de itens em um pedido</td>
<td></td>
<td>usar e-tag ou last-modified</td>
<td>sim</td>
</tr>
<tr>
<td>Itens em um pedido</td>
<td>/pedido/\{UUID\_pedido\}/item</td>
<td>post</td>
<td>cria um novo item em um pedido</td>
<td></td>
<td>retorna no header Location: o endereço do item criado</td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Itens em um pedido</td>
<td>/pedido/\{UUID\_pedido\}/item</td>
<td>delete</td>
<td>remove todos os itens em um pedido</td>
<td></td>
<td></td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Um item em um pedido</td>
<td>/pedido/\{UUID\_pedido\}/item/\{UUID\_item\}</td>
<td>get</td>
<td>retorna um item em um pedido</td>
<td>\* o pedido<br />\* o produto<br />\* o tamanho<br />\* os opcionais</td>
<td>a representação completa do pedido é enviada e recebida</td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Um item em um pedido</td>
<td>/pedido/\{UUID\_pedido\}/item/\{UUID\_item\}</td>
<td>put</td>
<td>modifica um item em um pedido</td>
<td>\* o pedido<br />\* o produto<br />\* o tamanho<br />\* os opcionais</td>
<td>a representação completa do pedido é enviada e recebida</td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Um item em um pedido</td>
<td>/pedido/\{UUID\_pedido\}/item/\{UUID\_item\}</td>
<td>delete</td>
<td>remove um item de um pedido</td>
<td></td>
<td></td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>As cobranças</td>
<td>/cobranca</td>
<td>get</td>
<td>retorna a lista de cobranças</td>
<td>\* pagamento ou recibo<br />\* pedido</td>
<td></td>
<td>sim, presumindo: imutáveis, sempre válidas.</td>
<td>sim</td>
</tr>
<tr>
<td>Uma cobranca</td>
<td>/cobranca/\{UUID\_cobranca\}</td>
<td>get</td>
<td>retorna uma cobrança</td>
<td></td>
<td></td>
<td>sim, presumindo: imutáveis, sempre válidas.</td>
<td>sim</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Pagamentos</td>
<td>/pagamento</td>
<td>get</td>
<td>retorna os pagamentos do usuário</td>
<td></td>
<td></td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Pagamentos</td>
<td>/pagamento</td>
<td>post</td>
<td>cria um pagamento</td>
<td>\* a cobrança</td>
<td>decidir: se o pagamento não for aceito: <br />\* vamos retorna um status de erro agora; ou<br />\* vamos criar o recurso mesmo assim, mas sinalizar isso para o usuário através de alguma propriedade?</td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Um pagamento</td>
<td>/pagamento/\{UUID\_pagamento\}</td>
<td>get</td>
<td>retorna um pagamento</td>
<td>\* a cobrança<br />\* o recibo, se houver</td>
<td></td>
<td>e-tag ou last-modified</td>
<td>sim</td>
</tr>
<tr>
<td>Um pagamento</td>
<td>/pagamento/\{UUID\_pagamento\}</td>
<td>put</td>
<td>modifica um pagamento</td>
<td>\* a cobrança<br />\* o recibo, se houver</td>
<td>só faz sentido o usuário modificar um pagamento que não tenha sido aprovado, estamos então falando de tentativas de pagamento</td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td>Um pagamento</td>
<td>/pagamento/\{UUID\_pagamento\}</td>
<td>delete</td>
<td>apaga um pagamento</td>
<td>\* a cobrança<br />\* o recibo, se houver</td>
<td>só faz sentido o usuário modificar um pagamento que não tenha sido aprovado, estamos então falando de tentativas de pagamento</td>
<td></td>
<td>sim</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Recibos</td>
<td>/recibo</td>
<td>get</td>
<td>retorna os recibos</td>
<td></td>
<td></td>
<td>e-tag ou last-modified</td>
<td>sim</td>
</tr>
<tr>
<td>Um recibo</td>
<td>/recibo/\{UUID\_recibo\}</td>
<td>get</td>
<td>retorna um recibo</td>
<td>\* a cobrança<br />\* o pagamento<br />\* o pedido</td>
<td></td>
<td>e-tag ou last-modified</td>
<td>sim</td>
</tr>
</table>





## (segue API proposta inicialmente)

:METHOD | URI                  | Params                |Description|Return|
|------:|----------------------|-----------------------|-----------|------|
|GET    |/v1/api/produtos             |  -   | lista dos produtos|{ produtos : [ { nome: cafe123, id: 3, preco: 5.55 } ], message: ok  }|
|GET    |/v1/api/produtos/<id>        | - | retorna um produto | { produto : { nome: cafe123, id: 3, preco: 5.55 }, comentarios: [ { user_id: 123, mensagem: gostei do sabor bla bla } ], message: ok  }|
|POST   |/v1/api/efetuar_pagamento    | { codigo_promocional: '123', compra_id: 123  } | efetua a compra, add queue e-mail | { message: ok } |
|POST   |/v1/api/clientes        | { email: 'rento.cron@xpto.com', nome: 'a name', senha: fuba } | ... | { message: ok } |
|POST   |/v1/api/clientes/login       | { email: 'rento.cron@xpto.com', senha: fuba } | ... | { message: ok } |
|POST   |/v1/api/produtos             | { nome: 'kafe gostoso', preco: 3.20, loja: 1 }, <Imagem> | cadastra o produto, role=admin | { message: ok } |


# URL Sitemap

:METHOD | URI                  | CONTENT               |Description|
|------:|----------------------|-----------------------|-----------|
|GET    | / | - | homepage, consulta os produtos, cria session |
|GET    | /logout      | - | destroi a sessao, rediciona para home|
|POST   | /login | email, senha | faz o login e atualiza session |
|GET    | /carrinho | | mostra os itens no carrinho com a opcao de digitar o codigo promocional e confirmar o pagamento |
|POST   | /carrinho/produto | { produto_id : 1 } | adiciona o produto no carrinho |
|DELETE | /carrinho/produto/<id> | - | remove o produto no carrinho |
|PUT    | /carrinho/produto/<id> | { qtde: nova_qtde}  | atualiza a quantidade do produto no carrinho|
|GET    | /carrinho/produto/<id> | - | na verdade, nao precisa! pois o GET /carrinho retornara todos os produtos para evitar querys...|
|GET    | /cliente/recuperar_senha | - | pergunta e-mail |
|POST   | /cliente/recuperar_senha | {email:x} | envia email com chave para resetar|
|GET    | /cliente/resetar_senha | ?email=xpto&chave=<sha1> | confere a chave, exibe formulario|
|POST   | /cliente/resetar_senha | {email=xpto, chave=<sha1>, senha1, senha2} | ... |








Template do site eu pensei nesse daqui: http://www.freewebsitetemplates.com/preview/clothesfashion/ é simples, da pra por uns cafes no lugar da mulher, e na interface do admin usar o twitter.github.com/bootstrap/ mesmo
