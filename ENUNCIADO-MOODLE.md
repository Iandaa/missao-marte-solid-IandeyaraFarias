# Atividade: Refatoração SOLID do jogo Missão Marte Unifor

## Contexto

O jogo **Missão Marte Unifor** é um jogo de console em Java no qual o piloto controla uma nave, resgata passageiros, evita perigos e registra sua pontuação em um ranking.

A versão inicial funciona, mas concentra diferentes responsabilidades em poucas classes. Essa organização dificulta a manutenção, a realização de testes e a inclusão de novas funcionalidades.

Nesta atividade, você deverá analisar o código existente e refatorá-lo aplicando os princípios SOLID.

## Objetivos de aprendizagem

Ao concluir a atividade, você deverá ser capaz de:

- identificar problemas de responsabilidade e acoplamento em um código existente;
- aplicar SRP, OCP, LSP, ISP e DIP em uma situação prática;
- separar domínio, serviço, apresentação e persistência;
- preservar o comportamento de uma aplicação durante uma refatoração;
- justificar decisões de projeto com argumentos técnicos;
- representar a estrutura do sistema usando diagramas UML;
- realizar uma revisão crítica, reconhecendo que SOLID não fornece uma única solução obrigatória.

## Código inicial

Utilize o código disponível na pasta `src/exercicio10` deste repositório.

Antes de iniciar a refatoração:

1. compile o projeto;
2. execute o jogo;
3. teste o menu, uma missão, o ranking e o reset do ranking;
4. registre quais comportamentos precisam continuar funcionando.

Mantenha o código inicial preservado para permitir a comparação entre as duas versões.

## Tarefa

Crie uma versão refatorada do jogo em um pacote separado, preferencialmente `solidexercicio10`, seguindo o tutorial disponível em `src/README.md`.

A nova versão deve manter o comportamento principal do jogo e organizar o código em responsabilidades menores. Como referência, o tutorial propõe as seguintes camadas:

- `Main`: ponto de entrada e composição das dependências;
- `service`: fluxo e regras da partida;
- `model`: entidades e regras do domínio;
- `presentation`: renderização do mapa e informações da partida;
- `repository`: contrato e implementação do ranking.

A estrutura sugerida não é obrigatória. Caso escolha uma organização diferente, explique no relatório por que ela é adequada ao problema.

## Requisitos funcionais

A versão refatorada deve:

- iniciar uma nova missão pelo menu;
- permitir escolher o piloto, a dificuldade e o tamanho do mapa;
- permitir movimentar a nave e embarcar passageiros;
- detectar colisões e encerramento da missão;
- exibir estatísticas ao final da partida;
- consultar o ranking;
- permitir resetar o ranking;
- persistir as pontuações;
- compilar e executar sem erros.

## Requisitos de projeto

Na refatoração, procure demonstrar:

- **SRP:** separação entre regras do jogo, apresentação e persistência;
- **OCP:** possibilidade de adicionar novos tipos de passageiros ou novas implementações sem modificar desnecessariamente o fluxo principal;
- **LSP:** uso correto de subclasses de `Passageiro` no lugar da classe base;
- **ISP:** interfaces pequenas, com operações relacionadas e utilizadas pelos clientes;
- **DIP:** dependência de abstrações, especialmente na persistência do ranking.

Não crie classes ou interfaces apenas para aumentar a quantidade de arquivos. Cada abstração deve resolver um problema identificável.

## Modelagem UML obrigatória

Além do código, cada equipe deverá produzir dois diagramas UML para documentar
a solução refatorada.

### 1. Diagrama de classes do domínio

Crie um diagrama de classes contendo as principais entidades do pacote
`solidexercicio10.model`, incluindo, quando existirem na solução:

- `EntidadeMapa`;
- `Passageiro` e suas subclasses;
- `Nave`;
- `Missao`;
- `Asteroide` e `Inimigo`;
- `Dificuldade`;
- `Posicionavel` e `Movel`.

O diagrama deve representar, quando aplicável:

- classes abstratas, classes concretas e enumerações;
- atributos e métodos públicos relevantes;
- herança e realização de interfaces;
- associações entre as entidades;
- composição ou agregação, quando fizer parte da decisão de projeto;
- multiplicidades relevantes, como uma `Missao` possuir uma `Nave` e listas de
   passageiros, asteroides e inimigos.

### 2. Diagrama de pacotes do projeto

Crie um diagrama de pacotes representando a organização da versão refatorada,
incluindo, quando presentes:

- `solidexercicio10`;
- `solidexercicio10.model`;
- `solidexercicio10.service`;
- `solidexercicio10.presentation`;
- `solidexercicio10.repository`.

Mostre as dependências entre os pacotes e indique a direção principal dessas
dependências. O diagrama deve permitir perceber que o serviço depende do
contrato `RankingRepository`, e não diretamente dos detalhes de persistência.

Os diagramas podem ser feitos com PlantUML, Mermaid, draw.io, StarUML,
Lucidchart ou outra ferramenta equivalente. Entregue também o arquivo-fonte do
diagrama, preferencialmente como `*.puml`, `*.mmd` ou `*.drawio`, para que o
material possa ser revisado e atualizado.

Salve os arquivos em uma pasta `docs/uml/` com nomes semelhantes a:

```text
docs/uml/diagrama-classes-model.puml
docs/uml/diagrama-classes-model.png
docs/uml/diagrama-pacotes.puml
docs/uml/diagrama-pacotes.png
```

Inclua no `README.md` do repositório uma imagem ou link para cada diagrama e
uma breve explicação das decisões representadas. Caso sua estrutura seja
diferente da sugerida pelo tutorial, os diagramas devem refletir a estrutura
real implementada.

## Revisão crítica obrigatória

Depois de concluir a implementação, faça uma revisão do próprio código. Você deverá analisar se todas as decisões do tutorial realmente fazem sentido para este projeto.

Crie o arquivo `REVISAO-SOLID.md` contendo:

1. pelo menos uma observação relacionada a cada princípio SOLID;
2. pelo menos uma melhoria adicional que poderia ser feita;
3. pelo menos uma decisão do tutorial com a qual você concorda, explicando o benefício;
4. pelo menos uma decisão com a qual você não concorda ou que implementaria de outra forma, apresentando justificativa;
5. os testes realizados e os respectivos resultados;
6. a prioridade de cada melhoria encontrada: alta, média ou baixa.

Para cada observação, utilize o seguinte formato:

```text
Local: classe, método ou arquivo
Princípio relacionado:
Observação:
Impacto para manutenção, testes ou evolução:
Proposta:
Prioridade:
```

Discordar de uma decisão não significa apontar um erro automaticamente. A justificativa deve considerar o tamanho do projeto, a complexidade introduzida, a facilidade de testes e o custo de manutenção.

## Apresentação da solução

Além da entrega no GitHub, cada equipe deverá apresentar a solução para a
turma. A apresentação deve ser objetiva e demonstrar:

- o funcionamento do jogo antes e depois da refatoração;
- as principais decisões de organização do código;
- exemplos de aplicação dos princípios SOLID;
- uma melhoria identificada na revisão crítica;
- uma decisão do tutorial com a qual a equipe concorda ou discorda;
- os testes realizados e eventuais limitações da solução.

Todos os integrantes devem participar da apresentação e estar preparados para
responder perguntas sobre o código e as decisões da equipe.

## Entrega pelo GitHub

A entrega deverá ser feita por meio de um repositório individual no GitHub. Não
envie arquivo compactado como entrega principal.

1. Crie um repositório para a atividade, preferencialmente com um nome como
   `missao-marte-solid-seu-nome`.
2. Publique no repositório o código inicial preservado e a versão refatorada.
3. Inclua o arquivo `REVISAO-SOLID.md` e o `README.md` da entrega.
4. Faça commits durante o desenvolvimento, identificando etapas como análise,
   refatoração, testes e revisão final.
5. Envie no Moodle o link completo do repositório e confirme que ele está
   público ou adicione o usuário `marcelobezerra-dotcom` como colaborador.

O repositório deve conter:

- o código inicial preservado para comparação;
- a versão refatorada;
- o arquivo `REVISAO-SOLID.md`;
- a pasta `docs/uml/` com os diagramas UML e seus arquivos-fonte;
- um arquivo `README.md` com instruções de compilação e execução;
- opcionalmente, testes automatizados ou evidências dos testes manuais realizados.

O `README.md` do repositório deve informar:

- nome do aluno;
- como compilar;
- como executar;
- quais alterações foram realizadas;
- quais decisões de projeto foram tomadas;
- quais limitações permanecem.
- onde encontrar os diagramas UML e o que eles representam.

O link enviado no Moodle deve permitir que o professor consulte os arquivos e
o histórico de commits até a data limite da atividade. Alterações posteriores
podem ser consideradas apenas se autorizadas pelo professor.

## Critérios de avaliação

### Valor total: 10,0 pontos

| Critério | Pontuação |
| --- | ---: |
| Funcionamento dos requisitos do jogo | 2,0 |
| Aplicação e justificativa de SRP e separação de responsabilidades | 1,5 |
| Aplicação e justificativa de OCP e LSP | 1,0 |
| Aplicação e justificativa de ISP e DIP | 1,0 |
| Organização, legibilidade e instruções de execução | 0,5 |
| Revisão crítica e identificação de melhorias | 1,5 |
| Qualidade dos testes e evidências apresentadas | 0,5 |
| Apresentação da solução para a turma | 1,0 |
| Diagramas UML e coerência com o código | 1,0 |

Uma solução que apenas reorganiza arquivos, sem justificar as decisões, sem
preservar o funcionamento do jogo ou sem realizar a apresentação, não atende ao
objetivo completo da atividade.

## Perguntas orientadoras

Durante a atividade, reflita sobre as seguintes questões:

- Quais motivos diferentes faziam a classe original mudar?
- O que seria necessário alterar para trocar o arquivo de ranking por um banco de dados ou por uma implementação em memória?
- Como adicionar um novo tipo de passageiro sem modificar a lógica principal?
- As subclasses de `Passageiro` respeitam o contrato esperado pela classe base?
- Alguma interface criada possui métodos que seus clientes não utilizam?
- A arquitetura proposta está adequada ao tamanho deste projeto ou introduz complexidade desnecessária?
- Qual melhoria você implementaria primeiro em uma próxima versão?

## Observação

SOLID é um conjunto de princípios para orientar decisões de design, não uma receita única. O mais importante nesta atividade é conseguir explicar o problema identificado, a solução escolhida e os impactos dessa escolha.
