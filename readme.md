### Trabalho de Engenharia de Software II - Padrões de Desenvolvimento
**Integrantes**: Alberto Parker, Eduardo Leal, Maiquel Junior e Vitor Manoel

------------

## Singleton
### Objetivo do Singleton:

O principal objetivo do padrão de projeto Singleton é assegurar que uma determinada classe possua apenas uma instância ao longo de todo o ciclo de vida da aplicação. Além disso, ele garante que essa instância seja facilmente acessível de qualquer lugar do sistema, funcionando como um ponto de acesso global.

Esse padrão é frequentemente utilizado em cenários onde um único ponto centralizado de controle ou de dados é necessário, evitando duplicações desnecessárias e conflitos de estado. Com o Singleton, é possível proteger recursos compartilhados, como arquivos de log, configurações, conexões com banco de dados, entre outros.

### Quando devemos Utilizar:

Deve-se utilizar Singleton quando:

- É necessário que apenas uma instância da classe exista.
- Diferentes partes da aplicação precisam acessar o mesmo objeto compartilhado.
- Há necessidade de centralizar um recurso ou controle, como: Configurações Globais,  Log ou até mesmo Conexão com banco de dados

### Estrutura:

No código abaixo, foi utilizado o padrão Singleton para garantir que apenas uma instância da classe Biblioteca seja criada e compartilhada por toda a aplicação.
Mesmo criando múltiplas variáveis, elas sempre apontam para a mesma instância, mantendo os dados sincronizados.

#### Sem Singleton

```typescript
class Biblioteca {
  private livros: string[] = [];

  public adicionarLivro(titulo: string): void {
    this.livros.push(titulo);
  }

  public listarLivros(): void {
    console.log("Livros na biblioteca:", this.livros);
  }
}

// Criação de duas instâncias
const biblioteca1 = new Biblioteca();
const biblioteca2 = new Biblioteca();

biblioteca1.adicionarLivro("React Aprenda Praticando");
biblioteca2.adicionarLivro("Lógica de Programação e Algoritmos com JavaScript");

biblioteca1.listarLivros();
// ["React Aprenda Praticando"]

biblioteca2.listarLivros();
// ["Lógica de Programação e Algoritmos com JavaScript"]

console.log(biblioteca1 === biblioteca2);
// Irá retornar false, pois são instâncias diferentes
```

#### Com Singleton

```typescript
class Biblioteca {
  private static instancia: Biblioteca;
  private livros: string[] = [];

  private constructor() {}

  public static getInstancia(): Biblioteca {
    if (!Biblioteca.instancia) {
      Biblioteca.instancia = new Biblioteca();
    }
    return Biblioteca.instancia;
  }

  public adicionarLivro(titulo: string): void {
    this.livros.push(titulo);
  }

  public listarLivros(): void {
    console.log("Livros na biblioteca:", this.livros);
  }
}

const biblioteca1 = Biblioteca.getInstancia();
const biblioteca2 = Biblioteca.getInstancia();

biblioteca1.adicionarLivro("React Aprenda Praticando");
biblioteca2.adicionarLivro("Lógica de Programação e Algoritmos com JavaScript");

biblioteca1.listarLivros();
// ["React Aprenda Praticando", "Lógica de Programação e Algoritmos com JavaScript"]
biblioteca2.listarLivros();
// Mesmo resultado

console.log(biblioteca1 === biblioteca2);
// Irá retornar true, pois é a mesma instância.
```

### Pontos Fortes:

- Consistência global: Garante que todos os módulos do sistema acessem a mesma instância, evitando divergências de estado.

- Controle centralizado: Facilita o gerenciamento de recursos compartilhados, como configurações ou dados comuns.

- Redução de uso de memória: Evita múltiplas instâncias desnecessárias, economizando recursos em sistemas grandes.

- Fácil de implementar e utilizar: Simples de integrar em linguagens orientadas a objetos como TypeScript, Java ou C#.

### Pontos Fracos:

- Estado global oculto: Como todos acessam a mesma instância, mudanças em um lugar afetam outros módulos, podendo gerar efeitos colaterais inesperados.

- Dificuldade em testes unitários: Torna-se mais difícil isolar comportamentos durante os testes, especialmente se a instância mantém estado interno.

- Acoplamento indesejado: Componentes que dependem diretamente do Singleton ficam mais difíceis de reutilizar ou substituir por versões alternativas.

- Não é thread-safe por padrão: Em sistemas com concorrência (multithread), é necessário cuidado adicional para evitar instâncias duplicadas.

## Factory Method
### Objetivo do Factory Method:

O padrão Factory Method serve para esconder como os objetos são criados, deixando essa tarefa para as classes filhas. Em vez de usar o new diretamente para criar um objeto, esse padrão define um método especial que pode ser mudado nas subclasses para escolher qual tipo de objeto será criado.

Com isso, o código principal não precisa saber qual classe exata está sendo usada para criar o objeto. Isso deixa o código mais flexível e fácil de mudar ou melhorar no futuro, já que as partes do programa ficam menos presas umas às outras.

### Quando devemos Utilizar:

É possível usar o Factory Method quando:

- Você precisa criar objetos de um jeito mais flexível, mas não quer que seu código dependa diretamente da classe usada para isso.

- O sistema precisa permitir novas versões ou tipos de objetos no futuro, sem precisar mudar o código que já está pronto.

- Você quer passar a responsabilidade de criar os objetos para outras classes (filhas), deixando o código principal mais genérico e reutilizável.

### Estrutura:

No código abaixo, foi usado o padrão Factory Method para criar diferentes tipos de livros, como LivroFisico e LivroDigital, usando uma estrutura comum (interface).

O método criarLivro() funciona como uma fábrica, ou seja, ele cuida da criação dos livros. Assim, as subclasses (como LojaFisica e LojaOnline) é que dizem qual tipo de livro será criado.
##### Sem Factory:

```typescript
interface Livro {
  ler(): void;
}

class LivroFisico implements Livro {
  ler(): void {
    console.log("Lendo livro físico: folheando páginas.");
  }
}

class LivroDigital implements Livro {
  ler(): void {
    console.log("Lendo livro digital: deslizando na tela.");
  }
}

class LojaFisica {
  venderLivro(): void {
    const livro = new LivroFisico(); // criação direta
    livro.ler();
  }
}

class LojaOnline {
  venderLivro(): void {
    const livro = new LivroDigital(); // criação direta
    livro.ler();
  }
}

const loja1 = new LojaFisica();
const loja2 = new LojaOnline();

loja1.venderLivro();
// Lendo livro físico
loja2.venderLivro();
// Lendo livro digital
```

##### Com Factory:

```typescript
interface Livro {
  ler(): void;
}

class LivroFisico implements Livro {
  ler(): void {
    console.log("Lendo livro físico: folheando páginas.");
  }
}

class LivroDigital implements Livro {
  ler(): void {
    console.log("Lendo livro digital: deslizando na tela.");
  }
}

abstract class LojaDeLivros {
  abstract criarLivro(): Livro;

  venderLivro(): void {
    const livro = this.criarLivro();
    livro.ler();
  }
}

class LojaFisica extends LojaDeLivros {
  criarLivro(): Livro {
    return new LivroFisico();
  }
}

class LojaOnline extends LojaDeLivros {
  criarLivro(): Livro {
    return new LivroDigital();
  }
}

const loja1 = new LojaFisica();
const loja2 = new LojaOnline();

loja1.venderLivro();
// Lendo livro físico
loja2.venderLivro();
// Lendo livro digital
```

### Pontos Fortes:

- Separação entre criar e usar: O código que usa o objeto não precisa saber qual classe exata está sendo usada para criá-lo.

- Fácil de adicionar novidades: Você pode criar novos tipos de produtos sem precisar mudar o código que já usa esses produtos.

- Segue o princípio aberto/fechado: Isso quer dizer que o sistema pode ser melhorado ou ampliado sem precisar mudar o que já está feito.

- Organização mais clara: Deixa o sistema mais organizado, dividido em partes menores, o que facilita na hora de entender e fazer manutenção.

### Pontos Fracos:

- Mais complexo: Usar esse padrão exige mais classes e mais estrutura do que simplesmente usar new para criar objetos direto.

- Muito complicado para projetos simples: Em sistemas pequenos, pode parecer exagero usar tanta estrutura só para criar objetos.

- Difícil no começo: Se não for bem planejado, pode ser confuso entender e usar esse padrão da forma certa.

## Builder
### Objetivo do Builder:

O padrão Builder tem como a sua função principal estruturar a criação de objetos que possuem várias partes ou configurações, permitindo que eles sejam montados gradualmente. A ideia central é separar a construção do objeto da sua representação final, de forma que o mesmo processo de construção possa gerar diferentes versões ou variações do objeto.

Isso evita construtores confusos com muitos parâmetros e permitindo maior clareza e controle na criação de objetos mais elaborados.

### Quando devemos Utilizar:

O Builder é especialmente útil quando:

- O objeto a ser criado possui muitas propriedades opcionais ou precisa de configuração personalizada.

- É necessário ter diferentes formas de montar o mesmo objeto.

- Deseja-se melhorar a legibilidade do processo de criação, evitando longos construtores ou múltiplas sobrecargas de método.

### Estrutura:

Neste exemplo, utilizamos o padrão Builder para montar objetos do tipo Livro, que podem conter título, autor, conteúdo e ISBN.
O processo de construção é feito passo a passo, com chamadas encadeadas, facilitando a personalização sem precisar passar tudo no construtor.

#### Sem Builder

```typescript
class Livro {
  titulo?: string;
  autor?: string;
  conteudo?: string;
  isbn?: string;

  exibirInfo(): void {
    console.log("Livro:", this.titulo, "-", this.autor, "-", this.isbn);
  }
}

// Criação do Livro
const livroCompleto = new Livro();
livroCompleto.titulo = "Diário de um Banana";
livroCompleto.autor = "Jeff Kinney";
livroCompleto.isbn = "978-8576831309";
livroCompleto.conteudo = "A vida nada fácil de um garoto na escola";

livroCompleto.exibirInfo();
// Livro: Diário de um Banana - Jeff Kinney - 978-8576831309
```

#### Com Builder

```typescript
class Livro {
  titulo?: string;
  autor?: string;
  conteudo?: string;
  isbn?: string;

  exibirInfo(): void {
    console.log("Livro:", this.titulo, "-", this.autor, "-", this.isbn);
  }
}

class LivroMontador {
  private livro: Livro;

  constructor() {
    this.livro = new Livro();
  }

  setTitulo(titulo: string): LivroMontador {
    this.livro.titulo = titulo;
    return this;
  }

  setAutor(autor: string): LivroMontador {
    this.livro.autor = autor;
    return this;
  }

  setConteudo(conteudo: string): LivroMontador {
    this.livro.conteudo = conteudo;
    return this;
  }

  setISBN(isbn: string): LivroMontador {
    this.livro.isbn = isbn;
    return this;
  }

  construir(): Livro {
    return this.livro;
  }
}

// Teste
const livroCompleto = new LivroMontador()
  .setTitulo("Diário de um Banana")
  .setAutor("Jeff Kinney")
  .setISBN("978-8576831309")
  .setConteudo("Capítulo 1: A vida nada fácil de um garoto na escola...")
  .construir();

livroCompleto.exibirInfo();
// Livro: Diário de um Banana - Jeff Kinney - 978-8576831309
```

### Pontos Fortes:

- Clareza na construção: O processo de montar objetos complexos ganha mais organização e lucidez.

- Personalização flexível: Permite criar diferentes versões do objeto sem modificar sua estrutura base.

- Evita construtores longos: Substitui a necessidade de múltiplos parâmetros ou sobrecargas de construtores.

- Reutilização de código: O mesmo Builder pode ser usado para montar objetos similares com pequenas variações.

### Pontos Fracos:

- Mais classes envolvidas: A aplicação do padrão requer a criação de classes auxiliares (Builder), o que pode aumentar o volume de código.

- Sobrecarga desnecessária em casos simples: Em objetos simples, o uso do Builder pode ser exagerado.

- Curva de aprendizado inicial: Para quem não conhece o padrão, a estrutura pode parecer excessiva à primeira vista.

## Abstract Factory
### Objetivo do Abstract Factory:

O padrão Abstract Factory tem como objetivo fornecer uma estrutura para criar famílias de objetos interdependentes, garantindo que eles sejam compatíveis entre si, sem que o código cliente precise saber quais classes concretas estão sendo usadas.

Esse padrão é especialmente útil quando o sistema precisa variar os produtos utilizados de forma consistente, como criar diferentes interfaces para sistemas operacionais, ou no nosso exemplo, diferentes tipos de livros e capas com estilos padronizados por editora.

### Quando devemos Utilizar:

O Abstract Factory é recomendado quando:

- Seu sistema precisa trabalhar com várias famílias de objetos relacionados.

- Você quer garantir que objetos criados juntos sejam compatíveis entre si.

- Deseja isolar o código cliente da lógica de criação dos objetos.

### Estrutura:

Neste exemplo, temos uma fábrica que cria produtos relacionados: Livro e Capa.
Cada editora (Moderna e Clássica) implementa sua própria versão dos dois produtos, e a fábrica garante que o livro e a capa combinem entre si.

#### Sem Abstract Factory:

```typescript
// Interfaces dos produtos
interface Livro {
  getDescricao(): string;
}

interface Capa {
  getEstilo(): string;
}

// Produtos - Editora Moderna
class LivroModerno implements Livro {
  getDescricao(): string {
    return "Livro de linguagem moderna e objetiva.";
  }
}

class CapaModerna implements Capa {
  getEstilo(): string {
    return "Capa com design minimalista e cores vivas.";
  }
}

// Produtos - Editora Clássica
class LivroClassico implements Livro {
  getDescricao(): string {
    return "Livro com estilo tradicional e linguagem rebuscada.";
  }
}

class CapaClassica implements Capa {
  getEstilo(): string {
    return "Capa com moldura dourada e fontes clássicas.";
  }
}

function montarLivroEditoraModerna() {
  const livro = new LivroModerno();
  const capa = new CapaModerna();

  console.log(livro.getDescricao());
  console.log(capa.getEstilo());
}

function montarLivroEditoraClassica() {
  const livro = new LivroClassico();
  const capa = new CapaClassica();

  console.log(livro.getDescricao());
  console.log(capa.getEstilo());
}

// Teste
montarLivroEditoraModerna();
montarLivroEditoraClassica();

```

#### Com Abstract Factory:

```typescript
interface Livro {
  getDescricao(): string;
}

interface Capa {
  getEstilo(): string;
}

// Produtos - Editora Moderna
class LivroModerno implements Livro {
  getDescricao(): string {
    return "Livro de linguagem moderna e objetiva.";
  }
}

class CapaModerna implements Capa {
  getEstilo(): string {
    return "Capa com design minimalista e cores vivas.";
  }
}

// Produtos - Editora Clássica
class LivroClassico implements Livro {
  getDescricao(): string {
    return "Livro com estilo tradicional e linguagem rebuscada.";
  }
}

class CapaClassica implements Capa {
  getEstilo(): string {
    return "Capa com moldura dourada e fontes clássicas.";
  }
}

// Fábrica abstrata
interface Editora {
  criarLivro(): Livro;
  criarCapa(): Capa;
}

// Fábricas concretas
class EditoraModerna implements Editora {
  criarLivro(): Livro {
    return new LivroModerno();
  }
  criarCapa(): Capa {
    return new CapaModerna();
  }
}

class EditoraClassica implements Editora {
  criarLivro(): Livro {
    return new LivroClassico();
  }
  criarCapa(): Capa {
    return new CapaClassica();
  }
}

function montarLivro(editora: Editora) {
  const livro = editora.criarLivro();
  const capa = editora.criarCapa();

  console.log(livro.getDescricao());
  console.log(capa.getEstilo());
}

// Teste
const moderna = new EditoraModerna();
const classica = new EditoraClassica();

montarLivro(moderna);
montarLivro(classica);
```

### Pontos Fortes:

- Consistência entre objetos criados: Garante que os objetos gerados por uma mesma fábrica sejam compatíveis entre si.

- Baixo acoplamento: O código cliente depende apenas da interface da fábrica, não das implementações concretas.

- Fácil expansão: Adicionar novas famílias de produtos (ex: uma nova editora) não exige alteração no código cliente.

- Ideal para sistemas que mudam de “modo” ou “estilo” completo, como temas visuais, tipos de clientes, idiomas etc.

### Pontos Fracos:

- Mais complexidade estrutural: Requer muitas interfaces e classes para representar fábricas e produtos.

- Manutenção mais trabalhosa em sistemas pequenos: Pode ser exagerado para projetos com poucas variações.

- Dificuldade para adicionar um novo produto à família: Adicionar um novo item (ex: marcador de página) exigiria mudanças em todas as fábricas.