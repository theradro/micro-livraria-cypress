# Cypress

[Cypress](https://www.cypress.io) é uma ferramenta utilizada para realização de testes de front-end de aplicações apresentadas em navegadores. Essa ferramenta permite a criação, execução e debugging de testes. Cypress é uma ferramenta grátis, open source e instalada localmente, constituida de um componente responsável pela execução dos testes, e um serviço de Dashboard para apresentação da execução dos testes e eventuais tarefas de debugging.

Cypress pode ser usado para executar diferentes tipos de testes:

- Testes End-to-end
- Testes de componentes
- Testes de API

Além disso, também é possível executar outros tipos de testes com auxílio de [plug-ins oficiais e plug-ins criados pela comunidade](https://docs.cypress.io/plugins/directory), como testes de envio de emails e apresentação visual de interfaces.

# End-to-end test

Existem diversos tipos diferentes de testes. Assim como apresentado no [Capítulo 8](https://engsoftmoderna.info/cap8.html) do livro texto, os testes são dividos em três grupos de acordo com a sua granularidade:

- Testes de Sistema
- Testes de Integração
- Testes de Unidade

Nesse roteiro, executaremos testes de sistema, também chamados de testes de interface com o usuário. Estes testes testam um software de ponta a ponta (end-to-end) simulando a utilização de um usuário. Como exemplo para a micro-livraria, a utilização do usuário consistiria em abrir o site, escolher o livro desejado, inserir o CEP, calcular o frete e realizar a compra de um livro. Para mais informações sobre a micro-livraria, sua arquitetura e funcionamento, consulte o [roteiro de microsserviços](https://github.com/aserg-ufmg/micro-livraria).

# Configuração do ambiente

Para realização do roteiro, é necessária a configuração inicial do ambiente. Os seguintes passos são necessários:

1 - Fazer um fork do repositório pressionando o botao "Fork" no canto superior direito da tela.

2 - Clonar o projeto para um diretório local:

```
git clone 
```
    
3 - Instalar o [Docker](https://docs.docker.com/get-docker/). A microlivraria será executada com auxílio de Containers.

4 - Executar o sistema da micro-livraria. Para gerar uma nova imagem, basta rodar o seguinte comando na raiz do projeto:

```
docker build -t micro-livraria -f cypress/Dockerfile .
```

Para executar a aplicação:

```
docker run -ti -p 3000:3000 -p 5000:5000 micro-livraria
```
    
5 - Instalar o Cypress. Existem diferentes formas de [instalação](https://docs.cypress.io/guides/getting-started/installing-cypress):

5.1 - A forma recomendada é via npm (necessário [Node.js](https://nodejs.org/en/download/)), versionando o Cypress por projeto. No diretório do projeto:
    
```
npm install cypress --save-dev
```

E em seguida:

```
npx cypress open
```
    
5.2 - Também é possível realizar o [download direto](https://download.cypress.io/desktop) da ferramenta. Após extrair o .zip basta abrir o executável e selecionar a pasta do projeto.

# Tarefa Prática #1: Criação do primeiro teste

A primeira tarefa será a introdução de uso do Cypress: como criar o primeiro teste e como visualizar a execução com auxílio do serviço de dashboard. 

Após a instalação do Cypress no diretório do projeto, será criada uma pasta `cypress` contento diversas pastas e arquivos. A pasta `integration`, em especifico, contem alguns exemplos de testes que podem ser utilizados como template para diversas tarefas, como utilização de cookies, navegação, etc. Ao abrir o Cypress utilizando o comando `npx cypress open` será exibida a tela apresentada na figura 1, onde `1` lista os testes criados para o sistema e `2` é o botão para criação de um novo arquivo de testes.

Para criação do nosso primeiro teste, iremos criar um novo arquivo chamado `meu_teste.js` dentro da pasta `integration`. Os arquivos de testes do Cypress são constituidos de uma sequência de funcões que podem ser encadeadas com outras funções para testar funcionalidades do front-end da aplicação. Como primeiro teste, iremos apenas observar o resultado de uma simples função de asserção no serviço de Dashboard da ferramenta. No arquivo `meu_teste.js` cole o seguinte código e salve o arquivo:

```
describe('Meu primeiro teste', () => {
    it('Não faz nada', () => {
      expect(true).to.equal(true)
    })
  })
```

O teste acima apenas checa se `true` é igual a `true`. Após salvar o arquivo, procure por ele na lista de arquivos de teste na aplicação do Cypress e clique duas vezes no arquivo `meu_teste.js`. Em seguida, será executado o aquivo de testes e os resultados apresentados conforme a figura 2. A área `3` apresenta os resultados dos testes, enquanto `4` apresenta os snapshots obtidos ao longo da execução de cada passo dos testes. Para o nosso simples teste foi apenas constatado que true é igual a true, então todos os testes foram executados com sucesso.

De forma análoga, se alterarmos a linha `3` para `expect(true).to.equal(false)` e salvarmos o arquivo, é possível observar que o navegador já ira se adequar às mudanças no arquivo de teste e consequentemente o teste irá falhar, uma vez que true não é igual a false.

# Tarefa Prática #2: Criação de teste end-to-end

Na segunda tarefa iremos simular um teste end-to-end da nosso sistema. Como mencionado anteriormente, iremos simular a utilização de um usuário constituida pelos seguintes passos:

1 - Abrir o site. 

2 - Escolher o livro desejado.

3 - Inserir o CEP.

4 - Calcular o frete.

5 - Realizar a compra do livro.

Para isso, iremos criar um novo arquivo `meu_teste_end_to_end.js` também na pasta `integration`. Comece inserindo o seguinte código no arquivo para realização do primeiro passo:

```
describe('Teste End-to-End', () => {
    it('Meu Primeiro Teste', () =>{
        // Visitar o viste
        cy.visit('http://localhost:5000/')
    })
  })
```

Os comandos do Cypress são constituídos pelo prefixo cy seguidos da função desejada. A função `visit()` visita uma página, que no caso da nossa micro-livraria que está sendo executada localmente, possui endereço `localhost:5000`. Ao salvar o arquivo vemos que o teste passou em `3`, e em `4` é exibida a página da micro-livraria.

Para o segundo passo iremos supor que o livro desejado é o livro sobre Padrões de Projeto da Gangue dos Quatro (Design Patterns de Gamma et al.). Logo, precisamos garantir que o livro está presente na nossa página. Iremos então pesquisar pela existencia dele na página com o seguinte código:

```
// Escolher o livro desejado
cy.get('[data-id=3]').should('contain.text', 'Design Patterns')
```
        
No código anterior realizamos uma query com o auxílio da função `get`. O nosso catálogo de livros está disposto em três colunas, e o livro desejado está na terceira linha, cujo identificador é o `data-id=3`. Em seguida, realizamos uma asserção sobre essa coluna afirmando que ela deve conter o texto `Design Patterns` que é o nome do livro. 

Ao passar o mouse em cima de cada etada do teste em `3` é possível observar que `4` muda, refletindo cada etapa de teste. Em específico, o última passo onde procuramos pela terceira coluna contendo o livro desejado é apresentada em destaque, mostrando que foi corretamente identificada.

No terceiro passo, precisamos inserir o CEP no campo indicado e clicar no botão `Calcular Frete`. Como existem três instâncias para calcular o frete, podemos utilizar a função `within()` para selecionar a coluna correta, conforme o código a seguir:
		```
		\\ Calcular o frete
		cy.get('[data-id=3]').within(() => {
            cy.get('input').type('10000-000')
            cy.contains('Calcular Frete').click().then
            cy.wait(2000)
        })
        cy.get('.swal-text').contains('O frete é: R$')
        // Calcular o frete
        cy.get('.swal-button').click()
        ```
Buscamos pela terceira coluna e procuramos pelo campo de `<input>`. Inserimos o CEP `10000-000` e pressionamos o botão `Calcular Frete`. Em seguida esperamos 2 segundos com auxílio da função `wait()` para garantir que o modal carregue corretamente. Dentro do modal selecionamos o `swal-text` e fazemos uma asserção para garantir que a mensagem está correta. Por fim clicamos no botão dentro do modal para fechar o pop-up.

Por último, para realizar a compra do livro, devemos pressionar o botão `Comprar` conforme o código abaixo:
		```
		\\ Realizar a compra
		cy.get('[data-id=3]').within(() => {
            cy.contains('Comprar').click()
            cy.wait(2000)
        })
        cy.get('.swal-text').contains('Sua compra foi realizada com sucesso')
        cy.get('.swal-button').click()
        ```
De forma análoga ao cálculo do frete, localizamos o botão `Comprar` na terceira coluna. Também utilizamos a função `wait()` para mais uma vez garantir que o modal carregue corretamente. Dentro do modal selecionamos o `swal-text` novamente e fazemos uma asserção com relação ao conteúdo, que dessa vez deve conter a mensagem de que a compra foi realizada com sucesso. Por fim, clicamos no botão dentro do modal para fechar o pop-up.
