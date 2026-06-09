# Resumo de uma conversa com Codex sobre formatos
Terça, 9 de junho de 2026
20h24 minutos

---

Vamos criar um novo formato inspirado em [Web Stories](https://amp.dev/about/stories), mas sem AMP.

A referência do repositório Web Stories WP serve para entender arquitetura, não para copiar o formato.

A ideia central é separar:

1. Editor
2. Modelo rico
3. Renderer
4. Player/Runtime
5. Export/Embed

### Editor
Onde a pessoa cria a experiência.

### Modelo rico

O “arquivo fonte” salvo pelo editor. Não é HTML final. Descreve cards/cenas, elementos, mídia, posições, estilos, animações, interações, navegação e estado.

Renderer:
Transforma o modelo rico em tela visual. Monta cada card/cena com textos, imagens, vídeos, botões, camadas, estilos, animações e assets.

Player/Runtime:
Executa a experiência. Controla avanço, volta, pausa, autoplay, gestos, transições, escolhas, estado, branching, histórico, replay e analytics.

Export/Embed:
Saídas adicionais. Pode gerar pacote embutível, imagem para preview, thumbnail, ou vídeo para TikTok/Reels/Shorts.

Decisão técnica recomendada:
Web-first e híbrido.

Usar DOM/HTML/CSS/React como base principal do runtime, com Canvas/WebGL/Three.js como camadas nativas opcionais quando necessário.

Motivo:
- DOM/React é melhor para texto real, botões, acessibilidade, links, formulários, responsividade, embed e integração com CMS.
- Canvas é ótimo para efeitos 2D, partículas, mini-games e renderização controlada.
- WebGL/Three.js é ótimo para efeitos premium, vidro/refração, profundidade, 3D, transições sofisticadas e linguagem visual tipo Liquid Glass.
- Vídeo deve ser saída/export, não runtime principal.
- Imagem estática serve para preview/capa.
- Pacote embed serve para publicar fora do CMS.

Não começar como motor 100% Canvas/WebGL por padrão, porque isso dificulta texto real, acessibilidade, links, foco, teclado, leitor de tela, responsividade, SEO, embed e manutenção.

Mas Canvas/WebGL devem existir desde o começo como parte nativa do formato, para permitir ideias fora da caixa.

Exemplo de modos de card:
- card comum: DOM/React
- card cinematográfico: WebGL
- card mini-game: Canvas/WebGL
- card exportável: render pass para vídeo

## Pipeline recomendado:

### Modelo rico

  -> Renderer DOM/React para UI, texto, botões, layout e acessibilidade
  -> Camadas Canvas/WebGL opcionais para efeitos especiais
  -> Player/runtime controlando navegação e estado
  -> Exporters opcionais: preview, imagem, vídeo, pacote embed

### Sobre vídeo

Export de vídeo é muito desejável e deve entrar como fase/feature estratégica.

A experiência original continua sendo interativa no player web, mas pode ser renderizada como vídeo linear.

O exportador de vídeo pode gerar:
- vídeo 9:16 para TikTok/Reels/Shorts
- teaser
- versão 15s/30s/60s
- thumbnail/capa
- sequência padrão
- percurso específico dentro do grafo
- variações automáticas

Para exportar vídeo de uma experiência com grafo, o sistema precisa escolher um percurso:

intro -> mesa -> chave -> porta -> final_bom

ou usar uma sequência editorial fixa.

### Navegação:
Queremos abandonar a lógica puramente linear dos stories:

1 -> 2 -> 3 -> 4

e pensar em grafo/estado entre cards.

Grafo:
Cada card é um nó. Cada ligação possível é uma aresta.

Exemplo:
intro -> porta
intro -> mesa
intro -> janela

Estado:
Memória temporária da experiência. Guarda escolhas, progresso, flags, score, itens encontrados, cards visitados etc.

Exemplo:
hasKey = true
visitedMesa = true
score = 20

Com grafo + estado, a navegação muda conforme o que a pessoa fez:

se hasKey = true:
  porta -> sala_secreta

se hasKey = false:
  porta -> porta_trancada

### Isso permite
- caminhos alternativos
- voltar sem ser só “previous”
- atalhos
- cenas desbloqueáveis
- cards repetidos com resultado diferente
- finais múltiplos
- missões simples
- esconder/mostrar opções
- mudar texto/imagem conforme escolhas anteriores
- navegação com elementos de game, sem precisar virar um game completo

### Níveis possíveis
1. Linear com atalhos
2. Grafo narrativo
3. Grafo com estado

### Estética/animação

Queremos mirar em algo compatível com:
- Liquid Glass: transparência, blur, refração, highlights, fluidez, sensação de material vivo.
- Material 3 / Pixel Expressive: cores dinâmicas, componentes expressivos, motion fluido, UI glanceable, personalização.

**Estado da arte técnico**

- UI e layout: HTML/CSS/React
- animações de interface: CSS transforms, Web Animations API, Motion/Framer Motion ou Motion One
- timelines complexas: GSAP
- animações vetoriais: Rive ou Lottie
- efeitos avançados/glass/3D: Three.js/WebGL
- WebGPU pode ser futuro, mas não precisa ser base inicial

## Princípio geral

O modelo deve ser independente de CMS, AMP e renderer final.

O CMS proprietário deve ser só uma camada de persistência/publicação/assets, via callbacks/API.

O runtime deve ser web-first, interativo, embutível, expansível e exportável.

## Frase de arquitetura

Vamos criar um formato web-first de experiências em cards: editor salva um modelo rico independente; renderer DOM/React monta as cenas; player com grafo+estado controla navegação; Canvas/WebGL entram como camadas opcionais para efeitos avançados; export/embed são saídas adicionais, incluindo vídeo para social.