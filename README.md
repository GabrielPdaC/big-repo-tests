# Estudo do uso de sparse-checkout para big repo's no git

Estudo voltado ao uso do git [sparse-checkout](https://git-scm.com/docs/git-sparse-checkout) para big repo com maioria dos arquivos no root do projeto. Trata-se de um diretório de scripts utilitários, os quais são utilizados em larga escalada na rede interna.

## Como utilizar o git sparse-checkout para não baixar todo repositório local:

### Clonando o repostório

* Ao utilizar o ```git clone``` é necessário informar o parâmetro ```--no-checkout``` vai fazer com que vincule o diretório ao repositório sem fazer download dos arquivos
```git-bash
git clone --no-checkout https://my/project.git
```

### Configurando spase-checkout para não considerar arquivos no root

* Com esse comando vai indicar no repositório que deve utilizar o sparse-checkout, setando ```core.sparseCheckout```
* Parâmetro ```--no-cone``` para indicar que não deve usar o cone pattern por padrão (setando ```core.sparseCheckoutCone false```), com isso, conseguimos fazer o checkout sem baixar os arquivo do root, adicionando apenas os arquivos que quisermos baixar no arquivo sparse-checkout[^1]
```git
git sparse-checkout init --no-cone
```
* Lembrando que por padrão o arquivo sparse-checkout[^1], inclui todo root e exclui qualquer diretório filho, vindo populado com:
> /* <br>
> !/*/

* Para sobrescrever todo arquivo de sparse-checkout[^1] com um novo conteúdo utilizamos o comando abaixo. Além disso, tem o parâmetro ```--stdin``` para pode digitar vários em uma linha.
```git
git sparse-checkout set [--stdin] | [file] [dir]
```

* Para incluir arquivo e diretórios no arquivo do sparse-checkout[^1] e, com isso monitor apenas esses arquivos/diretórios ao fazer fetch/status, utilizamos o seguinte comando:
* O parâmetros ```--no-cone``` setado indica que não utiliza cone pattern ao inserir novos diretórios, fazendo com que a recursão de arquivo tenha que ser informada no pattern. Utilizar a seguinte notação para arquivos do root ```/file.extension``` e ```/dir/``` para diretórios
```git
git sparse-checkout add [file] [dir]
```

* Comando para aplicar as configurações realizadas (não se faz necessário, geralmente. Só quando modifica algum parâmetro de configuração da inicialização)
```git
git sparse-checkout reapply
```

### Realizando alteração
* Após tudo configurado, podemos fazer o checkout apenas do que está indicado no arquivo de sparse-checkout
```git
git checkout [branch]
```

* Para fazer add de arquivos que não estão em diretórios do sparse-checkout, podemos utilizar o seguinte comando:
* Vale ressaltar que isso não inclui o arquivo no arquivo sparse-checkout[^1]. Então o melhor é inserir no arquivo utilizando o ```sparse-checkout add [file]``` após fazer stash da alteração.
```git
git add --sparse [file]
```

* No restante, basta seguir fluxo normal, com ```git commit``` e ```git push```

## Visão da implementação de script facilitador do uso do sparse-checkout

* Criar um script facilitador que vai fazer automaticamente a inicialização do sparse-checout e também vai adicionar os arquivo com ```git sparse-checkout add``` no arquivo de sparse-checkout[^1].

[^1]: O arquivo fica localizado em: .git/info/sparse-checkout
