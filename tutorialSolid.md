# Tutorial SOLID: Refatoracao da Missao Marte

Este documento apresenta a ordem recomendada para realizar a refatoracao do
jogo **Missao Marte Unifor**. O codigo-fonte existente deve ser mantido. Este
arquivo reorganiza apenas a leitura e a execucao do tutorial.

## 1. Preparacao

O codigo inicial esta em [src/exercicio10](src/exercicio10). A implementacao
refatorada sera criada pelo aluno a partir dos blocos de codigo deste documento.

Antes de iniciar:

1. Compile e execute o codigo inicial.
2. Teste o menu, uma missao, o ranking e o reset do ranking.
3. Anote os comportamentos que precisam continuar funcionando.
4. Crie ou use um pacote separado para a sua refatoracao.
5. Preserve a versao inicial para comparar o antes e o depois.

O objetivo nao e apenas reorganizar arquivos. A equipe deve identificar os
motivos de mudanca, reduzir acoplamento, justificar as abstracoes e manter o
jogo funcionando.

## 2. Ordem da refatoracao

Siga obrigatoriamente esta ordem:

1. `model`: entidades, interfaces e regras do dominio;
2. `repository`: contrato e persistencia do ranking;
3. `presentation`: exibicao do mapa;
4. `service`: orquestracao do jogo;
5. `Main`: composicao das dependencias e ponto de entrada.

A ordem evita que o projeto fique incompleto por muito tempo. Compile ao final
de cada etapa antes de iniciar a proxima.

## 3. Etapa 1: modelo do dominio

Comece pelas classes que representam os objetos do jogo. O dominio nao deve
conhecer `Scanner`, arquivos ou detalhes de apresentacao.

Crie ou organize os arquivos nesta ordem:

1. `Posicionavel`: contrato para objetos com coordenadas;
2. `Movel`: contrato para objetos que podem se mover;
3. `EntidadeMapa`: classe abstrata comum às entidades do mapa;
4. `Dificuldade`: enum com os niveis da missao;
5. `Passageiro`: classe abstrata com nome, tipo e pontuacao;
6. `Professor`, `Engenheiro` e `Astronauta`;
7. `Asteroide` e `Inimigo`;
8. `Nave`: posicao, vidas, capacidade e passageiros embarcados;
9. `Missao`: nave, passageiros, perigos, embarque e colisoes.

Use os blocos de codigo da secao 12 para criar os arquivos em
`src/solidexercicio10/model`.

### O que observar

- `EntidadeMapa` implementa `Posicionavel`.
- `Passageiro` herda de `EntidadeMapa`.
- Os tipos de passageiros podem ser tratados como `Passageiro`.
- `Nave` e `Inimigo` implementam `Movel`.
- `Missao` possui uma nave e colecoes de entidades.

### Validacao do modelo

```powershell
New-Item -ItemType Directory -Force -Path out | Out-Null
javac -d out (Get-ChildItem -Filter *.java -Path src/solidexercicio10/model | ForEach-Object FullName)
```

### Reflexao sobre o modelo

- Qual e o contrato comum entre as entidades do mapa?
- Por que `Passageiro` e uma classe abstrata?
- As subclasses podem substituir `Passageiro` sem quebrar o comportamento esperado?

## 4. Etapa 2: persistencia do ranking

Crie a camada de ranking depois que o modelo estiver disponivel:

1. `RankingEntry`: dados de uma pontuacao;
2. `RankingRepository`: contrato de salvar, listar e limpar;
3. `RankingService`: persistencia concreta em arquivo.

Use os blocos de codigo da secao 12 para criar os arquivos em
`src/solidexercicio10/repository`.

O servico do jogo devera depender de `RankingRepository`, e nao diretamente
de `RankingService`. Essa decisao aplica o DIP e permite trocar arquivo por
memoria, banco ou outra implementacao.

### Validacao do ranking

```powershell
javac -d out (Get-ChildItem -Recurse -Filter *.java -Path src/solidexercicio10/model,src/solidexercicio10/repository | ForEach-Object FullName)
```

### Reflexao sobre o ranking

- O que muda se a persistencia deixar de ser um arquivo?
- Por que o contrato deve ser uma interface?
- Como testar o ranking sem criar um arquivo real?

## 5. Etapa 3: apresentacao

Crie `presentation/MapaRenderer.java`.

A apresentacao deve somente exibir o estado da missao:

- coordenadas do mapa;
- nave;
- plataforma `L` na origem `(0,0)`;
- passageiros e perigos;
- legenda e comandos.

O renderer nao deve movimentar a nave, calcular pontuacao, embarcar
passageiros ou salvar o ranking.

Use o bloco de codigo da secao 12 para criar
`src/solidexercicio10/presentation/MapaRenderer.java`.

### Validacao da apresentacao

```powershell
javac -d out (Get-ChildItem -Recurse -Filter *.java -Path src/solidexercicio10/model,src/solidexercicio10/presentation | ForEach-Object FullName)
```

### Reflexao sobre a apresentacao

- O que seria necessario alterar para trocar o console por uma interface grafica?
- Por que a regra de embarque nao deve ficar no renderer?

## 6. Etapa 4: servico do jogo

Crie `service/JogoService.java`.

O servico coordena o caso de uso da partida:

- menu e comandos;
- criacao da missao;
- movimentacao, embarque e colisoes;
- pontuacao, movimentos e tempo;
- chamada do renderer;
- chamada do repositorio para salvar o ranking.

As posicoes de passageiros e perigos devem ser sorteadas em todo o mapa, sem
sobreposicao e sem ocupar a origem. A capacidade da nave deve comportar todos
os passageiros da dificuldade escolhida. A contagem deve mostrar passageiros a
bordo, restantes no mapa e total.

Use o bloco de codigo da secao 12 para criar
`src/solidexercicio10/service/JogoService.java`.

### Validacao do servico

```powershell
javac -d out (Get-ChildItem -Recurse -Filter *.java -Path src/solidexercicio10/model,src/solidexercicio10/repository,src/solidexercicio10/presentation,src/solidexercicio10/service | ForEach-Object FullName)
```

Teste pelo menos:

- posicoes aleatorias em mais de uma partida;
- contagem de passageiros;
- plataforma de retorno em `(0,0)`;
- consulta e limpeza do ranking;
- encerramento voluntario e fim de partida.

### Reflexao sobre o servico

- O `JogoService` ainda possui responsabilidades demais?
- A criacao da missao poderia ser extraida para uma `MissaoFactory`?
- O servico depende de abstracoes ou de detalhes concretos?

## 7. Etapa 5: ponto de entrada

Crie `Main.java` por ultimo.

O `Main` deve apenas:

1. exibir a mensagem inicial;
2. criar uma implementacao de `RankingRepository`;
3. criar `JogoService` usando a abstracao;
4. iniciar o loop com `Scanner`.

Use o bloco de codigo da secao 12 para criar `src/solidexercicio10/Main.java`.

### Validacao final

```powershell
Remove-Item -Recurse -Force out -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Force -Path out | Out-Null
javac -d out (Get-ChildItem -Recurse -Filter *.java -Path src/solidexercicio10 | ForEach-Object FullName)
java -cp out solidexercicio10.Main
```

Verifique o menu, uma partida, o ranking, o reset e a saida do jogo.

## 8. Relacao com SOLID

- **SRP:** cada classe possui um motivo principal para mudar.
- **OCP:** novos tipos e implementacoes podem ser adicionados sem alterar
  desnecessariamente o fluxo estavel.
- **LSP:** subclasses de `Passageiro` podem ser usadas no lugar da classe base.
- **ISP:** `Movel` e `Posicionavel` sao contratos pequenos.
- **DIP:** `JogoService` depende de `RankingRepository`.

SOLID nao determina uma unica quantidade de classes. A equipe pode escolher
outra estrutura, desde que explique o problema resolvido e os custos da escolha.

## 9. UML

Entregue dois diagramas:

- diagrama de classes das entidades de `model`;
- diagrama de pacotes do projeto.

Os modelos estao em [docs/uml](docs/uml), nos formatos PlantUML e Mermaid.
Inclua os arquivos-fonte e as imagens ou visualizacoes no repositorio.

## 10. Revisao critica

Crie `REVISAO-SOLID.md` contendo:

- uma observacao sobre cada principio SOLID;
- uma melhoria adicional;
- uma decisao do tutorial com a qual a equipe concorda;
- uma decisao com a qual a equipe discorda, com justificativa;
- testes realizados e resultados;
- prioridade das melhorias.

A revisao deve considerar complexidade, tamanho do projeto, testabilidade e
custo de manutencao.

## 11. Entrega e apresentacao

Publique o projeto em um repositorio individual no GitHub. Preserve o codigo
inicial, inclua a versao refatorada, os diagramas, o README, a revisao e os
commits da evolucao. Envie o link pelo Moodle e adicione
`marcelobezerra-dotcom` como colaborador ou mantenha o repositorio publico.

Todos os integrantes devem participar da apresentacao para a turma. A equipe
devera demonstrar o funcionamento, explicar a arquitetura, apresentar os
diagramas e defender suas decisoes.

## 12. Codigo de referencia para criar a pasta

Os arquivos abaixo sao a referencia completa da solucao. Crie cada arquivo no
caminho indicado. A pasta `src/solidexercicio10` nao precisa existir antes do
inicio: ela sera criada durante as etapas do tutorial.

### Etapa 1: `model`

#### `Posicionavel.java`, `Movel.java` e `Dificuldade.java`

```java
package solidexercicio10.model;

public interface Posicionavel { int getX(); int getY(); }
```

```java
package solidexercicio10.model;

public interface Movel { void mover(int dx, int dy); }
```

```java
package solidexercicio10.model;

public enum Dificuldade {
  FACIL, MEDIO, DIFICIL;
  public static Dificuldade deString(String valor) {
    if (valor == null) return MEDIO;
    return switch (valor.trim().toLowerCase()) {
      case "facil" -> FACIL;
      case "dificil" -> DIFICIL;
      default -> MEDIO;
    };
  }
}
```

#### `EntidadeMapa.java` e `Passageiro.java`

```java
package solidexercicio10.model;

public abstract class EntidadeMapa implements Posicionavel {
  protected int x;
  protected int y;
  protected EntidadeMapa(int x, int y) { this.x = x; this.y = y; }
  public int getX() { return x; }
  public int getY() { return y; }
  public abstract String getSimbolo();
}
```

```java
package solidexercicio10.model;

public abstract class Passageiro extends EntidadeMapa {
  private final String nome;
  private final String tipo;
  protected Passageiro(String nome, String tipo, int x, int y) {
    super(x, y); this.nome = nome; this.tipo = tipo;
  }
  public String getNome() { return nome; }
  public String getTipo() { return tipo; }
  public abstract int getPontuacao();
}
```

#### Passageiros e perigos

```java
package solidexercicio10.model;
public class Professor extends Passageiro {
  public Professor(String n, int x, int y) { super(n, "Professor", x, y); }
  public int getPontuacao() { return 15; }
  public String getSimbolo() { return "P"; }
}
```

```java
package solidexercicio10.model;
public class Engenheiro extends Passageiro {
  public Engenheiro(String n, int x, int y) { super(n, "Engenheiro", x, y); }
  public int getPontuacao() { return 20; }
  public String getSimbolo() { return "E"; }
}
```

```java
package solidexercicio10.model;
public class Astronauta extends Passageiro {
  public Astronauta(String n, int x, int y) { super(n, "Astronauta", x, y); }
  public int getPontuacao() { return 10; }
  public String getSimbolo() { return "T"; }
}
```

```java
package solidexercicio10.model;
public class Asteroide extends EntidadeMapa {
  public Asteroide(int x, int y) { super(x, y); }
  public String getSimbolo() { return "#"; }
}
```

```java
package solidexercicio10.model;
public class Inimigo extends EntidadeMapa implements Movel {
  public Inimigo(int x, int y) { super(x, y); }
  public void mover(int dx, int dy) { x += dx; y += dy; }
  public String getSimbolo() { return "X"; }
}
```

#### `Nave.java`

```java
package solidexercicio10.model;
import java.util.ArrayList;
import java.util.List;

public class Nave extends EntidadeMapa implements Movel {
  private final String nome;
  private final List<Passageiro> passageiros = new ArrayList<>();
  private final int capacidade;
  private int vidas = 3;
  public Nave(String nome, int x, int y, int capacidade) {
    super(x, y); this.nome = nome; this.capacidade = capacidade;
  }
  public String getNome() { return nome; }
  public int getVidas() { return vidas; }
  public int getCapacidade() { return capacidade; }
  public List<Passageiro> getPassageiros() { return passageiros; }
  public void embarcar(Passageiro p) { if (passageiros.size() < capacidade) passageiros.add(p); }
  public void perderVida() { vidas = Math.max(0, vidas - 1); }
  public void mover(int dx, int dy) { x += dx; y += dy; }
  public String getSimbolo() { return "@"; }
  public void moverComLimites(char c, int minX, int maxX, int minY, int maxY) {
    int dx = 0, dy = 0;
    switch (c) { case 'w' -> dy = 1; case 's' -> dy = -1; case 'a' -> dx = -1; case 'd' -> dx = 1; default -> { } }
    int novoX = x + dx, novoY = y + dy;
    if (novoX >= minX && novoX <= maxX && novoY >= minY && novoY <= maxY) { x = novoX; y = novoY; }
  }
}
```

#### `Missao.java`

```java
package solidexercicio10.model;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class Missao {
  private final Nave nave;
  private final List<Passageiro> passageiros = new ArrayList<>();
  private final List<Asteroide> asteroides = new ArrayList<>();
  private final List<Inimigo> inimigos = new ArrayList<>();
  public Missao(Nave nave) { this.nave = nave; }
  public Nave getNave() { return nave; }
  public List<Passageiro> getPassageiros() { return passageiros; }
  public List<Asteroide> getAsteroides() { return asteroides; }
  public List<Inimigo> getInimigos() { return inimigos; }
  public void adicionarPassageiro(Passageiro p) { passageiros.add(p); }
  public void adicionarAsteroide(Asteroide a) { asteroides.add(a); }
  public void adicionarInimigo(Inimigo i) { inimigos.add(i); }
  public Passageiro passagemNaPosicao() {
    for (Passageiro p : passageiros) if (mesmaPosicao(p, nave)) return p;
    return null;
  }
  public boolean embarcarPassageiroNaPosicao() {
    Passageiro p = passagemNaPosicao();
    if (p == null || nave.getPassageiros().size() >= nave.getCapacidade()) return false;
    nave.embarcar(p); passageiros.remove(p); return true;
  }
  public void moverInimigos(Random r, int minX, int maxX, int minY, int maxY) {
    for (Inimigo i : inimigos) {
      int dx = r.nextInt(3) - 1, dy = r.nextInt(3) - 1;
      if (i.getX() + dx >= minX && i.getX() + dx <= maxX && i.getY() + dy >= minY && i.getY() + dy <= maxY) i.mover(dx, dy);
    }
  }
  public boolean verificaColisao() {
    for (Asteroide a : asteroides) if (mesmaPosicao(a, nave)) return true;
    for (Inimigo i : inimigos) if (mesmaPosicao(i, nave)) return true;
    return false;
  }
  public boolean todosEmbarcados() { return passageiros.isEmpty(); }
  private boolean mesmaPosicao(Posicionavel a, Posicionavel b) { return a.getX() == b.getX() && a.getY() == b.getY(); }
}
```

### Etapa 2: `repository`

Crie os tres arquivos abaixo.

#### `RankingEntry.java`

```java
package solidexercicio10.repository;
import solidexercicio10.model.Dificuldade;
public class RankingEntry {
  public final String name; public final int score; public final Dificuldade dificuldade;
  public final int passageirosColetados; public final String dataHora; public final long tempoJogo;
  public RankingEntry(String name, int score, Dificuldade dificuldade, int passageiros, String data, long tempo) {
    this.name = name; this.score = score; this.dificuldade = dificuldade;
    this.passageirosColetados = passageiros; this.dataHora = data; this.tempoJogo = tempo;
  }
}
```

#### `RankingRepository.java`

```java
package solidexercicio10.repository;
import java.util.List;
import solidexercicio10.model.Dificuldade;
public interface RankingRepository {
  void salvar(String nome, int pontos);
  void salvar(String nome, int pontos, Dificuldade dificuldade, int passageiros, long tempo);
  List<RankingEntry> listar();
  void limpar();
}
```

#### `RankingService.java`

```java
package solidexercicio10.repository;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import solidexercicio10.model.Dificuldade;

public class RankingService implements RankingRepository {
  private final Path arquivo;
  public RankingService(String nome) { arquivo = Paths.get(nome); }
  public void salvar(String nome, int pontos) { salvar(nome, pontos, Dificuldade.MEDIO, 0, 0); }
  public void salvar(String nome, int pontos, Dificuldade dificuldade, int passageiros, long tempo) {
    List<String> linhas = new ArrayList<>();
    if (Files.exists(arquivo)) try { linhas = Files.readAllLines(arquivo, StandardCharsets.UTF_8); } catch (IOException ignored) { }
    String data = LocalDateTime.now().format(DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm"));
    linhas.add(nome + "|" + pontos + "|" + dificuldade + "|" + passageiros + "|" + data + "|" + tempo);
    try {
      Path parent = arquivo.getParent();
      if (parent != null) Files.createDirectories(parent);
      Files.write(arquivo, linhas, StandardCharsets.UTF_8);
    } catch (IOException e) { throw new IllegalStateException("Falha ao salvar ranking", e); }
  }
  public List<RankingEntry> listar() {
    List<RankingEntry> ranking = new ArrayList<>();
    if (!Files.exists(arquivo)) return ranking;
    try {
      for (String linha : Files.readAllLines(arquivo, StandardCharsets.UTF_8)) {
        String[] p = linha.split("\\|", -1);
        if (p.length < 6) continue;
        try { ranking.add(new RankingEntry(p[0], Integer.parseInt(p[1]), Dificuldade.deString(p[2]), Integer.parseInt(p[3]), p[4], Long.parseLong(p[5]))); }
        catch (NumberFormatException ignored) { }
      }
    } catch (IOException ignored) { return ranking; }
    ranking.sort(Comparator.comparingInt((RankingEntry e) -> e.score).reversed());
    return ranking;
  }
  public void limpar() { try { Files.deleteIfExists(arquivo); } catch (IOException e) { throw new IllegalStateException(e); } }
}
```

### Etapa 3: `presentation`

Crie `MapaRenderer.java`:

```java
package solidexercicio10.presentation;
import solidexercicio10.model.Missao;
public class MapaRenderer {
  public void desenhar(Missao m, int pontos, String piloto, int minX, int maxX, int minY, int maxY) {
    System.out.printf("%nMapa | Pontos: %d | Piloto: %s%n", pontos, piloto);
    for (int y = maxY; y >= minY; y--) {
      System.out.printf("%3d|", y);
      for (int x = minX; x <= maxX; x++) System.out.printf(" %2c", simbolo(m, x, y));
      System.out.println();
    }
    System.out.println("Legenda: @=Nave, L=Plataforma, P=Professor, E=Engenheiro, T=Astronauta, #=Asteroide, X=Inimigo, .=Vazio");
    System.out.println("Comandos: w/s/a/d (mover), c (embarcar), q (sair)");
  }
  private char simbolo(Missao m, int x, int y) {
    if (m.getNave().getX() == x && m.getNave().getY() == y) return '@';
    if (x == 0 && y == 0) return 'L';
    for (var p : m.getPassageiros()) if (p.getX() == x && p.getY() == y) return p.getSimbolo().charAt(0);
    for (var a : m.getAsteroides()) if (a.getX() == x && a.getY() == y) return '#';
    for (var i : m.getInimigos()) if (i.getX() == x && i.getY() == y) return 'X';
    return '.';
  }
}
```

### Etapa 4: `service`

Crie `JogoService.java`. Este arquivo coordena o menu, a criacao aleatoria da
missao, o embarque, a contagem, a apresentacao e o ranking. Para manter o
tutorial legivel, a implementacao completa esta no bloco a seguir.

```java
package solidexercicio10.service;
import java.util.*;
import solidexercicio10.model.*;
import solidexercicio10.presentation.MapaRenderer;
import solidexercicio10.repository.*;

public class JogoService {
  private final RankingRepository ranking; private final MapaRenderer renderer = new MapaRenderer(); private final Random random = new Random();
  public JogoService(RankingRepository ranking) { this.ranking = ranking; }
  public void executarLoop(Scanner s) {
    boolean ativo = true;
    while (ativo) {
      System.out.println("\n1. Nova missao\n2. Ranking\n3. Limpar ranking\n4. Sair");
      String op = ler(s, "Opcao: ", "4");
      switch (op) {
        case "1" -> jogar(s); case "2" -> exibirRanking();
        case "3" -> { ranking.limpar(); System.out.println("Ranking limpo."); }
        case "4" -> ativo = false; default -> System.out.println("Opcao invalida.");
      }
    }
  }
  private void jogar(Scanner s) {
    String nome = ler(s, "Piloto: ", "Piloto");
    Dificuldade dificuldade = Dificuldade.deString(ler(s, "Dificuldade: ", "medio"));
    int tamanho;
    try { tamanho = Integer.parseInt(ler(s, "Tamanho: ", "5")); } catch (NumberFormatException e) { tamanho = 5; }
    tamanho = Math.max(1, tamanho);
    int min = -tamanho, max = tamanho, total = dificuldade == Dificuldade.FACIL ? 4 : 5;
    if (dificuldade == Dificuldade.DIFICIL) total = 6;
    Nave nave = new Nave("A-1", 0, 0, total); Missao missao = new Missao(nave);
    colocar(missao, total, min, max, true, nave); colocar(missao, dificuldade == Dificuldade.DIFICIL ? 3 : 2, min, max, false, nave);
    colocarPerigos(missao, dificuldade == Dificuldade.DIFICIL ? 3 : 2, min, max, nave);
    int pontos = dificuldade == Dificuldade.FACIL ? 30 : dificuldade == Dificuldade.DIFICIL ? 15 : 20;
    int movimentos = 0; long inicio = System.currentTimeMillis(); ler(s, "Enter para iniciar", "");
    while (true) {
      renderer.desenhar(missao, pontos, nome, min, max, min, max);
      System.out.printf("A bordo: %d/%d | Restantes: %d | Total: %d%n", nave.getPassageiros().size(), nave.getCapacidade(), missao.getPassageiros().size(), nave.getPassageiros().size() + missao.getPassageiros().size());
      char c = ler(s, "Comando: ", "q").toLowerCase().charAt(0);
      if (c == 'q') return;
      if (c == 'c') { Passageiro p = missao.passagemNaPosicao(); if (p != null && missao.embarcarPassageiroNaPosicao()) pontos += p.getPontuacao(); }
      else if ("wsad".indexOf(c) >= 0) { nave.moverComLimites(c, min, max, min, max); pontos--; movimentos++; }
      missao.moverInimigos(random, min, max, min, max);
      if (missao.verificaColisao()) nave.perderVida();
      if (pontos <= 0 || nave.getVidas() == 0) return;
      if (missao.todosEmbarcados() && nave.getX() == 0 && nave.getY() == 0) {
        long tempo = (System.currentTimeMillis() - inicio) / 1000;
        System.out.printf("Missao concluida. Pontos: %d, movimentos: %d, tempo: %ds%n", pontos, movimentos, tempo);
        ranking.salvar(nome, pontos, dificuldade, nave.getPassageiros().size(), tempo); return;
      }
    }
  }
  private void colocar(Missao m, int quantidade, int min, int max, boolean passageiro, Nave n) {
    for (int i = 0; i < quantidade; i++) { int[] p = livre(m, min, max, n); if (passageiro) { if (i % 3 == 0) m.adicionarPassageiro(new Professor("Professor", p[0], p[1])); else if (i % 3 == 1) m.adicionarPassageiro(new Engenheiro("Engenheiro", p[0], p[1])); else m.adicionarPassageiro(new Astronauta("Astronauta", p[0], p[1])); } }
  }
  private void colocarPerigos(Missao m, int quantidade, int min, int max, Nave n) { for (int i = 0; i < quantidade; i++) { int[] p = livre(m, min, max, n); m.adicionarInimigo(new Inimigo(p[0], p[1])); } }
  private int[] livre(Missao m, int min, int max, Nave n) { int x, y; do { x = random.nextInt(max - min + 1) + min; y = random.nextInt(max - min + 1) + min; } while ((x == 0 && y == 0) || ocupado(m, x, y)); return new int[] { x, y }; }
  private boolean ocupado(Missao m, int x, int y) { for (Passageiro p : m.getPassageiros()) if (p.getX() == x && p.getY() == y) return true; for (Inimigo i : m.getInimigos()) if (i.getX() == x && i.getY() == y) return true; return false; }
  private void exibirRanking() { for (RankingEntry e : ranking.listar()) System.out.printf("%s: %d%n", e.name, e.score); }
  private String ler(Scanner s, String mensagem, String padrao) { System.out.print(mensagem); if (!s.hasNextLine()) return padrao; String valor = s.nextLine(); return valor.isBlank() ? padrao : valor; }
}
```

### Etapa 5: `Main`

```java
package solidexercicio10;
import java.util.Scanner;
import solidexercicio10.repository.RankingRepository;
import solidexercicio10.repository.RankingService;
import solidexercicio10.service.JogoService;

public class Main {
  public static void main(String[] args) {
    RankingRepository repository = new RankingService("ranking-solid-exercicio10.json");
    JogoService jogo = new JogoService(repository);
    try (Scanner scanner = new Scanner(System.in)) { jogo.executarLoop(scanner); }
  }
}
```

Depois de criar todos os arquivos, execute a validacao final da secao 7.
