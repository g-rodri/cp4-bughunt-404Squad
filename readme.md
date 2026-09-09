# Checkpoint 4 — Bug Hunt StreamFIAP

## Identificação

**Grupo:** **404Squad**

| Integrante | RM | Turma |
|---|---|---|
| Claus Moreira | 565503 | 2CCPX |
| Julia Lopes | 566557 | 2CCPX |
| Guilherme Martins | 566570 | 2CCPX |
| Gabriel Rodrigues | 566475 | 2CCPX |

| Campo | |
|---|---|
| **Total de bugs corrigidos** | 12 / 12 |
| **Total de ajustes de Clean Code** | 6 / 6 |

---

## Parte 1 — Bugs encontrados

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | o codigo permitia minutos menores que 0 | adicionamos uma validacao que impede ser menor que 0 (27-55~56 conteudo.java)| agora o construtor usa setDuracaoMinutos | Encapisulamento
| bug02 | o codigo nao aplicava a regra de descontos em filmes | a conta por tras do desconto na realidade deixava 20% mais caro (25 filme.java)| ajustado o valor para 0.8 |
logica matematica
| bug03 | sistema cobrava o preco em documentarios | por padrao existia uma declaracao para cobrar 9.90 de cada item por padrao (32~34 conteudo.java)| transforma o "calcularPrecoAluguel" para abstract | heranca 
| bug04 | documentario sem preco definido | agora que existe a exigencia de um preco por "Override" o codigo nao compila (documentario.java)| introducao do override e validacao do valor 0.0 assim como no sistema de filme e serie | heranca
| bug05 | construtor de classe "serie" nao trabalhava com superclasses | diferente dos outros construtores assim deixando informacoes incompletas (13 serie.java)|recebia informacoes mas nao as guardava por completo | super classes 
| bug06 | erro seguindo a logica do bug2 + Serie sobrecarregava calcularPrecoAluguel(double) em vez de sobrescrever| estava irregular pelos novos requisitos abstract de override de preco (13~15 serie.java)|adicionado @override garantido o preco 4.90 | assinatura de metodos  
| bug07 | ajusta os cadastrarfilme/serie/documentario para usar os novos getters | os acessos usavam apenas os 5 argumentos originais faltando o 6 (62-69-73-78 conteudoController.java)|garante o acesso de duracaoMinutos em documentario e nos outros construtores|Encapsulamento e logica de construtores.
| bug08 | usuário salvava com o nome vazio | construtor fazia `nome = nome` (auto-atribuição), o campo ficava null (22 usuario.java) | troquei pra `this.nome = nome` | this / escopo de variável |
| bug09 | usuário salvava sem id | o `@Id` não tinha `@GeneratedValue`, o banco não gerava id (11-12 usuario.java) | adicionei `@GeneratedValue(strategy = IDENTITY)` igual no conteudo | geração de id no JPA |
| bug10 | usuário sem crédito conseguia alugar (saldo ficava negativo) e quem tinha crédito era recusado | `temCreditosSuficientes` comparava invertido: `preco >= creditos` (28 usuario.java) | troquei pra `creditos >= preco` | lógica de comparação / regra de negócio |
| bug11 | conteúdo indisponível era alugado do mesmo jeito | o `alugar` nunca checava `isDisponivel`; a exceção existia mas nunca era lançada (36 usuario.java) | adicionei `if(!c.isDisponivel())` lançando ConteudoIndisponivelException | regra de negócio / exceção customizada |
| bug12 | buscar conteúdo inexistente devolvia vazio como se fosse sucesso | `buscarPorId` tinha catch vazio que engolia a exceção e retornava null (31-40 conteudocontroller.java) | tirei o try/catch, a exceção sobe pro GlobalExceptionHandler e vira 404 | exceções / anti-pattern do catch vazio |
| bug13 | buscar por categoria devolvia lista vazia | comparava String com `==` em vez de `equals` (47 conteudocontroller.java) | troquei por `equals` (depois virou findByCategoria no clean06) | == vs equals em String |
| bug14 | aluguel de menor de idade virava erro 500 sem mensagem pro cliente | a ClassificacaoIndicativaException é checked e não tinha handler no GlobalExceptionHandler | adicionei um `@ExceptionHandler` retornando 422 com a mensagem da regra | exceções checked vs unchecked |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | (16 em conteudo.java)| encapsulaao incorreta e vuneravel | alteramos o campo para private. 
| clean02 | método calcularDescontoAntigo no conteudocontroller.java | código morto (ninguém chamava) | apaguei o método |
| clean03 | bloco de cupom comentado no fim do conteudocontroller.java | código comentado acumulado (o git já guarda o histórico) | apaguei as linhas comentadas |
| clean04 | comentário do debitarCreditos (usuario.java) | comentário tem que refletir o código (dizia "adiciona" mas subtrai) | corrigi pra "debita o valor" |
| clean05 | variável `p` no método alugar (usuario.java) | nome de variável sem significado | renomeei `p` pra `preco` |
| clean06 | listarPorCategoria fazia um `for` na mão (conteudocontroller.java) | reinventava o que o Spring Data já resolve | troquei pelo `conteudoRepository.findByCategoria(categoria)` |

---

## Parte 3 — Perguntas de reflexão

### 1. Injeção de dependência (Aula 13)
Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
injetar um bean, e por que isso não funcionaria com um `new` comum?

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)
Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

RESPOSTA:
No ProdutoDAO da Aula 12, cada operação exigia escrever manualmente Connection, PreparedStatement, ResultSet e fechar tudo direito pra não vazar recursos em querys. O ConteudoRepository estende JpaRepository, e o Spring Data JPA já entrega CRUD completo (save, findById, findAll, delete) sem implementar nada, porque ele gera a implementação em tempo de execução por trás dos panos. O findByCategoria funciona do mesmo jeito o Spring interpreta o nome do método ("find By Categoria") e monta a query automaticamente, sem precisar escrever SQL.

### 3. Exceções checked vs unchecked (Aula 11)
A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

RESPOSTA:
extends Exception cria uma exceção checked o Java obriga a tratar ou declarar em todo método no caminho, senão não compila. enquanto extends RuntimeException cria uma unchecked pode ser lançada sem essa obrigação, ClassificacaoIndicativaException era checked mas não tinha handler no GlobalExceptionHandler, então o Spring não sabia o que fazer com ela a correção foi adicionar um @ExceptionHandler(ClassificacaoIndicativaException.class) retornando 422 com a mensagem da exceção no corpo da resposta assim o GlobalExceptionHandler intercepta e transforma o erro interno numa resposta clara pro cliente, em vez de deixar ele estourar cru.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

RESPOSTA:
(override) é quando o método filho tem exatamente a mesma assinatura do método da classe-mãe. Já (overload) é quando o nome é igual mas os parâmetros são diferentes. no bug06: a classe-mãe tinha calcularPrecoAluguel() sem parâmetro, mas Serie declarava calcularPrecoAluguel(double desconto) o original continuava ativo por trás e, como não era abstrato ainda (bug02), Serie herdava o R$9,90 padrão sem ninguém notar. Por isso compilava liso. O @Override resolve forçando o compilador a checar se a assinatura bate com algo lá Se não bater, ele acusa erro na hora.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)
Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

RESPOSTA:
Formato do dado (setter/construtor): se o valor em si já nasce errado, tipo duracaoMinutos <= 0 (bug01), a checagem fica no setter. E o construtor precisa usar esse setter que não havia sido atribuido de forma correta, tinha validação só numa porta de entrada, e a outra (construtor) passava reto. Por isso foi necessário multiplas validações
Regra de negócio (método do model): quando a validação depende do momento, tipo créditos vs. preço do aluguel (bug10) ou se o conteúdo está disponível (bug11), não dá pra travar isso no construtor — o valor é válido isoladamente, o problema é o contexto na hora da ação. Por isso essas checagens ficam dentro do alugar(), não em setters.

### 6. Abstração e interface (Aulas 8 e 9)
`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

RESPOSTA:
Conteudo é uma classe abstrata que serve de base para garantir os atributos comuns e forçar o calcularPrecoAluguel(), enquanto Promocionavel é uma interface usada apenas para comportamentos opcionais que nem todo item possui. Se o documentário passasse a ter promoções, a mudança seria bem simples: bastaria adicionar implements Promocionavel nele e escrever a lógica do método, deixando Conteudo e a interface totalmente intactos.Isso mostra que o design do sistema permite estender comportamentos de forma flexivel sem precisar mexer na estrutura base que já funciona.

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
