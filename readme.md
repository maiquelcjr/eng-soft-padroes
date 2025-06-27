### Trabalho de Engenharia de Software II - Padrões de Desenvolvimento
**Integrantes**: Alberto Parker, Anthony Pereira, Eduardo Leal, Maiquel Junior e Vitor Manoel

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

O padrão Factory Method tem como objetivo encapsular a criação de objetos, delegando essa responsabilidade a subclasses. Em vez de instanciar objetos diretamente com new, o Factory Method define um método de fábrica que pode ser sobrescrito para determinar qual tipo de objeto será criado.

Isso permite que o código principal não precise conhecer a classe concreta que está sendo instanciada, promovendo desacoplamento e maior flexibilidade para extensão.

### Quando devemos Utilizar:

É possível utilizar o Factory Method quando:

- Você precisa instanciar objetos de forma flexível, mas não quer acoplar seu código à classe concreta.
- O sistema precisa ser facilmente extensível com novas variantes de produtos, sem alterar o código cliente.
- Você quer delegar a responsabilidade de criação de objetos a subclasses, mantendo o código genérico.

### Estrutura:

No código abaixo, foi utilizado o padrão Factory Method para permitir a criação de diferentes tipos de livros (LivroFisico e LivroDigital) por meio de uma interface comum.
O método criarLivro() é a fábrica que encapsula a lógica de criação, permitindo que as subclasses definam qual objeto específico será instanciado.
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

- Desacoplamento entre criação e uso: O código cliente não precisa conhecer as classes concretas.

- Facilidade para extensão: Novos tipos de produtos podem ser adicionados sem alterar o código que os consome.

- Aplica o princípio aberto/fechado (OCP): Permite extensão do sistema sem modificar código existente.

- Organização clara: Torna o sistema mais modular e fácil de manter.

### Pontos Fracos:

- Aumento na complexidade: Requer mais classes e estruturas do que instanciar diretamente com new.

- Excesso de abstração em sistemas simples: Pode parecer desnecessário para projetos pequenos ou com poucas variações de objeto.

- Sobrecarga inicial: Pode ser difícil entender e aplicar corretamente se mal planejado.

