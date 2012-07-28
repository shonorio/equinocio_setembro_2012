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

:METHOD| URI | Params | Description | Return |

:GET | /produtos | - | lista dos produtos | { produtos : [ { nome: cafe123, id: 3, preco: 5.55 } ], message: ok  }|
:GET | /produtos/<id> | - | retorna um produto | { produto : { nome: cafe123, id: 3, preco: 5.55 }, comentarios: [ { user_id: 123, mensagem: gostei do sabor bla bla } ], message: ok  }|


# URL Sitemap

