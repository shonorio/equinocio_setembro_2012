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
|GET    |/api/produtos             |  -   | lista dos produtos|{ produtos : [ { nome: cafe123, id: 3, preco: 5.55 } ], message: ok  }|
|GET    |/api/produtos/<id>        | - | retorna um produto | { produto : { nome: cafe123, id: 3, preco: 5.55 }, comentarios: [ { user_id: 123, mensagem: gostei do sabor bla bla } ], message: ok  }|
|POST   |/api/efetuar_pagamento    | { codigo_promocional: '123', compra_id: 123  } | efetua a compra, add queue e-mail | { message: ok } |
|POST   |/api/clientes/novo        | { email: 'rento.cron@xpto.com', nome: 'a name', senha: fuba } | ... | { message: ok } |
|POST   |/api/clientes/login       | { email: 'rento.cron@xpto.com', senha: fuba } | ... | { message: ok } |
|POST   |/api/produtos             | { nome: 'kafe gostoso', preco: 3.20, loja: 1 }, <Imagem> | cadastra o produto | { message: ok } |


# URL Sitemap

:METHOD | URI                  | CONTENT               |Description|
|------:|----------------------|-----------------------|-----------|
|GET    | / | - | homepage, consulta os produtos, cria session |
|GET    | /logout      | - | destroi a sessao, rediciona para home|
|POST   | /login | cliente.email, cliente.senha | faz o login e atualiza session |
|GET    | /carrinho | | mostra os itens no carrinho com a opcao de digitar o codigo promocional e confirmar o pagamento |



TODO:
    ?question? vamos deixar os metodos do /api/ disponiveis para acesso direto ou faremos um layer pela aplicação?
    adicionar parte de login/senha do admin e escrever as URLs