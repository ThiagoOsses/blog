---
layout: post
title: "Contain CakePHP"
date: '2015-12-06'
---

Sempre achei o find do CakePHP um tanto quanto sujo, quando existem relacionamento entre modelos, o array retornado é gigante, com muitos campos desnecessários, de todos os lados. Felizmente descobri algo incrível chamado *Contain*. 

O Contain serve pra você poder escolher quais relacionamentos serão feitos na action em questão. Mas pra isso, precisamos definir que o modelo contém o behavior *[Containable][containable]*.

No model:

{% highlight php %}
<?php 
class Post extends AppModel {
    public $actsAs = array('Containable');
    ...
}
?>
{% endhighlight %}

Ou na própria action:

{% highlight php %}
<?php 
...
public function index() {
	$this->Post->Behaviors->load('Containable');
}
...
?>
{% endhighlight %}


Feito isso, é possível definir na query quais relacionamentos serão feitos nesse momento. Porém, com um detalhe: O recursive precisa obrigatóriamente estar como -1, pra todos os relacionamentos serem desfeitos.

{% highlight php %}
<?php 
$this->Post->find('all', array(
	// todos os relacionamentos precisam serem desfeitos.
	'recursive' => -1,
	// Modelos que serão relacionados com o model Post
	'contain' => array(
		'Tag',
		'Category'
	),
));
?>
{% endhighlight %}

É possível aplicar filtros e escolher os campos que serão trazidos nos relacionamentos, evitando assim trazer campos desnecessários.

{% highlight php %}
<?php 
$this->Post->find('all', array(
	// todos os relacionamentos precisam serem desfeitos.
	'recursive' => -1,
	// Modelos que serão relacionados com o model Post
	'contain' => array(
		'Tag' => array(
			'fields' => array(
				'Tag.id',
				'Tag.title',
			),
		),
		'Category' => array(
			'conditions' => array(
				'Category.enabled' => true,
			),
			'fields' => array(
				'Category.title',
			),
		),
	),
));
?>
{% endhighlight %}

Uma outra funcionalidade interessante é o uso recursivo do behavior *[Containable][containable]*, podemos aplicar os seguintes níveis de relacionamentos, desde que necessário.

{% highlight php %}
<?php 
$this->Post->find('all', array(
	// todos os relacionamentos precisam serem desfeitos.
	'recursive' => -1,
	// Modelos que serão relacionados com o model Post
	'contain' => array(
		'Tag' => array(
			// Tag contém categoria
			'Category',
			'fields' => array(
				'Tag.id',
				'Tag.title',
			),
		),
	),
));
?>
{% endhighlight %}

Essa funcionalidade se aplica a versão 2.x do [CakePHP][CakePHP]

Bom, por enquanto é só. Espero ter ajudado, e pra quem quiser aqui tem a documentação oficial do Behavior [Containable][containable], sugiro que leiam, pois a documentação oficial é sempre mais completa. Aqui foi só um atalho :)

[containable]: http://book.cakephp.org/2.0/en/core-libraries/behaviors/containable.html
[CakePHP]: http://book.cakephp.org/2.0/en/index.html