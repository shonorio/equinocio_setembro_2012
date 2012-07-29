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
|GET    | /cliente/recurar_senha | - | pergunta e-mail |
|POST   | /cliente/recurar_senha | {email:x} | envia email com chave para resetar|
|GET    | /cliente/resetar_senha | ?email=xpto&chave=<sha1> | confere a chave, exibe formulario|
|POST   | /cliente/resetar_senha | {email=xpto, chave=<sha1>, senha1, senha2} | ... |








Template do site eu pensei nesse daqui: http://www.freewebsitetemplates.com/preview/clothesfashion/ é simples, da pra por uns cafes no lugar da mulher, e na interface do admin usar o twitter.github.com/bootstrap/ mesmo