# Checkpoint 4 — Bug Hunt StreamFIAP

> Copie este arquivo para a raiz do seu repositório com o nome **README.md**
> e preencha todas as seções.

## Identificação

**Grupo:** **404Squad**

| Integrante | RM | Turma |
2CCPX
Claus Moreira Rm 565503
Julia Lopes Rm 566557
Guilherme Martins Rm 566570
Gabriel Rodrigues Rm 566475

| Campo | |
|---|---|
| **Total de bugs corrigidos** | 07 / 12 |
| **Total de ajustes de Clean Code** | 01 / 6 |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | o codigo permitia minutos menores que 0 | adicionamos uma validacao que impede ser menor que 0 (27-55~56 conteudo.java)| agora o construtor usa setDuracaoMinutos | Encapisulamento
| bug02 | o codigo nao aplicava a regra de descontos em filmes | a conta por tras do desconto na realidade deixava 20% mais caro (25 filme.java)| ajustado o valor para 0.8 |
logica matematica
| bug03 | sistema cobrava o preco em documentarios | por padrao existia uma declaracao para cobrar 9.50 de cada item por padrao (32~34 conteudo.java)| transforma o "calcularPrecoAluguel" para abstract | heranca 
| bug04 | documentario sem preco definido | agora que existe a exigencia de um preco por "Override" o codigo nao compila (documentario.java)| introducao do override e validacao do valor 0.0 assim como no sistema de filme e serie | heranca
| bug05 | construtor de classe "serie" nao trabalhava com superclasses | diferente dos outros construtores assim deixando informacoes incompletas (13 serie.java)|recebia informacoes mas nao as guardava por completo | super classes 
| bug06 | erro seguindo a logica do bug2 + Serie sobrecarregava calcularPrecoAluguel(double) em vez de sobrescrever| estava irregular pelos novos requisitos abstract de override de preco (13~15 serie.java)|adicionado @override garantido o preco 4.90 | assinatura de metodos  
| bug07 | ajusta os cadastrarfilme/serie/documentario para usar os novos getters | os acessos usavam apenas os 5 argumentos originais faltando o 6 (62-69-73-78 conteudoController.java)|garante o acesso de duracaoMinutos em documentario e nos outros construtores|Encapsulamento e logica de construtores.
| bug08 | | | | |
| bug09 | | | | |
| bug10 | | | | |
| bug11 | | | | |
| bug12 | | | | |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | (16 em conteudo.java)| encapsulaao incorreta e vuneravel | alteramos o campo para private. 
| clean02 | | | |
| clean03 | | | |
| clean04 | | | |
| clean05 | | | |
| clean06 | | | |

---

## Parte 3 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do projeto
> como exemplo**. Respostas genéricas de tutorial não pontuam.

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

### 3. Exceções checked vs unchecked (Aula 11)
A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)
Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

### 6. Abstração e interface (Aulas 8 e 9)
`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

Conteudo é abstrata porque define o que conteúdo tem que ser todo Filme, Serie e Documentario herda os atributos básicos e é obrigado a implementar calcularPrecoAluguel() (agora que é abstract). Já Promocionavel é uma interface porque representa algo opcional, nem todo conteúdo entra em promoção. Por isso Filme e Serie fazem implements Promocionavel, e Documentario simplesmente não implementa nada — o que já bate com o contrato.

Se Documentario passasse a ter promoção, a mudança seria mínima, basta adicionar implements Promocionavel nele e escrever seu aplicarPromocao(double preco), igual Filme/Serie já fazem. Nada mais mudaria— Conteudo.java e Promocionavel.java ficam intactos, porque calcularPrecoPromocional() já checa instanceof Promocionavel de forma genérica, sem saber qual subclasse é.

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
